---
title: 'Démarrage rapide : Créer un serveur Azure Database for MariaDB - Portail Azure'
description: Cet article vous guide dans le portail Azure pour créer rapidement un exemple de serveur Azure Database for MariaDB en environ cinq minutes.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: d4de6bdb6ab880a3f7da3cf27e34baa1c0284ec7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409403"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-portal"></a>Créer un serveur Azure Database for MariaDB à l’aide du portail Azure

Azure Database for MariaDB est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MariaDB hautement disponibles dans le cloud. Ce guide de démarrage rapide vous montre comment créer un serveur Azure Database for MariaDB en quelques minutes à l’aide du portail Azure.  

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Ouvrez votre navigateur Web, puis accédez au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-an-azure-database-for-mariadb-server"></a>Créer un serveur Azure Database for MariaDB
Vous créez un serveur Azure Database for MariaDB avec un ensemble défini de ressources de calcul et de stockage<!-- [compute and storage resources](./concepts-compute-unit-and-storage.md)-->. Vous créez ce serveur dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Pour créer un serveur Azure Database for MariaDB, effectuez les étapes suivantes :

1. Cliquez sur le bouton **Créer une ressource** (+) dans le coin supérieur gauche du portail.

2. Tapez **Azure Database for MariaDB** dans la zone de recherche pour localiser le service.

   ![Option Azure Database for MariaDB](./media/quickstart-create-mariadb-server-database-using-azure-portal/2_navigate-to-mariadb.png)

3. Remplissez le formulaire de détails du nouveau serveur avec les informations suivantes :
   
   ![Créer un formulaire de serveur](./media/quickstart-create-mariadb-server-database-using-azure-portal/4-create-form.png)

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    ---|---|---
    Nom du serveur | Nom de serveur unique | Choisissez un nom unique qui identifie votre serveur Azure Database for MariaDB. Par exemple, mydemoserver. Le nom de domaine *.mariadb.database.azure.com* est ajouté au nom de serveur que vous indiquez. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
    Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre serveur. Si vous avez plusieurs abonnements, sélectionnez l’abonnement dans lequel la ressource est facturée.
    Groupe de ressources | *myresourcegroup* | Spécifiez un nom de groupe de ressources nouveau ou existant.    Groupe de ressources|*myresourcegroup*| Un nouveau nom de groupe de ressources ou un nom de groupe existant dans votre abonnement.
    Sélectionner une source | *Vide* | Sélectionnez *Vide* pour créer un nouveau serveur à partir de zéro. (Vous sélectionnez *Sauvegarde* si vous créez un serveur à partir d’une géosauvegarde d’un serveur Azure Database for MariaDB existant).
    Connexion d’administrateur serveur | myadmin | Un compte de connexion à utiliser lors de la connexion au serveur. Le nom de connexion d’administrateur ne doit pas être **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.
    Mot de passe | *Votre choix* | Spécifiez un mot de passe pour le compte Administrateur du serveur. Il doit inclure entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères appartenant à trois des catégories suivantes : lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
    Confirmer le mot de passe | *Votre choix*| Confirmez le mot de passe du compte d’administrateur.
    Lieu | *La région la plus proche de vos utilisateurs*| Choisissez l’emplacement le plus proche de vos utilisateurs ou de vos autres applications Azure.
    Version | *La version la plus récente*| La version la plus récente (sauf si vous avez des exigences spécifiques).
    Niveau tarifaire | **Usage général**, **Gen 5**, **2 vCores**, **5 Go**, **7 jours**, **Géoredondant** | Les configurations de calcul, de stockage et de sauvegarde pour votre nouveau serveur. Sélectionnez **Niveau tarifaire**. Ensuite, sélectionnez l’onglet **Usage général**. *Gen 5*, *2 vCores*, *5 Go* et *7 jours* sont les valeurs par défaut pour **Génération de calcul**, **vCore**, **Stockage** et la **Période de conservation des sauvegardes**. Vous pouvez laisser ces curseurs en l’état. Pour activer les sauvegardes de votre serveur dans le stockage géo-redondant, sélectionnez **Géographiquement redondant** dans les **Options de redondance de sauvegarde**. Pour enregistrer cette sélection du niveau tarifaire, sélectionnez **OK**. La capture d’écran suivante capture ces sélections.
  
    > [!IMPORTANT]
    > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.
    > 

   ![Créer un serveur - Fenêtre de niveau tarifaire](./media/quickstart-create-mariadb-server-database-using-azure-portal/3-pricing-tier.png)

4.  Sélectionnez **Créer** pour approvisionner le serveur. L’approvisionnement peut durer jusqu’à 20 minutes.
   
5.  Dans la barre d’outils, sélectionnez **Notifications** (icône de cloche) pour surveiller le processus de déploiement.
   
  Par défaut, les bases de données suivantes sont créées sous votre serveur : **information_schema**, **mysql**, **performance_schema**, et **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Azure Database for MariaDB crée un pare-feu au niveau du serveur. Il empêche les applications et les outils externes de se connecter au serveur et à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. 

1.   Après le déploiement, localisez votre serveur. Si nécessaire, vous pouvez le rechercher. Par exemple, sélectionnez **Toutes les ressources** dans le menu de gauche. Tapez ensuite le nom du serveur, tel que **mydemoserver**, pour rechercher le serveur que vous venez de créer. Sélectionnez le nom du serveur dans la liste des résultats. La page **Présentation** correspondant à votre serveur s’ouvre et propose des options pour poursuivre la configuration de la page.

2. Sur la page du serveur, sélectionnez **Sécurité de la connexion**.

3.  Sous le titre **Règles de pare-feu**, sélectionnez la zone de texte vide de la colonne **Nom de la règle** pour commencer à créer la règle de pare-feu. Spécifiez la plage exacte d’adresses IP des clients qui se connecteront à ce serveur.
   
   ![Sécurité de connexion : règles de pare-feu](./media/quickstart-create-mariadb-server-database-using-azure-portal/5-firewall-2.png)

4. Dans la barre d’outils supérieure de la page **Sécurité de la connexion**, sélectionnez **Enregistrer**. Avant de continuer, attendez la notification indiquant que la mise à jour a été faite avec succès. 

   > [!NOTE]
   > Les connexions à Azure Database for MariaDB communiquent sur le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.
   > 

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion
Pour vous connecter à votre serveur de base de données, il vous faut le nom de serveur complet et les informations d’identification de connexion d’administrateur. Il est possible que vous ayez noté ces valeurs précédemment dans l’article relatif au guide de démarrage rapide. Si vous ne l’avez pas fait, vous pouvez facilement localiser le nom du serveur et les informations de connexion sur la page **Vue d’ensemble** ou sur la page **Propriétés** du serveur sur le portail Azure.

Pour trouver ces valeurs, effectuez les étapes suivantes : 

1. Ouvrez la page **Vue d’ensemble** de votre serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**. 

2. Placez le curseur sur chaque champ afin de faire apparaître l’icône de copie à droite du texte. Sélectionnez l’icône de copie appropriée pour copier les valeurs qui vous intéressent.

Dans cet exemple, le nom du serveur est **mydemoserver.mariadb.database.azure.com**, et la connexion de l’administrateur du serveur est **myadmin@mydemoserver**.

## <a name="connect-to-mariadb-using-mysql-command-line"></a>Se connecter à MariaDB avec la ligne de commande mysql
Vous pouvez utiliser différentes applications pour vous connecter à votre serveur Azure Database for MariaDB.

Commençons par utiliser l’outil de ligne de commande [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) pour illustrer la procédure de connexion au serveur. Vous pouvez aussi utiliser un navigateur web et Azure Cloud Shell comme décrit dans cet article sans avoir besoin d’installer d’autres logiciels. Si vous avez installé l’utilitaire mysql en local, vous pouvez également vous y connecter depuis cet emplacement.

1. Lancez Azure Cloud Shell via l’icône de la console (**>_**) située dans le coin supérieur droit du portail Azure.
![Symbole de terminal Azure Cloud Shell](./media/quickstart-create-mariadb-server-database-using-azure-portal/7-cloud-console.png)

2.  Azure Cloud Shell s’ouvre dans votre navigateur pour vous permettre de taper des commandes de l’interpréteur de commandes bash.

   ![Invite de commandes - Exemple de ligne de commande mysql](./media/quickstart-create-mariadb-server-database-using-azure-portal/8-bash.png)

3. À l’invite Cloud Shell, connectez-vous à votre serveur Azure Database for MariaDB en tapant la ligne de commande mysql.

    Pour vous connecter à un serveur Azure Database for MariaDB avec l’utilitaire mysql, utilisez le format suivant :

    ```bash
    mysql --host <fully qualified server name> --user <server admin login name>@<server name> -p
    ```

    Par exemple, la commande ci-après permet de se connecter au serveur de notre exemple :

    ```azurecli-interactive
    mysql --host mydemoserver.mariadb.database.azure.com --user myadmin@mydemoserver -p
    ```

    Paramètre mysql |Valeur suggérée|Description
    ---|---|---
    --host | *Nom du serveur* | Spécifiez la valeur de nom de serveur utilisée quand vous avez créé le serveur Azure Database for MariaDB. Le serveur que nous utilisons dans notre exemple est **mydemoserver.mariadb.database.azure.com**. Utilisez le nom de domaine complet (**\*.mariadb.database.azure.com**), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion.
    --user | *Nom de connexion d’administrateur du serveur* |Il s’agit du nom d’utilisateur de connexion de l’administrateur du serveur fourni quand vous avez créé le serveur Azure Database for MariaDB. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion. Le format correct est *username@servername*.
    -p | *Patienter jusqu’à être invité* |Lorsque vous y êtes invité, spécifiez le mot de passe que vous avez fourni lorsque vous avez créé le serveur. Notez que les caractères du mot de passe que vous tapez ne sont pas visibles au niveau de l’invite bash. Après avoir entré le mot de passe, sélectionnez **Entrer**.

   Une fois connecté, l’utilitaire mysql affiche une invite `mysql>` dans laquelle vous pouvez taper des commandes. 

   Voici un exemple de sortie mysql :

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.39.0 MariaDB Server
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Si le pare-feu n’est pas configuré pour autoriser l’adresse IP d’Azure Cloud Shell, l’erreur suivante se produit :
    >
    > ERROR 2003 (28000) : Le client avec l’adresse IP 123.456.789.0 n’est pas autorisé à accéder au serveur.
    >
    > Pour résoudre l’erreur, veillez à ce que la configuration du serveur corresponde à celle détaillée dans la section « Configurer une règle de pare-feu au niveau du serveur » de l’article.

4. Pour s’assurer que la connexion est opérationnelle, affichez le statut du serveur en tapant `status` dans l’invite mysql.

    ```sql
    status
    ```

   > [!TIP]
   > Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.7 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Créez une base de données vide à l’invite **mysql** en tapant la commande suivante :
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    La commande peut prendre quelques instants. 

    Dans un serveur Azure Database for MariaDB, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Il n’existe aucune limite au nombre de bases de données que vous pouvez créer, mais plusieurs bases de données partagent les mêmes ressources de serveur. 

6. Répertoriez les bases de données à l’invite **mysql** en tapant la commande suivante :

    ```sql
    SHOW DATABASES;
    ```

7.  Tapez `\q`, puis sélectionnez la clé **Entrer** pour quitter l’outil mysql. Une fois terminé, vous pouvez fermer Azure Cloud Shell.

Vous venez de vous connecter au serveur Azure Database for MariaDB et de créer une base de données utilisateur vide. Passez à la section suivante pour effectuer un exercice similaire. L’exercice suivant montre comment se connecter au même serveur à l’aide d’un autre outil commun, MySQL Workbench.

## <a name="connect-to-server-using-mysql-workbench"></a>Se connecter au serveur à l’aide de MySQL Workbench
Pour vous connecter au serveur à l’aide de MySQL Workbench, effectuez les étapes suivantes :

1. Ouvrez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir de la page [Télécharger MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

2. Créez une nouvelle connexion. Cliquez sur le signe plus (+) à côté du titre **Connexions MySQL**.

3. Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations de votre connexion au serveur dans l’onglet **Paramètres**. Les valeurs d’espace réservé sont affichées à titre d’exemple. Remplacez le nom d’hôte, le nom d’utilisateur et le mot de passe avec vos propres valeurs.

   ![Configurer une nouvelle connexion](./media/quickstart-create-mariadb-server-database-using-azure-portal/setup-new-connection.png)

    |Paramètre |Valeur suggérée|Description du champ|
    |---|---|---|
     Nom de connexion | Connexion démo | Une étiquette pour cette connexion. |
    Méthode de connexion | Standard (TCP/IP) | Standard (TCP/IP) est suffisant. |
    Nom d’hôte | *Nom du serveur* | Spécifiez la valeur de nom de serveur utilisée quand vous avez créé le serveur Azure Database for MariaDB. Le serveur que nous utilisons dans notre exemple est **mydemoserver.mariadb.database.azure.com**. Utilisez le nom de domaine complet (**\*.mariadb.database.azure.com**), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion.|
     Port | 3306 | Il s’agit du port à utiliser lors de la connexion au serveur Azure Database for MariaDB. |
    Nom d’utilisateur |  *Nom de connexion d’administrateur du serveur* | Il s’agit des informations de connexion de l’administrateur du serveur fournies quand vous avez créé le serveur Azure Database for MariaDB. Le nom d’utilisateur dans notre exemple est **myadmin@mydemoserver**. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion. Le format correct est *username@servername*.
    Mot de passe | *Votre mot de passe* | Sélectionnez le bouton **Stocker dans le coffre-fort…** pour enregistrer le mot de passe. |

4. Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés. Sélectionnez ensuite **OK** pour enregistrer la connexion. 

    > [!NOTE]
    > Le protocole SSL est appliqué par défaut sur votre serveur et requiert une configuration supplémentaire afin de vous connecter avec succès. <!--For more information, see [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md).--> Pour désactiver SSL dans ce guide de démarrage rapide, accédez au portail Azure. Sélectionnez ensuite la page Sécurité de la connexion pour désactiver le bouton **Appliquer le protocole SSL**.

## <a name="clean-up-resources"></a>Supprimer des ressources
Vous disposez de deux moyens de supprimer les ressources que vous avez créées dans le guide de démarrage rapide. Vous pouvez supprimer le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md), qui inclut toutes les ressources du groupe de ressources. Si vous souhaitez conserver les autres ressources, supprimez uniquement la ressource d’un serveur.

> [!TIP]
> Les autres guides de démarrage rapide de cette collection reposent sur ce guide. Si vous souhaitez continuer à utiliser d’autres guides de démarrages rapides, ne nettoyez pas les ressources créées au cours de ce guide. Sinon, procédez comme suit pour supprimer toutes les ressources que vous avez créées avec ce guide de démarrage rapide.
>

Pour supprimer l’intégralité du groupe de ressources, y compris le serveur que vous venez de créer, procédez comme suit :

1.  Localisez votre groupe de ressources dans le portail Azure. Dans le menu de gauche, sélectionnez **Groupes de ressources**, puis le nom du groupe de ressources, **myresourcegroup** dans notre exemple.

2.  Sur la page de votre groupe de ressources, sélectionnez **Supprimer**. Tapez ensuite le nom de votre groupe de ressources, **myresourcegroup** dans notre exemple, dans la zone pour confirmer la suppression, et sélectionnez **Supprimer**.

Pour supprimer uniquement le serveur que vous venez de créer, procédez comme suit :

1.  Localisez votre serveur dans le portail Azure, s’il n’est pas déjà ouvert. Cliquez sur **Toutes les ressources** dans le menu de gauche du portail Azure. Recherchez ensuite le serveur que vous avez créé.

2.  Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**. 

   ![Azure Database for MariaDB : Supprimer le serveur](./media/quickstart-create-mariadb-server-database-using-azure-portal/delete-server.png)

3.  Vérifiez le nom du serveur à supprimer et affichez les bases de données affectées situées sous celui-ci. Tapez votre nom de serveur dans la zone, **mydemoserver** dans notre exemple. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
- [Concevoir votre première base de données Azure Database for MariaDB](./tutorial-design-database-using-portal.md)