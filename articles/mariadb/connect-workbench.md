---
title: Se connecter à Azure Database for MariaDB depuis MySQL Workbench
description: Ce guide de démarrage rapide décrit les étapes à suivre dans l’utilisation de MySQL Workbench pour se connecter et interroger des données à partir d’Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: b3a4d00381361b5299e86b959d9775318ae81e88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998229"
---
# <a name="azure-database-for-mariadb-use-mysql-workbench-to-connect-and-query-data"></a>Azure Database for MariaDB : Utiliser MySQL Workbench pour se connecter et interroger des données
Ce guide de démarrage rapide explique comment se connecter à une instance d’Azure Database for MariaDB en utilisant l’application MySQL Workbench. 

## <a name="prerequisites"></a>Prérequis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer un serveur Azure Database for MariaDB à l’aide du Portail Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Créer un serveur Azure Database for MariaDB à l’aide d’Azure CLI](./quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Installer MySQL Workbench
Téléchargez et installez MySQL Workbench sur votre ordinateur depuis le [site web MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Procurez-vous les informations de connexion nécessaires pour se connecter à l’instance Azure Database for MariaDB. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mydemoserver**.

3. Cliquez sur le nom du serveur.

4. Dans le panneau **Vue d’ensemble** du serveur, notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**. Si vous oubliez votre mot de passe, vous pouvez également le réinitialiser dans ce panneau.
 ![Nom du serveur Azure Database for MariaDB](./media/connect-workbench/1_server-overview-name-login.png)

## <a name="connect-to-server-using-mysql-workbench"></a>Se connecter au serveur à l’aide de MySQL Workbench 
Pour se connecter à un serveur Azure Database for MariaDB avec MySQL Workbench, suivez ces étapes :

1.  Lancez l’application MySQL Workbench sur votre ordinateur. 

2.  Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations suivantes dans l’onglet **Paramètres** :

    ![configurer une nouvelle connexion](./media/connect-workbench/2-setup-new-connection.png)

    | **Paramètre** | **Valeur suggérée** | **Description du champ** |
    |---|---|---|
    |   Nom de connexion | Connexion démo | Spécifiez une étiquette pour cette connexion. |
    | Méthode de connexion | Standard (TCP/IP) | Standard (TCP/IP) est suffisant. |
    | Nom d’hôte | *nom du serveur* | Spécifiez la valeur de nom de serveur utilisée lorsque vous avez créé l’instance Azure Database for MariaDB. Le serveur que nous utilisons dans notre exemple est mydemoserver.mariadb.database.azure.com. Utilisez le nom de domaine complet (\*.mariadb.database.azure.com), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion.  |
    | Port | 3306 | Utilisez toujours le port 3306 lorsque vous vous connectez à Azure Database for MariaDB. |
    | Nom d’utilisateur |  *nom de connexion d’administrateur du serveur* | Tapez le nom d’utilisateur de connexion de l’administrateur du serveur fourni lorsque vous avez créé l’instance Azure Database for MariaDB. Le nom d’utilisateur dans notre exemple est myadmin@mydemoserver. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion. Le format correct est *username@servername*.
    | Mot de passe | votre mot de passe | Cliquez sur le bouton **Stocker dans le coffre-fort…** pour enregistrer le mot de passe. |

3.   Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés. 

4.   Cliquez sur **OK** pour enregistrer la connexion. 

5.   Dans la liste de **connexions MySQL**, cliquez sur le nom correspondant à votre serveur puis patientez jusqu’à ce que la connexion soit établie.

        Un nouvel onglet SQL s’ouvre avec un éditeur vide où vous pouvez saisir vos requêtes.
    
        > [!NOTE]
        > Par défaut, la sécurité de la connexion SSL est exigée et appliquée sur votre serveur Azure Database for MariaDB. Habituellement, bien qu’aucune configuration supplémentaire avec certificats SSL ne soit requise pour MySQL Workbench afin de vous connecter à votre serveur, nous recommandons de lier la certification d’autorité de certification SSL à MySQL Workbench. Si vous souhaitez désactiver le protocole SSL, visitez le portail Azure et cliquez sur la page Sécurité de la connexion pour désactiver le bouton bascule Appliquer une connexion SSL.

## <a name="create-table-insert-read-update-and-delete-data"></a>Créer une table, insérer, lire, mettre à jour et supprimer des données
1. Copiez et collez l’exemple de code SQL dans un onglet SQL vide pour illustrer des exemples de données.

    Ce code crée une base de données vide nommée quickstartdb, puis crée un exemple de table nommée inventory. Il insère des lignes, puis lit les lignes. Il modifie les données avec une instruction de mise à jour et lit de nouveau les lignes. Enfin, il supprime une ligne puis lit de nouveau les lignes.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    La capture d’écran montre un exemple de code SQL dans SQL Workbench et la sortie obtenue après son exécution.
    
    ![Onglet Workbench MySQL de SQL pour exécuter l’exemple de code SQL](media/connect-workbench/3-workbench-sql-tab.png)

2. Pour exécuter l’exemple de Code SQL, cliquez sur l’icône d’éclair dans la barre d’outils de l’onglet**Fichier SQL**.
3. Vous observerez trois onglets de résultats dans la section **Grille de résultats** au milieu de la page. 
4. La liste **Sortie** apparaît en bas de la page. L’état de chaque commande s’affiche. 

Vous êtes à présent connecté à Azure Database for MariaDB à l’aide de MySQL Workbench et avez interrogé des données en utilisant le langage SQL.

<!--
## Next steps
> [!div class="nextstepaction"]
> [Migrate your database using Export and Import](./concepts-migrate-import-export.md)
-->