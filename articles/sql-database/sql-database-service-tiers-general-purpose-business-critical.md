---
title: Azure SQL Database - Usage général et Critique pour l’entreprise | Microsoft Docs
description: L’article aborde les niveaux de service Usage général et Critique pour l’entreprise dans le modèle d’achat de cœurs virtuels.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 15fd86a88c3025f81741d614b03d5c4c7c60262c
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351740"
---
# <a name="general-purpose-and-business-critical-service-tiers"></a>Niveaux de service Usage général et Critique pour l’entreprise

Cet article décrit les considérations relatives au stockage et à la sauvegarde pour les niveaux de service Usage général et Critique pour l’entreprise dans le modèle d’achat basé sur le nombre de cœurs virtuels.

> [!NOTE]
> Pour plus d’informations sur le niveau de service Hyperscale dans le modèle d’achat basé sur le nombre de cœurs virtuels, consultez [Niveau de service Hyperscale](sql-database-service-tier-hyperscale.md). Pour obtenir une comparaison du modèle d’achat basé sur le nombre de cœurs virtuelss avec le modèle d’achat basé sur des unités DTU, consultez [Ressources et modèles d’achat Azure SQL Database](sql-database-service-tiers.md).

## <a name="data-and-log-storage"></a>Stockage des données et des journaux

Tenez compte des éléments suivants :

- Le stockage alloué est utilisé par les fichiers de données (MDF) et les fichiers journaux (LDF).
- Chaque taille de calcul d’une base de données unique accepte une taille de base de données maximale, qui correspond, par défaut, à 32 Go.
- Lorsque vous configurez la taille de base de données unique nécessaire (c’est-à-dire, la taille des fichiers MDF), 30 % de stockage supplémentaire sont automatiquement ajoutés pour prendre en charge les fichiers LDF
- La taille de stockage dans une instance gérée doit être spécifiée en multiples de 32 Go.
- Vous pouvez choisir n’importe quelle taille de base de données unique située entre 10 Go et la taille maximale prise en charge.
  - Pour le stockage Standard, augmentez ou diminuez la taille par incréments de 10 Go
  - Pour le stockage Premium, augmentez ou diminuez la taille par incréments de 250 Go
- Dans le niveau de service Usage général, `tempdb` utilise un disque SSD attaché et le coût de ce stockage est inclus dans le prix du cœur virtuel.
- Dans le niveau de service Critique pour l’entreprise, `tempdb` partage le disque SSD attaché avec les fichiers MDF et LDF, et le coût du stockage tempDB est inclus dans le prix du cœur virtuel.

> [!IMPORTANT]
> Le stockage total alloué aux fichiers MDF et LDF vous est facturé.

Pour surveiller la taille totale actuelle des fichiers MDF et LDF, utilisez [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Pour surveiller la taille actuelle de chaque fichier MDF et LDF, utilisez [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Sauvegardes et stockage

Du stockage de sauvegardes de base de données est alloué pour prendre en charge les fonctionnalités Limite de restauration dans le temps et [Rétention à long terme](sql-database-long-term-retention.md) de SQL Database. Ce stockage est alloué séparément pour chaque base de données. De plus, les fonctionnalités Limite de restauration dans le temps et Conservation à long terme sont, elles aussi, facturées séparément.

- **Limite de restauration dans le temps** : les sauvegardes de bases de données sont automatiquement copiées vers le [stockage RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md). La taille de stockage augmente dynamiquement avec chaque nouvelle création de sauvegarde.  Le stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux copiés toutes les 5 minutes. La consommation du stockage dépend du taux de change de la base de données et de la période de rétention. Vous pouvez configurer une période de rétention distincte pour chaque base de données, allant de 7 à 35 jours. Un volume de stockage minimal correspondant à la taille des données est fourni sans frais supplémentaires. Pour la plupart des bases de données, cette quantité est suffisante pour stocker l’équivalent de 7 jours de sauvegardes.
- **Rétention à long terme** : SQL Database permet de configurer une rétention à long terme des sauvegardes complètes d’une durée de 10 ans. Si la stratégie de rétention à long terme est activée, ces sauvegardes sont stockées automatiquement dans le stockage RA-GRS. Toutefois, vous pouvez contrôler la fréquence à laquelle les sauvegardes sont copiées. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. Cette configuration définit la quantité de stockage utilisée pour les sauvegardes de rétention à long terme. Vous pouvez utiliser la calculatrice de prix LTR pour estimer le coût du stockage de rétention à long terme. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles de calcul et les tailles de stockage spécifiques disponibles pour une base de données unique dans les niveaux de service Usage général et Critique pour l’entreprise, consultez [Limites des ressources basées sur les cœurs virtuels SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage spécifiques disponibles pour les pools élastiques dans les niveaux de service Usage général et Critique pour l’entreprise, consultez [Limites des ressources basées sur les cœurs virtuels SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).