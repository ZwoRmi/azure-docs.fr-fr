---
title: 'Concepts : sécurité dans AKS (Azure Kubernetes Service)'
description: Découvrez la sécurité dans AKS (Azure Kubernetes Service), notamment la communication entre les maîtres et les nœuds, les stratégies réseau et les secrets Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: e29b94f270b295725400103f288f3d3bd0c2a2eb
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380562"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Concepts de sécurité pour les applications et les clusters dans AKS (Azure Kubernetes Service)

Pour protéger vos données clientes quand vous exécutez des charges de travail d’applications dans AKS (Azure Kubernetes Service), vous devez prendre en compte la sécurité de votre cluster. Kubernetes inclut des composants de sécurité tels que les *stratégies réseau* et les *secrets*. Azure ajoute des composants tels que les groupes de sécurité réseau et les mises à niveau de cluster orchestrées. Ces composants de sécurité sont combinés afin que votre cluster AKS exécute les dernières versions de Kubernetes et mises à jour de sécurité du système d’exploitation, et que le trafic vers les pods et l’accès aux informations d’identification sensibles soient sécurisés.

Cet article présente les concepts fondamentaux qui sécurisent vos applications dans AKS :

- [Sécurité des composants maîtres](#master-security)
- [Sécurité des nœuds](#node-security)
- [Mise à niveau des clusters](#cluster-upgrades)
- [Sécurité du réseau](#network-security)
- [Secrets Kubernetes](#secrets)

## <a name="master-security"></a>Sécurité du maître

Dans AKS, les composants maîtres Kubernetes font partie du service managé fourni par Microsoft. Chaque cluster AKS a son propre maître Kubernetes dédié monolocataire pour fournir le serveur d’API, le planificateur, etc. Ce maître est managé et maintenu par Microsoft.

Par défaut, le serveur d’API Kubernetes utilise une adresse IP publique avec nom de domaine complet (FQDN). Vous pouvez contrôler l’accès au serveur d’API avec des contrôles d’accès en fonction du rôle Kubernetes et Azure Active Directory. Pour plus d’informations, consultez [Intégration d’Azure AD à AKS][aks-aad].

## <a name="node-security"></a>Sécurité des nœuds

Les nœuds AKS sont des machines virtuelles Azure dont vous assurez la gestion et la maintenance. Les nœuds exécutent une distribution Ubuntu Linux optimisée avec le runtime de conteneur Docker. Quand un cluster AKS est créé ou fait l’objet d’un scale-up, les nœuds sont déployés automatiquement avec les dernières configurations et mises à jour de sécurité du système d’exploitation.

La plateforme Azure applique automatiquement les correctifs de sécurité du système d’exploitation aux nœuds chaque nuit. Si une mise à jour de la sécurité du système d’exploitation nécessite un redémarrage de l’hôte, ce redémarrage n’est pas effectué automatiquement. Vous pouvez redémarrer les nœuds manuellement, ou bien appliquer une approche courante qui consiste à utiliser [Kured][kured], démon de redémarrage open source pour Kubernetes. S’exécutant en tant que [ressource DaemonSet][aks-daemonset], Kured supervise chaque nœud à la recherche d’un fichier indiquant qu’un redémarrage est nécessaire. Les redémarrages sont gérés au sein du cluster à l’aide du même [processus d’isolation et de drainage](#cordon-and-drain) que celui appliqué pour la mise à niveau du cluster.

Les nœuds sont déployés sur un sous-réseau de réseau virtuel privé, sans aucune adresse IP publique affectée. Pour des raisons de gestion et de résolution des problèmes, SSH est activé par défaut. Cet accès SSH n’est disponible qu’au moyen de l’adresse IP interne. Vous pouvez utiliser des règles de groupe de sécurité réseau Azure pour restreindre davantage l’accès d’une plage d’adresses IP aux nœuds AKS. La suppression de la règle SSH de groupe de sécurité réseau par défaut et la désactivation du service SSH sur les nœuds empêchent la plateforme Azure d’effectuer des tâches de maintenance.

Pour fournir le stockage, les nœuds utilisent des disques managés Azure. Pour la plupart des tailles de nœud de machine virtuelle, il s’agit de disques Premium assortis de disques SSD hautes performances. Les données stockées sur les disques managés sont automatiquement chiffrées au repos au sein de la plateforme Azure. Pour améliorer la redondance, ces disques sont également répliqués de manière sécurisée au sein du centre de données Azure.

## <a name="cluster-upgrades"></a>Mise à niveau des clusters

Pour des raisons de conformité et de sécurité, ou pour que soient utilisées les dernières fonctionnalités, Azure fournit des outils pour orchestrer la mise à niveau d’un cluster et de composants AKS. Cette orchestration de la mise à niveau inclut les composants maîtres et agents Kubernetes. Vous pouvez afficher la liste des versions Kubernetes disponibles pour votre cluster AKS. Pour démarrer le processus de mise à niveau, vous spécifiez une de ces versions disponibles. Ensuite, Azure isole et draine de manière sécurisée chaque nœud AKS et effectue la mise à niveau.

### <a name="cordon-and-drain"></a>Isolation et drainage

Pendant le processus de mise à niveau, les nœuds AKS sont chacun isolés du cluster afin que de nouveaux pods ne soient pas planifiés sur ces nœuds. Les nœuds sont ensuite drainés et mis à niveau comme suit :

- Les pods existants sont normalement arrêtés et planifiés sur les nœuds restants.
- Le nœud est redémarré, le processus de mise à niveau est effectué, puis le nœud rejoint le cluster AKS.
- Les pods sont planifiés pour être réexécutés sur les nœuds.
- Le nœud suivant dans le cluster est isolé et drainé au moyen du même processus jusqu’à ce que tous les nœuds soient mis à niveau.

Pour plus d’informations, consultez [Mettre à niveau un cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Sécurité du réseau

Pour la connectivité et la sécurité avec les réseaux locaux, vous pouvez déployer votre cluster AKS sur des sous-réseaux de réseau virtuel Azure existants. Ces réseaux virtuels peuvent avoir une connexion Express Route ou VPN de site à site Azure à votre réseau local. Des contrôleurs d’entrée Kubernetes peuvent être définis avec des adresses IP privées internes afin que les services ne soient accessibles que via cette connexion réseau interne.

### <a name="azure-network-security-groups"></a>Groupes de sécurité réseau Azure

Pour filtrer le flux du trafic dans les réseaux virtuels, Azure utilise des règles de groupe de sécurité réseau. Ces règles définissent les plages d’adresses IP source et de destination, les ports et les protocoles qui se voient autoriser ou refuser l’accès aux ressources. Des règles par défaut sont créées pour autoriser le trafic TLS vers le serveur d’API Kubernetes et pour l’accès SSH aux nœuds. Quand vous créez des services avec des équilibreurs de charge, des mappages de ports ou des routes d’entrée, AKS modifie automatiquement le groupe de sécurité réseau afin que le trafic transite de manière appropriée.

## <a name="kubernetes-secrets"></a>Secrets Kubernetes

Un *secret* Kubernetes permet d’injecter des données sensibles dans des pods, telles que les clés ou les informations d’identification d’accès. Tout d’abord, vous créez un secret à l’aide de l’API Kubernetes. Quand vous définissez votre pod ou déploiement, un secret spécifique peut être demandé. Les secrets sont fournis uniquement aux nœuds dont un pod planifié a besoin d’un secret. En outre, le secret est stocké dans un volume *tmpfs*, au lieu d’être écrit sur le disque. Quand le dernier pod sur un nœud qui requiert un secret est supprimé, ce dernier est supprimé du volume tmpfs du nœud. Les secrets sont stockés dans un espace de noms donné et ne sont accessibles qu’aux pods se trouvant dans cet espace de noms.

L’utilisation de secrets réduit la quantité d’informations sensibles définies dans le manifeste YAML des pods ou services. Au lieu de cela, vous demandez le secret stocké sur le serveur d’API Kubernetes dans le cadre de votre manifeste YAML. Ainsi, l’accès au secret n’est accordé qu’au pod concerné.

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec la sécurisation de vos clusters AKS, consultez [Mettre à niveau un cluster AKS][aks-upgrade-cluster].

Pour plus d’informations sur les concepts fondamentaux de Kubernetes et d’AKS, consultez les articles suivants :

- [Clusters et charges de travail Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identité Kubernetes/AKS][aks-concepts-identity]
- [Réseaux virtuels Kubernetes/AKS][aks-concepts-network]
- [Stockage Kubernetes/AKS][aks-concepts-storage]
- [Mise à l’échelle Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md