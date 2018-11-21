---
title: Migrer des clusters Apache Hadoop locaux vers Azure HDInsight - bonnes pratiques sur l’infrastructure
description: Découvrez les bonnes pratiques sur l’infrastructure pour la migration des clusters Hadoop locaux vers Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 614fdae1865f008bdbc2cb8d5e8b96c0addcc112
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036920"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrer des clusters Apache Hadoop locaux vers Azure HDInsight - bonnes pratiques sur l’infrastructure

Cet article fournit des recommandations pour la gestion de l’infrastructure des clusters Azure HDInsight. Il fait partie d’une série qui propose des bonnes pratiques pour aider à la migration des systèmes Apache Hadoop locaux vers Azure HDInsight.

## <a name="plan-well-for-the-capacity-needed-for-hdinsight-clusters"></a>Planifier correctement la capacité nécessaire pour les clusters HDInsight

Les principaux choix à effectuer pour la planification de la capacité des clusters HDInsight sont les suivants :

- **Choisir la région** : la région Azure détermine où le cluster est physiquement provisionné. Pour réduire la latence de lecture et d’écriture, le cluster doit être dans la même région que les données.
- **Choisir la taille et l’emplacement de stockage** : le stockage par défaut doit être dans la même région que le cluster. Pour un cluster à 48 nœuds, il est recommandé d’avoir de 4 à 8 comptes de stockage. Bien que le stockage total actuel puisse être suffisant, chaque compte de stockage fournit une bande passante réseau supplémentaire pour les nœuds de calcul. Quand il existe plusieurs comptes de stockage, utilisez un nom aléatoire pour chaque compte de stockage, sans préfixe. L’objectif de l’attribution aléatoire des noms est de réduire le risque de goulot d’étranglement de stockage (limitation) ou les défaillances en mode commun parmi tous les comptes. Pour de meilleures performances, utilisez un seul conteneur par compte de stockage.
- **Choisir la taille et le type de machine virtuelle (série G désormais prise en charge)**  : chaque type de cluster a un ensemble de types de nœuds, et chaque type de nœud a des options spécifiques pour la taille et le type de machine virtuelle. La taille et le type de machine virtuelle sont déterminés par la puissance de traitement de l’UC, la taille de la RAM et la latence du réseau. Une charge de travail simulée peut être utilisée pour déterminer la taille de machine virtuelle optimale et le type de chaque nœud.
- **Choisir le nombre de nœuds worker** : le nombre initial de nœuds worker peut être déterminé à l’aide des charges de travail simulées. Le cluster peut être mis à l’échelle par la suite en ajoutant d’autres nœuds worker pour répondre aux pics de charge. Le cluster peut ensuite revenir au niveau de départ quand les nœuds worker supplémentaires ne sont pas nécessaires.

Pour plus d’informations, consultez l’article [Planification de la capacité pour les clusters HDInsight](../hdinsight-capacity-planning.md)

## <a name="use-the-recommended-virtual-machine-types-for-cluster-nodes"></a>Utiliser les types de machine virtuelle recommandés pour les nœuds de cluster

Consultez [Tailles des machines virtuelles et configuration des nœuds par défaut pour les clusters](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) pour connaître les types de machine virtuelle recommandés pour chaque type de cluster HDInsight.

## <a name="check-the-availability-of-hadoop-components-in-hdinsight"></a>Vérifier la disponibilité des composants Hadoop dans HDInsight

Chaque version de HDInsight est une distribution cloud d’une version de la solution HDP (Hortonworks Data Platform) et est constituée d’un ensemble de composants de l’écosystème Hadoop. Consultez [Gestion des versions des composants HDInsight](../hdinsight-component-versioning.md) pour plus d’informations sur tous les composants HDInsight et leur version actuelle.

Vous pouvez également utiliser l’interface utilisateur ou l’API REST Ambari pour vérifier les versions et composants Hadoop dans HDInsight.

Les applications ou composants qui étaient disponibles dans les clusters locaux mais qui ne font pas partie des clusters HDInsight peuvent être ajoutés sur un nœud de périphérie ou une machine virtuelle dans le même réseau virtuel que le cluster HDInsight. Une application Hadoop tierce qui n’est pas disponible sur Azure HDInsight peut être installée à l’aide de l’option « Applications » dans le cluster HDInsight. Des applications Hadoop personnalisées peuvent être installées sur un cluster HDInsight avec des « actions de script ». Le tableau suivant répertorie certaines des applications courantes et leurs options d’intégration HDInsight :

|**Application**|**Intégration**
|---|---|
|Ventilation|Nœud de périphérie HDInsight ou IaaS
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Aucune (HDP uniquement)
|Datameer|Nœud de périphérie HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB comme alternative sur Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB comme alternative sur Azure)
|NiFi|IaaS 
|Presto|Nœud de périphérie HDInsight ou IaaS
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW comme alternative sur Azure)
|Tableau|IaaS 
|Waterline|Nœud de périphérie HDInsight
|StreamSets|Périphérie HDInsight 
|Palantir|IaaS 
|Sailpoint|Iaas 

Pour plus d’informations, consultez l’article [Composants Apache Hadoop disponibles avec différentes versions de HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personnaliser des clusters HDInsight à l’aide d’actions de script

HDInsight fournit une méthode de configuration des clusters appelée **action de script**. Une action de script est un script bash qui s’exécute sur les nœuds dans un cluster HDInsight et peut être utilisé pour installer des composants supplémentaires et modifier des paramètres de configuration.

Les actions de script doivent être stockées sur un URI accessible à partir du cluster HDInsight. Elles peuvent être utilisées pendant ou après la création du cluster et peuvent aussi être limitées de manière à s’exécuter sur certains types de nœuds uniquement.

Le script peut être persistant ou exécuté une seule fois. Les scripts persistants servent à personnaliser les nouveaux nœuds worker ajoutés au cluster lors d’opérations de mise à l’échelle. Un script persistant peut également appliquer des modifications à un autre type de nœud, tel qu’un nœud principal, quand des opérations de mise à l’échelle ont lieu.

HDInsight propose des scripts prédéfinis pour installer les composants suivants sur des clusters HDInsight :

- Ajouter un compte de stockage Azure
- Installer Hue
- Installer Presto
- Installer Solr
- Installer Giraph
- Précharger les bibliothèques Hive
- Installer ou mettre à jour Mono

> [!Note]
> HDInsight ne fournit pas de prise en charge directe pour les composants hadoop personnalisés ni les composants installés à l’aide d’actions de script.

Des actions de script peuvent également être publiées dans la Place de marché Azure en tant qu’application HDInsight.

Pour plus d’informations, consultez les articles suivants :

- [Installer des applications Hadoop tierces sur Azure HDInsight](../hdinsight-apps-install-applications.md)
- [Personnaliser des clusters HDInsight à l’aide d’actions de script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publier une application HDInsight sur la Place de marché Microsoft Azure](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personnaliser des configurations HDInsight à l’aide de Bootstrap

Les modifications des configurations dans les fichiers config tels que `core-site.xml`, `hive-site.xml` et `oozie-env.xml` peuvent être apportées à l’aide de Bootstrap. Le script suivant est un exemple qui utilise Powershell :

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzureRmHDInsightClusterConfig '
    | Set—AzureRmHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzureRmHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzureRmHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Pour plus d’informations, consultez l’article [Personnalisation de clusters HDInsight à l’aide de Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md)

## <a name="use-edge-nodes-on-hadoop-clusters-in-hdinsight-to-access-the-client-tools"></a>Utiliser des nœuds de périphérie sur des clusters Hadoop dans HDInsight pour accéder aux outils clients

Un nœud de périphérie vide est une machine virtuelle Linux dotée des mêmes outils clients installés et configurés que dans les nœuds principaux, mais sans services Hadoop en cours d’exécution. Le nœud de périphérie peut être utilisé aux fins suivantes :

- accès au cluster
- test des applications clientes
- hébergement des applications clientes

Les nœuds de périphérie peuvent être créés et supprimés via le portail Azure et peuvent être utilisés pendant ou après la création du cluster. Après avoir créé le nœud de périphérie, vous pouvez vous y connecter à l’aide de SSH et exécuter les outils clients pour accéder au cluster Hadoop dans HDInsight. Le point de terminaison SSH de nœud de périphérie est `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.

Pour plus d’informations, consultez l’article [Utiliser les nœuds de périphérie vides sur les clusters Hadoop dans HDInsight](../hdinsight-apps-use-edge-node.md)

## <a name="use-the-scale-up-and-scale-down-feature-of-clusters"></a>Utiliser la fonctionnalité de scale-up et de scale-down des clusters

HDInsight fournit l’élasticité en vous offrant la possibilité de monter ou de descendre en puissance le nombre de nœuds de travail dans vos clusters. Cette fonctionnalité vous permet de réduire un cluster après certaines heures ou les week-ends, et de le développer pendant les pics d’activité.

La mise à l’échelle du cluster peut être automatisée à l’aide des méthodes suivantes :

### <a name="powershell-cmdlet"></a>Applet de commande PowerShell

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

### <a name="azure-cli"></a>Azure CLI

```powershell
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```

### <a name="azure-portal"></a>Portail Azure

Quand vous ajoutez des nœuds à votre cluster HDInsight en cours d’exécution, aucun travail en attente ni en cours d’exécution n’est affecté. De nouveaux travaux peuvent être soumis en toute sécurité pendant que le processus de mise à l’échelle est en cours d’exécution. Si les opérations de mise à l’échelle échouent pour une raison quelconque, l’échec est géré en douceur, laissant le cluster dans un état fonctionnel.

Toutefois, si vous effectuez un scale-down sur votre cluster en supprimant des nœuds, tous les travaux en attente ou en cours d’exécution échouent à la fin de l’opération de mise à l’échelle. Cet échec est dû au redémarrage de certains des services au cours du processus. Pour résoudre ce problème, vous pouvez attendre la fin des travaux avant d’effectuer un scale-down sur votre cluster, arrêter manuellement les travaux ou renvoyer ces travaux une fois l’opération de mise à l’échelle terminée.

Si vous réduisez votre cluster jusqu’à la valeur minimale d’un nœud worker, HDFS peut se bloquer en mode sans échec si les nœuds worker sont redémarrés en raison d’une mise à jour corrective ou immédiatement après l’opération de mise à l’échelle. Vous pouvez exécuter la commande suivante afin de sortir HDFS du mode sans échec :

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Après avoir quitté le mode sans échec, vous pouvez manuellement supprimer les fichiers temporaires, ou attendre que Hive les nettoie automatiquement.

Pour plus d’informations, consultez l’article [Mettre à l’échelle les clusters HDInsight](../hdinsight-scaling-best-practices.md)

## <a name="use-hdinsight-with-azure-virtual-network"></a>Utiliser HDInsight avec un réseau virtuel Azure

Les réseaux virtuels Azure permettent à des ressources Azure, comme les machines virtuelles Azure, de communiquer en toute sécurité entre elles, avec Internet et avec les réseaux locaux, en filtrant et en acheminant le trafic réseau.

L’utilisation d’un réseau virtuel Azure avec HDInsight permet les scénarios suivants :

- connexion à HDInsight directement à partir d’un réseau local ;
- connexion de HDInsight à des banques de données dans un réseau virtuel Azure ;
- accès direct aux services Hadoop qui ne sont pas disponibles publiquement sur Internet, tels que les API Kafka ou l’API Java HBase.

HDInsight peut être ajouté à un réseau virtuel Azure nouveau ou existant. Si HDInsight est ajouté à un réseau virtuel existant, les groupes de sécurité réseau et itinéraires définis par l’utilisateur doivent être mis à jour pour autoriser un accès illimité à [plusieurs adresses IP](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip-1) dans le centre de données Azure. En outre, évitez de bloquer le trafic vers les [ports](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports) qui sont utilisés par les services HDInsight.

> [!Note]
> HDInsight ne prend pas en charge le tunneling forcé pour l’instant. Le tunneling forcé est un paramètre de sous-réseau qui force l’acheminement du trafic Internet sortant vers un appareil à des fins d’inspection et de journalisation. Supprimez le tunneling forcé avant d’installer HDInsight dans un sous-réseau, ou créez un sous-réseau pour HDInsight. HDInsight ne prend pas non plus en charge la restriction de la connectivité réseau sortante.

Pour plus d’informations, consultez les articles suivants :

- [Vue d’ensemble des réseaux virtuels Azure](../../virtual-network/virtual-networks-overview.md)
- [Étendre HDInsight à l’aide d’un Réseau virtuel Azure](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="use-azure-virtual-network-service-endpoints-to-securely-connect-to-other-azure-services"></a>Utiliser des points de terminaison de service de réseau virtuel Azure pour se connecter en toute sécurité à d’autres services Azure

HDInsight prend en charge les [points de terminaison de service de réseau virtuel](../../virtual-network/virtual-network-service-endpoints-overview.md)  qui vous permettent de vous connecter en toute sécurité au stockage Blob Azure, à Azure Data Lake Storage Gen2, à Cosmos DB et aux bases de données SQL. En activant un point de terminaison de service pour Azure HDInsight, le trafic transite par un itinéraire sécurisé à partir du centre de données Azure. Avec ce niveau amélioré de sécurité au niveau de la couche de mise en réseau, vous pouvez verrouiller les comptes de stockage de données volumineux sur leur réseau virtuel spécifié et continuer à utiliser les clusters HDInsight sans interruption pour accéder à ces données et les traiter.

Pour plus d’informations, consultez les articles suivants :

- [Points de terminaison de service de réseau virtuel](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Améliorer la sécurité HDInsight avec des points de terminaison de service](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/.md)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Connecter HDInsight au réseau local

HDInsight peut être connecté au réseau local à l’aide de réseaux virtuels Azure et d’une passerelle VPN. La procédure suivante peut être utilisée pour établir la connectivité :

- Utilisez HDInsight dans un réseau virtuel Azure qui dispose d’une connectivité au réseau local.
- Configurez la résolution de noms DNS entre le réseau virtuel et le réseau local.
- Configurez des groupes de sécurité réseau ou des itinéraires définis par l’utilisateur pour contrôler le trafic réseau.

Pour plus d’informations, consultez l’article [Connecter HDInsight à votre réseau local](../connect-on-premises-network.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez le prochain article de cette série :

- [Bonnes pratiques concernant le stockage pour une migration locale vers Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md)