---
title: Copier de façon incrémentielle plusieurs tables en utilisant Azure Data Factory | Microsoft Docs
description: Dans ce tutoriel, vous allez créer un pipeline Azure Data Factory qui copie de façon incrémentielle des données delta de plusieurs tables d’une base de données SQL Server locale dans une base de données Azure SQL.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: 44ae433040c2c9cab47567cb663d4e588311a4a1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177413"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Charger de façon incrémentielle des données provenant de plusieurs tables de SQL Server vers une base de données Azure SQL
Dans ce tutoriel, vous allez créer une fabrique de données Azure Data Factory avec un pipeline qui charge les données delta de plusieurs tables d’une base de données SQL Server locale vers une base de données Azure SQL.    

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Préparer les magasins de données source et de destination.
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Installer le runtime d’intégration. 
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et filigrane.
> * Créez, exécutez et surveillez un pipeline.
> * Passez en revue les résultats.
> * Ajouter ou mettre à jour des données dans les tables source.
> * Réexécuter et surveiller le pipeline.
> * Passer en revue les résultats finaux.

## <a name="overview"></a>Vue d’ensemble
Voici les étapes importantes à suivre pour créer cette solution : 

1. **Sélectionner la colonne de limite**.
    
    Sélectionnez une colonne pour chaque table du magasin de données sources, qui peut servir à identifier les enregistrements nouveaux ou mis à jour pour chaque exécution. Normalement, les données contenues dans cette colonne sélectionnée (par exemple, last_modify_time ou ID) continuent de croître à mesure que des lignes sont créées ou mises à jour. La valeur maximale de cette colonne est utilisée comme limite.

1. **Préparer un magasin de données pour stocker la valeur de limite**.   
    
    Dans ce tutoriel, la valeur de filigrane est stockée dans une base de données SQL.

1. **Créer un pipeline avec les activités suivantes** : 
    
    a. Créez une activité ForEach qui effectue une itération dans une liste de noms de table source qui est transmise en tant que paramètre au pipeline. Pour chaque table source, elle appelle les activités suivantes pour effectuer le chargement delta pour cette table.

    b. Créez deux activités de recherche. Servez-vous de la première activité de recherche pour récupérer la dernière valeur de filigrane. Utilisez la deuxième activité de recherche pour récupérer la nouvelle valeur de filigrane. Ces valeurs de filigrane sont transmises à l’activité de copie.

    c. Créez une activité de copie qui copie les lignes du magasin de données source dont la valeur de la colonne de filigrane est supérieure à l’ancienne valeur de filigrane et inférieure à la nouvelle. Elle copie ensuite les données delta du magasin de données source dans un stockage Blob Azure sous la forme d’un nouveau fichier.

    d. Créez une activité StoredProcedure qui met à jour la valeur de filigrane pour le pipeline s’exécutant la prochaine fois. 

    Voici le diagramme général de la solution : 

    ![Chargement incrémentiel de données](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis
* **SQL Server**. Dans le cadre de ce tutoriel, vous allez utiliser une base de données SQL Server locale comme magasin de données source. 
* **Azure SQL Database**. Vous allez utiliser une base de données SQL comme magasin de données récepteur. Si vous ne disposez pas d’une base de données SQL, consultez [Créer une base de données Azure SQL Database](../sql-database/sql-database-get-started-portal.md) pour connaître la procédure à suivre pour en créer une. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Créer des tables sources dans votre base de données SQL Server

1. Ouvrez SQL Server Management Studio, puis connectez-vous à votre base de données SQL Server locale.

1. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur la base de données et choisissez **Nouvelle requête**.

1. Exécutez la commande SQL suivante sur votre base de données pour créer des tables nommées `customer_table` et `project_table` :

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Créer des tables de destination dans votre base de données Azure SQL
1. Ouvrez SQL Server Management Studio, puis connectez-vous à votre base de données Azure SQL.

1. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur la base de données et choisissez **Nouvelle requête**.

1. Exécutez la commande SQL suivante sur votre base de données SQL pour créer des tables nommées `customer_table` et `project_table` :  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Créer une autre table dans la base de données Azure SQL pour stocker la valeur de limite supérieure
1. Exécutez la commande SQL suivante sur votre base de données SQL pour créer une table sous le nom `watermarktable` pour stocker la valeur de filigrane : 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Insérer des valeurs de limite supérieure initiale pour les deux tables source dans la table de limite supérieure.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Créer une procédure stockée dans la base de données Azure SQL 

Exécutez la commande suivante pour créer une procédure stockée dans votre base de données SQL. Cette procédure stockée met à jour la valeur de filigrane après chaque exécution du pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Créer des types de données et des procédures stockées supplémentaires dans la base de données Azure SQL
Exécutez la requête suivante pour créer deux procédures stockées et deux types de données dans votre base de données SQL. Ils sont utilisés pour fusionner les données des tables source dans les tables de destination.

Afin de faciliter le démarrage du parcours, nous utilisons directement ces procédures stockées en transmettant les données delta par l’intermédiaire d’une variable de table, puis nous les fusionnons dans le magasin de destination. Faites attention qu’il ne s’attende pas à ce qu’un nombre « élevé » de lignes delta (plus de 100) soient stockées dans la variable de table.  

Si vous n’avez pas besoin de fusionner un grand nombre de lignes delta dans le magasin de destination, nous vous suggérons de d’abord utiliser l’activité de copie pour copier toutes les données delta dans une table de « mise en lots » temporaire dans le magasin de destination, puis de générer votre propre procédure stockée sans utiliser la variable de table pour les fusionner de la table de « mise en lots » dans la table « finale ». 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
1. Dans la page **Nouvelle fabrique de données**, entrez **ADFMultiIncCopyTutorialDF** dans le champ **Nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomADFMultiIncCopyTutorialDF), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
1. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
1. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
        Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
1. Sélectionnez **V2 (préversion)** pour la **version**.
1. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent être proposés dans d’autres régions.
1. Sélectionnez **Épingler au tableau de bord**.     
1. Cliquez sur **Créer**.      
1. Sur le tableau de bord, vous voyez la vignette suivante avec l’état : **Déploiement de Data Factory**. 

    ![mosaïque déploiement de fabrique de données](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
1. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d’accueil Data Factory](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
1. Cliquez sur la vignette **Créer et surveiller** pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet distinct.
1. Dans la page de prise en main de l’interface utilisateur d’Azure Data Factory, cliquez sur **Create pipeline (Créer un pipeline)** ou passez dans l’onglet **Modifier**. 

   ![Page de prise en main](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Créer un runtime d’intégration auto-hébergé
Lorsque vous déplacez des données d’un magasin de données d’un réseau privé (local) à un magasin de données Azure, installez un runtime d’intégration (IR) auto-hébergé dans votre environnement local. Le runtime d’intégration auto-hébergé déplace les données entre votre réseau privé et Azure. 

1. En bas du volet gauche, cliquez sur **Connexions**, puis sélectionnez **Runtimes d’intégration** dans la fenêtre **Connexions**. 

   ![Onglet Connexions](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
1. Dans l’onglet **Runtimes d’intégration**, cliquez sur **+ Nouveau**. 

   ![Nouveau runtime d’intégration : bouton](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
1. Dans la fenêtre **Installation du runtime d’intégration**, sélectionnez **Perform data movement and dispatch activities to external computes** (Effectuer des activités de déplacement et de distribution des données vers des ressources de calcul externes), puis cliquez sur **Suivant**. 

   ![Sélectionner un type de runtime d’intégration](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
1. Sélectionnez **Réseau privé**, puis cliquez sur **Suivant**. 

   ![Sélectionner un réseau privé](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
1. Entrez **MySelfHostedIR** pour le **Nom**, puis cliquez sur **Suivant**. 

   ![Nom de runtime d’intégration auto-hébergé](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
1. Cliquez sur **Cliquez ici pour lancer l’installation rapide pour cet ordinateur** dans la section **Option 1 : installation rapide**. 

   ![Cliquer sur le lien d’installation rapide](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. Dans la fenêtre **Installation rapide du runtime d’intégration (auto-hébergé)** , cliquez sur **Fermer**. 

   ![Installation du runtime d’intégration - réussie](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. Dans la fenêtre **Installation du runtime d’intégration**, cliquez sur **Terminer**. 

   ![Installation du runtime d’intégration - terminée](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
1. Vérifiez que **MySelfHostedIR** figure dans la liste des runtimes d’intégration.

    ![Runtimes d’intégration : liste](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Créez des services liés
Vous allez créer des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans cette section, vous allez créer des services liés à vos bases de données SQL Server et SQL locales. 

### <a name="create-the-sql-server-linked-service"></a>Créer le service lié SQL Server
Dans cette étape, vous liez votre base de données SQL Server locale à la fabrique de données.

1. Dans la fenêtre **Connexions**, passez de l’onglet **Runtimes d’intégration** à l’onglet **Services liés**, puis cliquez sur **+ Nouveau**.

    ![Bouton Nouveau service lié](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. Dans la fenêtre **Nouveau service lié**, sélectionnez **SQL Server**, puis cliquez sur **Continuer**. 

    ![Sélectionner SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
1. Dans la fenêtre **Nouveau service lié**, procédez comme suit :

    1. Entrez **SqlServerLinkedService** comme **nom**. 
    1. Sélectionnez **MySelfHostedIR** pour **se connecter via le runtime d’intégration**. Il s’agit d’une étape **importante**. Le runtime d’intégration par défaut ne peut pas se connecter à un magasin de données local. Utilisez le runtime d’intégration auto-hébergé que vous avez créé précédemment. 
    1. Dans le champ **Nom du serveur**, entrez le nom de l’ordinateur sur lequel réside la base de données SQL Server.
    1. Dans le champ **Nom de la base de données**, entrez le nom de la base de données de votre serveur SQL Server qui contient les données sources. Vous avez créé une table et inséré des données dans cette base de données dans le cadre des conditions préalables. 
    1. Dans le champ **Type d’authentification**, sélectionnez le **type d’authentification** à utiliser pour vous connecter à la base de données. 
    1. Dans le champ **Nom d’utilisateur**, entrez le nom de l’utilisateur qui a accès à la base de données SQL Server. Si vous avez besoin d’utiliser une barre oblique (`\`) dans votre nom de serveur ou de compte d’utilisateur, utilisez le caractère d’échappement (`\`). Par exemple `mydomain\\myuser`.
    1. Dans le champ **Mot de passe**, entrez le **mot de passe** de l’utilisateur. 
    1. Pour vérifier si Data Factory peut se connecter à votre base de données SQL Server, cliquez sur **Tester la connexion**. Corrigez les erreurs jusqu’à ce que la connexion soit établie. 
    1. Pour enregistrer le service lié, cliquez sur **Enregistrer**.

        ![Service lié SQL Server - paramètres](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Créer le service lié Azure SQL Database
Dans cette dernière étape, vous créez un service lié qui relie votre base de données SQL Server source à la fabrique de données. Dans cette étape, vous liez votre base de données Azure SQL de destination/récepteur à la fabrique de données. 

1. Dans la fenêtre **Connexions**, passez de l’onglet **Runtimes d’intégration** à l’onglet **Services liés**, puis cliquez sur **+ Nouveau**.

    ![Bouton Nouveau service lié](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. Dans la fenêtre **Nouveau service lié**, sélectionnez **Azure SQL Database**, puis cliquez sur **Continuer**. 
1. Dans la fenêtre **Nouveau service lié**, procédez comme suit :

    1. Entrez **AzureSqlDatabaseLinkedService** pour **Nom**. 
    1. Dans le champ **Nom du serveur**, sélectionnez le nom de votre serveur SQL Azure dans la liste déroulante. 
    1. Dans le champ **Nom de la base de données**, sélectionnez la base de données Azure SQL dans laquelle vous avez créé les tables customer_table et project_table dans le cadre des prérequis 
    1. Dans le champ **Nom d’utilisateur**, entrez le nom de l’utilisateur qui a accès à la base de données Azure SQL. 
    1. Dans le champ **Mot de passe**, entrez le **mot de passe** de l’utilisateur. 
    1. Pour vérifier si Data Factory peut se connecter à votre base de données SQL Server, cliquez sur **Tester la connexion**. Corrigez les erreurs jusqu’à ce que la connexion soit établie. 
    1. Pour enregistrer le service lié, cliquez sur **Enregistrer**.

        ![Service lié SQL Azure : paramètres](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
1. Vérifiez que la liste contient deux services liés. 
   
    ![Deux services liés](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Créez les jeux de données
Dans cette étape, vous créez des jeux de données pour représenter la source de données, la destination des données et l’emplacement de stockage du filigrane.

### <a name="create-a-source-dataset"></a>Créer un jeu de données source

1. Dans le volet gauche, cliquez sur **+ (plus)** , puis sur **Jeu de données**.

   ![Menu Nouveau jeu de données](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **SQL Server**, puis cliquez sur **Terminer**. 

   ![Sélectionner SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
1. Un nouvel onglet est ouvert dans le navigateur web et vous permet de configurer le jeu de données. Un jeu de données est également affiché dans l’arborescence. Dans l’onglet **Général** de la fenêtre Propriétés située dans la partie inférieure, entrez **SourceDataset** dans le champ **Nom**. 

   ![Jeu de données source - nom](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
1. Passez dans l’onglet **Connexion** de la fenêtre Propriétés, puis sélectionnez **SqlServerLinkedService** dans la liste déroulante **Service lié**. Vous ne sélectionnez pas une table ici. L’activité de copie dans le pipeline utilise une requête SQL pour charger les données plutôt que de charger l’ensemble de la table.

   ![Jeu de données source : connexion](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Créer un jeu de données récepteur
1. Dans le volet gauche, cliquez sur **+ (plus)** , puis sur **Jeu de données**.

   ![Menu Nouveau jeu de données](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Azure SQL Database**, puis cliquez sur **Terminer**. 

   ![Sélectionner Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Un nouvel onglet est ouvert dans le navigateur web et vous permet de configurer le jeu de données. Un jeu de données est également affiché dans l’arborescence. Dans l’onglet **Général** de la fenêtre Propriétés située dans la partie inférieure, entrez **SinkDataset** dans le champ **Nom**.

   ![Jeu de données récepteur : général](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
1. Passez dans l’onglet **Paramètres** de la fenêtre Propriétés, et procédez comme suit : 

    1. Cliquez sur **Nouveau** dans la section **Créer/Mettre à jour des paramètres**. 
    1. Entrez **SinkTableName** dans le champ **Nom** et **Chaîne** dans le champ **Type**. Ce jeu de données utilise **SinkTableName** comme paramètre. Le paramètre SinkTableName est défini par le pipeline de manière dynamique lors de l’exécution. L’activité ForEach du pipeline effectue une itération dans une liste de noms de table et transmet le nom de table à ce jeu de données à chaque itération.
   
       ![Jeu de données récepteur : propriétés](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Passez dans l’onglet **Connexion** de la fenêtre Propriétés, puis sélectionnez **AzureSqlLinkedService** dans la liste déroulante **Service lié**. Pour la propriété **Table**, cliquez sur **Ajouter du contenu dynamique**. 

   ![Jeu de données récepteur : connexion](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
1. Sélectionnez **SinkTableName** dans la section **Paramètres**
   
   ![Jeu de données récepteur : connexion](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 1. Après avoir cliqué sur **Terminer**, vous verrez **\@dataset().SinkTableName** comme nom de table.
   
   ![Jeu de données récepteur : connexion](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Créer un jeu de données pour un filigrane
Dans cette étape, vous allez créer un jeu de données pour stocker une valeur de limite supérieure. 

1. Dans le volet gauche, cliquez sur **+ (plus)** , puis sur **Jeu de données**.

   ![Menu Nouveau jeu de données](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Azure SQL Database**, puis cliquez sur **Terminer**. 

   ![Sélectionner Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Dans l’onglet **Général** de la fenêtre Propriétés située dans la partie inférieure, entrez **WatermarkDataset** comme **nom**.
1. Basculez vers l’onglet **Connexions**, et procédez comme suit : 

    1. Sélectionnez **AzureSqlDatabaseLinkedService** pour **Service lié**.
    1. Sélectionnez **[dbo].[ watermarktable]** comme **Table**.

       ![Jeu de données de filigrane : connexion](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Créer un pipeline
Ce pipeline prend une liste de noms de tables comme paramètre. L’activité ForEach effectue une itération dans la liste de noms de table et effectue les opérations suivantes : 

1. Utilisez l’activité de recherche pour récupérer l’ancienne valeur de filigrane (valeur initiale ou valeur utilisée dans la dernière itération).

1. Utilisez l’activité de recherche pour récupérer la nouvelle valeur de filigrane (valeur maximale de la colonne de filigrane de la table source).

1. Utilisez l’activité de copie pour copier des données entre ces deux valeurs de filigrane depuis la base de données source vers la base de données de destination.

1. Utilisez l’activité StoredProcedure pour mettre à jour l’ancienne valeur de filigrane à utiliser au cours de la première étape de l’itération suivante. 

### <a name="create-the-pipeline"></a>Créer le pipeline

1. Dans le volet gauche, cliquez sur **+ (plus)** , puis cliquez sur **Pipeline**.

    ![Nouveau pipeline : menu](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
1. Dans l’onglet **Général** de la fenêtre **Propriétés**, entrez **IncrementalCopyPipeline** comme **nom**. 

    ![Nom du pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
1. Dans la fenêtre **Propriétés**, procédez comme suit : 

    1. Cliquez sur **+ Nouveau**. 
    1. Entrez **tableList** pour le **nom** du paramètre. 
    1. Sélectionnez **Objet** pour le **type** de paramètre.

    ![Paramètres du pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
1. Dans la boîte à outils **Activités**, développez **Iteration & Conditionals** (Itération et conditions), puis faites glisser et déposez l’activité **ForEach** sur la surface du concepteur de pipeline. Dans l’onglet **Général** de la fenêtre **Propriétés**, entrez **IterateSQLTables**. 

    ![Activité ForEach : nom](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
1. Passez dans l’onglet **Paramètres** de la fenêtre **Propriétés**, puis entrez `@pipeline().parameters.tableList` dans le champ **Éléments**. L’activité ForEach parcourt une liste de tables et effectue l’opération de copie incrémentielle. 

    ![Activité ForEach : paramètres](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
1. Sélectionnez l’activité **ForEach** dans le pipeline si elle n’est pas déjà sélectionnée. Cliquez sur le bouton **Modifier (icône Crayon)** .

    ![Activité ForEach : modification](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
1. Dans la boîte à outils **Activités**, développez **Général** puis faites glisser et déposez l’activité **Recherche** sur la surface du concepteur de pipeline. Ensuite, saisissez **LookupOldWaterMarkActivity** dans **Nom**.

    ![Première activité de recherche : nom](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
1. Passez dans l’onglet **Paramètres** de la fenêtre **Propriétés**, puis procédez comme suit : 

    1. Sélectionnez **WatermarkDataset** dans le champ **Jeu de données source**.
    1. Sélectionnez **Requête** pour **Utiliser la requête**. 
    1. Entrez la requête SQL suivante pour **Requête**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Première activité de recherche : paramètres](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Glissez-déposez l’activité **Recherche** à partir de la boîte à outils **Activités**, puis entrez **LookupNewWaterMarkActivity** dans le champ **Nom**.
        
    ![Seconde activité de recherche : nom](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
1. Basculez vers l’onglet **Paramètres** .

    1. Sélectionnez **SourceDataset** pour **Jeu de données source**. 
    1. Sélectionnez **Requête** pour **Utiliser la requête**.
    1. Entrez la requête SQL suivante pour **Requête**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Seconde activité de recherche : paramètres](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Glissez-déposez l’activité **Copie** à partir de la boîte à outils **Activités**, puis entrez **IncrementalCopyActivity** dans le champ **Nom**. 

    ![Activité de copie - nom](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
1. Connectez une à une les activités **Recherche** à l’activité **Copie**. Pour ce faire, faites glisser la case **verte** attachée à l’activité **Recherche**, puis déposez-la sur l’activité **Copie**. Relâchez le bouton de la souris lorsque la couleur de bordure de l’activité Copie devient **bleue**.

    ![Connecter des activités Recherche à l’activité Copie](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Sélectionnez l’activité **Copie** dans le pipeline. Passez dans l’onglet **Source** de la fenêtre **Propriétés**. 

    1. Sélectionnez **SourceDataset** pour **Jeu de données source**. 
    1. Sélectionnez **Requête** pour **Utiliser la requête**. 
    1. Saisissez la requête SQL suivante pour **Requête**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Activité de copie - paramètres de la source](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Passez dans l’onglet **Récepteur**, puis sélectionnez **SinkDataset** dans le champ **Jeu de données récepteur**. 
        
    ![Activité de copie - paramètres du récepteur](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
1. Effectuez également les étapes suivantes :

    1. Dans la propriété **Jeu de données**, pour le paramètre **SinkTableName**, entrez `@{item().TABLE_NAME}`.
    1. Pour la propriété **Nom de procédure stockée**, entrez `@{item().StoredProcedureNameForMergeOperation}`.
    1. Pour la propriété **Type de table**, entrez `@{item().TableType}`.


        ![Activité Copie : paramètres](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Glissez-déposez l’activité **Procédure stockée** de la boîte à outils **Activités** vers la surface du concepteur de pipeline. Connectez l’activité **Copie** à l’activité **Procédure stockée**. 

    ![Activité Copie : paramètres](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
1. Sélectionnez l’activité **Procédure stockée** dans le pipeline, puis entrez **StoredProceduretoWriteWatermarkActivity** dans le champ **Nom** de l’onglet **Général** de la fenêtre **Propriétés**. 

    ![Activité de procédure stockée - nom](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
1. Passez dans l’onglet **Compte SQL** et sélectionnez **AzureSqlDatabaseLinkedService** dans la liste déroulante **Service lié**.

    ![Activité de procédure stockée - Compte SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Basculez vers l’onglet **Procédure stockée**, et procédez comme suit :

    1. Pour **Nom de la procédure stockée**, sélectionnez `usp_write_watermark`. 
    1. Sélectionnez **Import parameter** (Paramètre d’importation). 
    1. Indiquez les valeurs suivantes pour les paramètres : 

        | Nom | type | Valeur | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Chaîne | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Activité de procédure stockée- paramètres de procédure stockée](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Dans le volet gauche, cliquez sur **Publier**. Cette action publie les entités que vous avez créées pour le service Data Factory. 

    ![Bouton Publier](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
1. Patientez jusqu’à voir le message **Publication réussie**. Pour afficher les notifications, cliquez sur le lien **Afficher les notifications**. Fermez la fenêtre de notifications en cliquant sur le **X**.

    ![Afficher les notifications](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Exécuter le pipeline

1. Dans la barre d’outils du pipeline, cliquez sur **Déclencheur**, puis sur **Déclencher maintenant**.     

    ![Déclencher maintenant](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Dans la fenêtre **Pipeline Run** (Exécution du pipeline), entrez la valeur suivante pour le paramètre **tableList**, puis cliquez sur **Terminer**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Arguments d’exécution de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Vous observez l’exécution du pipeline activée par le **déclencheur manuel**. Cliquez sur le bouton **Actualiser** pour actualiser la liste. Les liens de la colonne **Action** vous permettent de visualiser les exécutions d’activités associées à l’exécution du pipeline et de réexécuter le pipeline. 

    ![Exécutions de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Vous observez toutes les exécutions d’activités associées à l’exécution du pipeline sélectionné. 

    ![Exécutions d’activités](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Passer en revue les résultats.
Dans SQL Server Management Studio, exécutez les requêtes suivantes sur la base de données SQL cible pour vérifier que les données ont été copiées à partir des tables source vers les tables de destination : 

**Requête** 
```sql
select * from customer_table
```

**Sortie**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Requête**

```sql
select * from project_table
```

**Sortie**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Requête**

```sql
select * from watermarktable
```

**Sortie**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Notez que les valeurs de filigrane des deux tables ont été mises à jour. 

## <a name="add-more-data-to-the-source-tables"></a>Ajouter plus de données aux tables sources

Exécutez la requête suivante sur la base de données SQL Server source pour mettre à jour une ligne existante dans customer_table. Insérez une nouvelle ligne dans project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Exécutez à nouveau le pipeline
1. Dans la fenêtre du navigateur web, passez dans l’onglet **Modifier** sur la gauche. 
1. Dans la barre d’outils du pipeline, cliquez sur **Déclencheur**, puis sur **Déclencher maintenant**.   

    ![Déclencher maintenant](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Dans la fenêtre **Pipeline Run** (Exécution du pipeline), entrez la valeur suivante pour le paramètre **tableList**, puis cliquez sur **Terminer**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Surveiller à nouveau le pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Vous observez l’exécution du pipeline activée par le **déclencheur manuel**. Cliquez sur le bouton **Actualiser** pour actualiser la liste. Les liens de la colonne **Action** vous permettent de visualiser les exécutions d’activités associées à l’exécution du pipeline et de réexécuter le pipeline. 

    ![Exécutions de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Vous observez toutes les exécutions d’activités associées à l’exécution du pipeline sélectionné. 

    ![Exécutions d’activités](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Passer en revue les résultats finaux
Dans SQL Server Management Studio, exécutez les requêtes suivantes sur la base de données cible pour vérifier que les données nouvelles/mises à jour ont été copiées à partir des tables sources vers les tables de destination. 

**Requête** 
```sql
select * from customer_table
```

**Sortie**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Notez les nouvelles valeurs de **Name** et **LastModifytime** pour le **PersonID** du numéro 3. 

**Requête**

```sql
select * from project_table
```

**Sortie**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Notez que l’entrée **NewProject** a été ajoutée à project_table. 

**Requête**

```sql
select * from watermarktable
```

**Sortie**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Notez que les valeurs de filigrane des deux tables ont été mises à jour.
     
## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Préparer les magasins de données source et de destination.
> * Créer une fabrique de données.
> * Créer un runtime d’intégration auto-hébergé (IR).
> * Installer le runtime d’intégration.
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et filigrane.
> * Créez, exécutez et surveillez un pipeline.
> * Passez en revue les résultats.
> * Ajouter ou mettre à jour des données dans les tables source.
> * Réexécuter et surveiller le pipeline.
> * Passer en revue les résultats finaux.

Passez au tutoriel suivant pour en savoir plus sur la transformation des données en utilisant un cluster Spark sur Azure :

> [!div class="nextstepaction"]
>[Charger de façon incrémentielle des données d’Azure SQL Database dans le stockage Blob Azure à l’aide de la technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-portal.md)


