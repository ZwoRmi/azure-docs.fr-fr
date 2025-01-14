---
title: Configurer un groupe de basculement pour Azure SQL Database
description: Découvrez comment configurer un groupe de basculement automatique pour une base de données unique Azure SQL Database, un pool élastique et une instance managée à l’aide du portail Azure, de l’interface de ligne de commande Azure et de PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 9206fd264854cd9e5d8e46473dd60b05a3362fdd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71328707"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configurer un groupe de basculement pour Azure SQL Database

Cette rubrique explique comment configurer un [groupe de basculement automatique](sql-database-auto-failover-group.md) pour une base de données unique Azure SQL Database, un pool élastique et une instance managée à l’aide du portail Azure ou de PowerShell. 

## <a name="single-database"></a>Base de données unique
Créez le groupe de basculement et ajoutez-y une base de données en utilisant le portail Azure ou PowerShell.

### <a name="prerequisites"></a>Prérequis

Prenez en compte les prérequis suivants :

- La connexion au serveur et les paramètres de pare-feu pour le serveur secondaire doivent correspondre à ceux de votre serveur principal. 

### <a name="create-failover-group"></a>Créer un groupe de basculement

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Créez votre groupe de basculement et ajoutez-y votre base de données en utilisant le portail Azure.

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure SQL**. Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez la base de données unique que vous souhaitez ajouter au groupe de basculement. 
1. Sélectionnez le nom du serveur sous **Nom du serveur** pour ouvrir les paramètres du serveur.

   ![Ouvrir un serveur pour une base de donnée unique](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis sélectionnez **Ajouter un groupe** pour créer un groupe de basculement. 

    ![Ajouter un nouveau groupe de basculement](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Dans la page **Groupe de basculement**, entrez ou sélectionnez les valeurs requises, puis sélectionnez **Créer**.

   - **Base de données dans le groupe** : Choisissez la base de données que vous souhaitez ajouter à votre groupe de basculement. L’ajout de la base de données au groupe de basculement démarre automatiquement le processus de géoréplication. 
        
    ![Ajouter la base de données SQL au groupe de basculement](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Créez votre groupe de basculement et ajoutez-y votre base de données unique en utilisant PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Test de basculement 

Testez le basculement de votre groupe de basculement à l’aide du portail Azure ou de PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Testez le basculement de votre groupe de basculement à l’aide du portail Azure. 

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez la base de données unique que vous souhaitez ajouter au groupe de basculement. 

   ![Ouvrir un serveur pour une base de donnée unique](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis choisissez le groupe de basculement que vous venez de créer. 
  
   ![Sélectionner le groupe de basculement à partir du portail](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Vérifiez quel est le serveur principal et quel est le serveur secondaire. 
1. Sélectionnez **Basculement** dans le volet des tâches pour faire basculer votre groupe de basculement contenant votre base de données unique. 
1. Sélectionnez **Oui** en réponse à l’avertissement qui signale que les sessions TDS seront déconnectées. 

   ![Basculer votre groupe de basculement contenant votre base de données SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Vérifiez quel est maintenant le serveur principal et quel est le serveur secondaire. Si le basculement a réussi, les deux serveurs doivent avoir échangé leur rôle. 
1. Resélectionnez **Basculer** pour rétablir les serveurs dans leur rôle d’origine. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Testez le basculement de votre groupe de basculement à l’aide de PowerShell.  

Vérifiez le rôle du réplica secondaire : 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Basculez vers le serveur secondaire : 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Rétablir le groupe de basculement sur le serveur principal :

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

## <a name="elastic-pool"></a>Pool élastique
Créez le groupe de basculement et ajoutez-y un pool élastique en utilisant le portail Azure ou PowerShell.  

### <a name="prerequisites"></a>Prérequis

Prenez en compte les prérequis suivants :

- La connexion au serveur et les paramètres de pare-feu pour le serveur secondaire doivent correspondre à ceux de votre serveur principal. 

### <a name="create-the-failover-group"></a>Créer le groupe de basculement 

Créez le groupe de basculement pour votre pool élastique en utilisant le portail Azure ou PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Créez votre groupe de basculement et ajoutez-y votre pool élastique en utilisant le portail Azure.

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez le pool élastique que vous souhaitez ajouter au groupe de basculement. 
1. Dans le volet **Vue d’ensemble**, sélectionnez le nom du serveur sous **Nom du serveur** pour ouvrir les paramètres du serveur.
  
    ![Ouvrir le serveur pour le pool élastique](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis sélectionnez **Ajouter un groupe** pour créer un groupe de basculement. 

    ![Ajouter un nouveau groupe de basculement](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Dans la page **Groupe de basculement**, entrez ou sélectionnez les valeurs requises, puis sélectionnez **Créer**. Vous pouvez soit créer un serveur secondaire, soit sélectionner un serveur secondaire existant. 

1. Sélectionnez **Base de données dans le groupe**, puis choisissez le pool élastique que vous souhaitez ajouter au groupe de basculement. Si aucun pool élastique n’existe sur le serveur secondaire, un avertissement s’affiche pour vous inviter à y en créer un. Sélectionnez l’avertissement, puis cliquez sur **OK** pour créer le pool élastique sur le serveur secondaire. 
        
    ![Ajouter un pool élastique à un groupe de basculement](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Sélectionnez **Sélectionner** pour appliquer les paramètres de votre pool élastique au groupe de basculement, puis sélectionnez **Créer** pour créer votre groupe de basculement. L’ajout du pool élastique au groupe de basculement démarre automatiquement le processus de géoréplication. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Créez votre groupe de basculement et ajoutez-y votre pool élastique en utilisant PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Test de basculement

Testez le basculement de votre pool élastique à l’aide du portail Azure ou de PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Faites basculer votre groupe de basculement sur le serveur secondaire, puis effectuez une restauration automatique en utilisant le portail Azure. 

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez le pool élastique que vous souhaitez ajouter au groupe de basculement. 
1. Dans le volet **Vue d’ensemble**, sélectionnez le nom du serveur sous **Nom du serveur** pour ouvrir les paramètres du serveur.
  
    ![Ouvrir le serveur pour le pool élastique](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis choisissez le groupe de basculement créé dans la section 2. 
  
   ![Sélectionner le groupe de basculement à partir du portail](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Vérifiez quel est le serveur principal et quel est le serveur secondaire. 
1. Sélectionnez **Basculement** dans le volet des tâches pour faire basculer votre groupe de basculement contenant votre pool élastique. 
1. Sélectionnez **Oui** en réponse à l’avertissement qui signale que les sessions TDS seront déconnectées. 

   ![Basculer votre groupe de basculement contenant votre base de données SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Vérifiez quel est le serveur principal et quel est le serveur secondaire. Si le basculement a réussi, les deux serveurs doivent avoir échangé leur rôle. 
1. Sélectionnez **Basculement** à nouveau pour rétablir les paramètres d’origine du groupe de basculement. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Testez le basculement de votre groupe de basculement à l’aide de PowerShell.

Vérifiez le rôle du réplica secondaire : 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Basculez vers le serveur secondaire : 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

## <a name="managed-instance"></a>Instance gérée

Créez un groupe de basculement entre deux instances managées à l’aide du portail Azure ou de PowerShell. 

Vous devrez créer une passerelle pour le réseau virtuel de chaque instance managée, connecter les deux passerelles, puis créer le groupe de basculement.

### <a name="prerequisites"></a>Prérequis
Prenez en compte les prérequis suivants :

- L’instance managée secondaire doit être vide.
- La plage de sous-réseau du réseau virtuel secondaire ne doit pas chevaucher la plage de sous-réseau du réseau virtuel principal. 
- Le classement et le fuseau horaire de l’instance secondaire doivent correspondre à ceux de l’instance principale. 
- Lors de la connexion des deux passerelles, la **clé partagée** doit être la même pour les deux connexions. 

### <a name="create-primary-virtual-network-gateway"></a>Créer une passerelle de réseau virtuel principal 

Créez la passerelle de réseau virtuel principal avec le portail Azure ou PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Créez la passerelle de réseau virtuel principal avec le portail Azure. 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre groupe de ressources et sélectionnez la ressource **Réseau virtuel** pour votre instance managée principale. 
1. Sélectionnez **Sous-réseaux** sous **Paramètres**, puis choisissez d’ajouter un nouveau **Sous-réseau de passerelle**. Laissez les valeurs par défaut. 

   ![Ajouter une passerelle pour l’instance managée principale](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Une fois la passerelle de sous-réseau créée, sélectionnez **Créer une ressource** dans le volet de navigation gauche, puis saisissez `Virtual network gateway` dans la zone de recherche. Sélectionnez la ressource de **Passerelle de réseau virtuel** publiée par **Microsoft**. 

   ![Créer une passerelle de réseau virtuel](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Renseignez les champs requis pour configurer la passerelle de votre instance managée principale. 

   Le tableau suivant montre les valeurs nécessaires pour la passerelle de l’instance managée principale :
 
    | **Champ** | Valeur |
    | --- | --- |
    | **Abonnement** |  L’abonnement dans lequel votre instance managée principale se trouve. |
    | **Nom** | Le nom de votre passerelle de réseau virtuel. | 
    | **Région** | La région dans laquelle votre instance managée secondaire se trouve. |
    | **Type de passerelle** | Sélectionnez **VPN**. |
    | **Type de VPN** | Sélectionnez **Route-based** |
    | **Référence (SKU)**| Laissez la valeur `VpnGw1` par défaut. |
    | **Lieu**| L’emplacement où se trouve votre instance gérée secondaire et votre réseau virtuel secondaire.   |
    | **Réseau virtuel**| Sélectionnez le réseau virtuel pour votre instance managée secondaire. |
    | **Adresse IP publique**| Sélectionnez **Créer nouveau**. |
    | **Nom de l’adresse IP publique**| Entrez un nom pour votre adresse IP. |
    | &nbsp; | &nbsp; |

1. Laissez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer** pour passer en revue les paramètres de votre passerelle de réseau virtuel.

   ![Paramètres de la passerelle principale](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Sélectionnez **Créer** pour créer votre passerelle de réseau virtuel. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Créez la passerelle de réseau virtuel principal à l’aide de PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel secondaire

Créez la passerelle de réseau virtuel secondaire avec le portail Azure ou PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Répétez les étapes de la section précédente pour créer le sous-réseau et la passerelle du réseau virtuel pour l’instance managée secondaire. Renseignez les champs requis pour configurer la passerelle de votre instance managée secondaire. 

   Le tableau suivant montre les valeurs nécessaires pour la passerelle de l’instance managée secondaire :

   | **Champ** | Valeur |
   | --- | --- |
   | **Abonnement** |  L’abonnement dans lequel votre instance managée secondaire se trouve. |
   | **Nom** | Le nom de votre passerelle de réseau virtuel, par exemple `secondary-mi-gateway`. | 
   | **Région** | La région dans laquelle votre instance managée secondaire se trouve. |
   | **Type de passerelle** | Sélectionnez **VPN**. |
   | **Type de VPN** | Sélectionnez **Route-based** |
   | **Référence (SKU)**| Laissez la valeur `VpnGw1` par défaut. |
   | **Lieu**| L’emplacement où se trouve votre instance gérée secondaire et votre réseau virtuel secondaire.   |
   | **Réseau virtuel**| Sélectionnez le réseau virtuel créé dans la section 2, par exemple `vnet-sql-mi-secondary`. |
   | **Adresse IP publique**| Sélectionnez **Créer nouveau**. |
   | **Nom de l’adresse IP publique**| Entrez un nom pour votre adresse IP, par exemple `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Paramètres de la passerelle secondaire](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Créez la passerelle de réseau virtuel secondaire à l’aide de PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Connecter les passerelles 
Créez les connexions entre les deux passerelles à l’aide du portail Azure ou de PowerShell. 

Deux connexions doivent être créées : la connexion entre la passerelle principale et la passerelle secondaire, puis la connexion entre la passerelle secondaire et la passerelle principale. 

La clé partagée utilisée pour les deux connexions doit être la même pour chaque connexion. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Créez les connexions entre les deux passerelles à l’aide du portail Azure. 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez la passerelle principale que vous avez créée à l’étape 4. 
1. Sélectionnez **Connexions** sous **Paramètres**, puis sélectionnez **Ajouter** pour créer une nouvelle connexion. 

   ![Ajouter une connexion à la passerelle principale](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Entrez un nom pour votre connexion, puis tapez une valeur pour la **clé partagée**. 
1. Sélectionnez la **deuxième passerelle de réseau virtuel**, puis sélectionnez la passerelle pour l’instance managée secondaire. 

   ![Créer une connexion entre les passerelles principale et secondaire](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Sélectionnez **OK** pour ajouter votre nouvelle connexion entre les passerelles principale et secondaire.
1. Répétez ces étapes pour créer une connexion de la passerelle de l’instance managée secondaire à la passerelle de l’instance managée principale. 

   ![Créer la connexion de la passerelle secondaire à principale](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Créez les connexions entre les deux passerelles à l’aide de PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Créer le groupe de basculement 
Créez le groupe de basculement pour vos instances managées à l’aide du portail Azure ou de PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Créez le groupe de basculement pour vos instances managées à l’aide du portail Azure. 

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez l’instance managée principale que vous souhaitez ajouter au groupe de basculement.  
1. Sous **Paramètres**, accédez à **Groupes de basculement d’instance**, puis choisissez **Ajouter un groupe** pour ouvrir la page **Groupe de basculement d’instance**. 

   ![Ajouter un groupe de basculement](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Dans la page **Groupe de basculement d’instance**, tapez le nom de votre groupe de basculement, puis choisissez l’instance managée secondaire dans la liste déroulante. Sélectionnez **Créer** pour créer votre groupe de basculement. 

   ![Créer un groupe de basculement](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Une fois le déploiement du groupe de basculement terminé, vous serez redirigé vers la page **Groupe de basculement**. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Créez le groupe de basculement pour vos instances managées à l’aide de PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Test de basculement

Testez le basculement de votre groupe de basculement à l’aide du portail Azure ou de PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Testez le basculement de votre groupe de basculement à l’aide du portail Azure. 

1. Accédez à votre instance managée dans le [portail Azure](https://portal.azure.com) et sélectionnez **Groupes de basculement d’instance** sous paramètres. 
1. Vérifiez quelle instance managée est la principale et laquelle est la secondaire. 
1. Sélectionnez **Basculement**, puis cliquez sur **Oui** dans l’avertissement concernant les sessions TDS sur le point d’être déconnectées. 

   ![Basculer le groupe de basculement](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Vérifiez quelle instance est la principale et laquelle est la secondaire. Si le basculement a réussi, les deux instances doivent avoir échangé leur rôle. 

   ![Les instances managées ont échangé leurs rôles après le basculement](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Sélectionnez de nouveau **Basculement** pour faire basculer à nouveau l’instance principale vers le rôle principal. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Testez le basculement de votre groupe de basculement à l’aide de PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Localiser le point de terminaison de l’écouteur

Une fois votre groupe de basculement configuré, mettez à jour la chaîne de connexion de votre application sur le point de terminaison de l’écouteur. Ainsi, votre application restera connectée à l’écouteur du groupe de basculement, plutôt qu’à la base de données primaire, au pool élastique ou à l’instance managée. De cette façon, vous n’avez pas besoin de mettre à jour manuellement la chaîne de connexion chaque fois que votre entité Azure SQL Database fait l’objet d’un basculement et le trafic est routé vers l’entité qui fait office d’entité principale. 

Le point de terminaison de l’écouteur se présente sous la forme, `fog-name.database.windows.net` et est visible dans le Portail Azure, quand vous affichez le groupe de basculement :

![Chaîne de connexion du groupe de basculement](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Remarques

- La suppression d’un groupe de basculement pour une base de données unique ou mise en pool n’interrompt pas la réplication et n’entraîne pas la suppression de la base de données répliquée. Vous devrez arrêter manuellement la géoréplication et supprimer la base de données du serveur secondaire si vous souhaitez rajouter une base de données unique ou mise en pool à un groupe de basculement après sa suppression. Si vous n’effectuez pas l’une ou l’autre de ces opérations, vous risquez d’obtenir une erreur similaire à `The operation cannot be performed due to multiple errors` quand vous tentez d’ajouter la base de données au groupe de basculement. 


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions détaillées sur la configuration d’un groupe de basculement, consultez les tutoriels suivants :
- [Ajouter une base de données unique à un groupe de basculement](sql-database-single-database-failover-group-tutorial.md)
- [Ajouter un pool élastique à un groupe de basculement](sql-database-elastic-pool-failover-group-tutorial.md)
- [Ajouter des instances managées à un groupe de basculement](sql-database-managed-instance-failover-group-tutorial.md)
 
Pour obtenir une vue d’ensemble des options de haute disponibilité pour Azure SQL Database, consultez [Géoréplication](sql-database-active-geo-replication.md) et [Groupes de basculement automatique](sql-database-auto-failover-group.md). 
