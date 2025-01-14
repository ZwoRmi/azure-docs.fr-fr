---
title: Migrer une base de données d’une instance SQL Server vers Azure SQL Database - Managed Instance | Microsoft Docs
description: Apprenez à migrer une base de données depuis une instance SQL Server vers Azure SQL Database - Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: f877306170b45d65a52a4c76afd7f064e83f240a
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937310"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migration d’une instance SQL Server vers une instance managée Azure SQL Database

Dans cet article, vous allez découvrir les méthodes possibles pour la migration d’une instance SQL Server 2005 ou ultérieure vers une [instance managée Azure SQL Database](sql-database-managed-instance.md). Pour obtenir des informations sur la migration vers une base de données unique ou un pool élastique, consultez [Migration vers une base de données unique ou mise en pool](sql-database-cloud-migrate.md). Pour obtenir des informations sur la migration à partir d’autres plateformes, consultez le [Guide de migration des bases de données Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Si vous souhaitez démarrer rapidement et essayer Managed Instance, vous souhaiterez peut-être accéder au [guide de démarrage rapide](sql-database-managed-instance-quickstart-guide.md) plutôt qu’à cette page. 

Le processus général de migration d’une base de données ressemble à ce qui suit :

![processus de migration](./media/sql-database-managed-instance-migration/migration-process.png)

- [Évaluer la compatibilité d’instance gérée](#assess-managed-instance-compatibility) où vous devez vous assurer qu’aucun problème bloquant ne peut empêcher vos migrations.
  - Cette étape comprend également la création d’une [ligne de base des performances](#create-performance-baseline) pour déterminer l’utilisation des ressources sur votre instance de SQL Server source. Cette étape est nécessaire si vous souhaitez déployer une Managed Instance correctement dimensionnée et vérifier que les performances après la migration ne sont pas affectées.
- [Choisir des options de connectivité des applications](sql-database-managed-instance-connect-app.md)
- [Déployer sur une instance gérée dimensionnée de façon optimale](#deploy-to-an-optimally-sized-managed-instance) où vous allez choisir les caractéristiques techniques (nombre de vCores, quantité de mémoire) et le niveau de performance (critique pour l’entreprise, usage général) de votre Managed Instance.
- [Sélectionner une méthode de migration et effectuer la migration](#select-migration-method-and-migrate) où vous migrez vos bases de données à l’aide de la migration hors connexion (sauvegarde/restauration native, importation/exportation de base de données) ou de la migration en ligne (service de migration des données, réplication transactionnelle).
- [Surveiller les applications](#monitor-applications) pour vous assurer que vous disposez des performances attendues.

> [!NOTE]
> Pour effectuer la migration d’une base de données individuelle vers une base de données unique ou un pool élastique, consultez [Effectuer la migration d’une base de données SQL Server vers Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Évaluer la compatibilité d’instance gérée

Tout d’abord, déterminez si l’instance gérée est compatible avec les exigences de base de données de votre application. L’option de déploiement d’instance gérée est conçue pour faciliter la migration « lift-and-shift » de la plupart des applications existantes qui utilisent SQL Server localement ou sur des machines virtuelles. Toutefois, vous risquez parfois d’avoir besoin de fonctionnalités qui ne sont pas encore prises en charge et dont le coût d’implémentation d’une solution de contournement est trop élevé.

Utilisez l’[Assistant Migration de données](https://docs.microsoft.com/sql/dma/dma-overview) pour détecter les éventuels problèmes de compatibilité qui impactent le fonctionnement de la base de données sur Azure SQL Database. Cet Assistant ne prend pas encore en charge une instance managée en tant que destination de migration, mais il est recommandé d’effectuer une évaluation par rapport à Azure SQL Database et d’examiner attentivement la liste des problèmes de compatibilité et de parité de fonctionnalité signalés par rapport à la documentation du produit. Consultez les [fonctionnalités Azure SQL Database](sql-database-features.md) pour savoir si des problèmes de blocage sont signalés dans l’instance gérée, car la plupart des problèmes de blocage empêchant de migrer vers une base de données Azure SQL Database ont été supprimés avec l’instance gérée. Par exemple, des fonctionnalités comme les requêtes entre plusieurs bases de données, les transactions entre plusieurs bases de données au sein de la même instance, un serveur lié à d’autres sources SQL, la prise en charge du Common Language Runtime, les tables temporaires globales, les affichages au niveau de l’instance, Service Broker, etc. sont disponibles dans les instances gérées.

Si certains problèmes de blocage signalés ne sont pas supprimés avec l’option de déploiement d’instance gérée, vous devrez peut-être envisager une autre solution, comme l’utilisation de [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Voici quelques exemples :

- Si vous avez besoin d’un accès direct au système d’exploitation ou au système de fichiers, par exemple pour installer des agents tiers ou personnalisés sur la même machine virtuelle avec SQL Server.
- Si vous dépendez obligatoirement de fonctionnalités qui ne sont pas encore prises en charge, comme FileStream/FileTable, PolyBase et les transactions entre plusieurs instances.
- Si vous devez absolument conserver une version spécifique de SQL Server (2012, par exemple).
- Si vos exigences de calcul sont beaucoup plus faibles que ce qu’offre l’instance gérée (un seul vCore, par exemple) et que l’option de consolidation de bases de données n’est pas une option acceptable.

Si vous avez résolu tous les bloqueurs de migration identifiés et que vous poursuivez la migration vers Managed Instance, notez que certains changements peuvent affecter les performances de votre charge de travail :
- Le mode de la récupération complète obligatoire et la planification de la sauvegarde automatisée périodique peuvent avoir un impact sur les performances de votre charge de travail, ou sur les actions de maintenance/ETL si vous avez utilisé régulièrement le mode simple ou de journalisation en bloc, ou avez arrêté les sauvegardes à la demande.
- Différentes configurations au niveau serveur ou base de données, telles que les indicateurs de trace ou les niveaux de compatibilité.
- Les nouvelles fonctionnalités que vous utilisez, comme les groupes de basculement automatique ou la technologie TDE, peuvent avoir une incidence sur l’utilisation du processeur et des E/S.

Managed Instance garantit une disponibilité de 99,99 % même dans les scénarios critiques, afin que la surcharge engendrée par ces fonctionnalités ne puissent pas être désactivée. Pour plus d’informations, consultez [les causes racines susceptibles de provoquer des performances différentes sur SQL Server et Managed Instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Créer un référentiel de performance

Si vous avez besoin de comparer les performances de votre charge de travail sur Managed Instance avec votre charge de travail d’origine s’exécutant sur SQL Server, vous devez créer un référentiel de performance qui sera utilisé pour la comparaison. 

La ligne de base des performances est un ensemble de paramètres tels que l’utilisation moyenne/maximale de l’UC, la latence moyenne/maximale d’E/S de disque, le débit, les IOPS, l’espérance de vie moyenne/maximale d’une page, la taille maximale de Tempdb. Vous aimeriez avoir des paramètres similaires voire mieux adaptés après la migration. Il est donc important de mesurer et de noter les valeurs de référence pour ces paramètres. En plus des paramètres système, vous devriez sélectionner un ensemble de requêtes représentatives ou les requêtes les plus importantes dans votre charge de travail et mesurer la durée minimale/moyenne/maximale, l’utilisation de l’UC pour les requêtes sélectionnées. Ces valeurs vous permettraient de comparer les performances de la charge de travail en cours d’exécution sur Managed Instance aux valeurs d’origine sur votre SQL Server source.

Voici quelques paramètres à mesurer sur votre instance SQL Server : 
- [Analysez l’utilisation du processeur sur votre instance SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) et enregistrez l’utilisation moyenne et maximale du processeur.
- [Analysez l’utilisation de la mémoire sur votre instance SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) et déterminez la quantité de mémoire utilisée par différents composants, tels que le pool de mémoires tampons, le cache du plan, le pool de stockage en colonne, l’[OLTP en mémoire](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017), etc. Par ailleurs, vous devez rechercher les valeurs moyennes et maximales du compteur de performance de la mémoire Espérance de vie d’une page.
- Analysez l’utilisation des E/S disque sur l’instance SQL Server source à l’aide de la vue [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) ou des [compteurs de performance](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Analysez les performances des requêtes et des charges de travail sur votre instance SQL Server en examinant les vues de gestion dynamique ou le magasin de données des requêtes si vous effectuez une migration à partir de la version SQL Server 2016 +. Identifiez la durée moyenne et l’utilisation du processeur des requêtes les plus importantes dans votre charge de travail, afin de les comparer avec les requêtes qui s’exécutent sur l’instance Managed Instance.

> [!Note]
> Si vous remarquez un problème avec votre charge de travail sur SQL Server, comme une utilisation élevée du processeur, une sollicitation constante de la mémoire, des problèmes de paramétrage ou de tempdb, vous devez tenter de le résoudre dans votre instance SQL Server source avant de procéder au référentiel et à la migration. Migrer des problèmes connus sur un nouveau système peut provoquer des résultats inattendus et invalider toute comparaison de performance.

À l’issue de cette activité, vous devez avoir documenté les valeurs moyennes et maximales du processeur, de la mémoire et de l’utilisation des E/S sur votre système source, de même que les durées moyennes et maximales des requêtes principales et les plus critiques dans votre charge de travail, avec leur utilisation du processeur. Vous devrez utiliser ces valeurs ultérieurement pour comparer les performances de votre charge de travail sur Managed Instance avec celles du référentiel de performance de la charge de travail sur l’instance SQL Server source.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Déployer sur une instance gérée dimensionnée de façon optimale

Une instance gérée est adaptée pour des charges de travail locales qui planifient une migration vers le cloud. Un [nouveau modèle d’achat](sql-database-service-tiers-vcore.md) est ainsi présenté. Il offre davantage de flexibilité dans le choix du niveau de ressources pour vos charges de travail. Localement, vous êtes probablement habitué à dimensionner ces charges de travail à l’aide de cœurs physiques et de bandes passantes E/S. Le modèle d’achat pour une instance gérée repose sur des mémoires à tores magnétiques virtuelle, ou « vCore », avec un stockage et des E/S supplémentaires disponibles séparément. Le modèle vCore vous permet de comprendre plus facilement vos exigences de calcul dans le cloud par rapport à ce que vous utilisez localement aujourd’hui. Ce nouveau modèle vous permet de dimensionner au mieux votre environnement de destination dans le cloud. Quelques conseils généraux pouvant vous aider à choisir les caractéristiques et le niveau de service appropriés sont décrits ici :
- En fonction de l’utilisation de l’UC de référence, vous pouvez approvisionner une Managed Instance correspondant au nombre de cœurs que vous utilisez sur SQL Server, tout en tenant compte des caractéristiques de l’UC qui sont susceptibles d’être mises à l’échelle, afin de satisfaire aux [caractéristiques des machines virtuelles où est installée Managed Instance](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- En fonction de l’utilisation de la mémoire de référence, choisissez [le niveau de service disposant de la mémoire correspondante](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). La quantité de mémoire ne peut pas être choisie directement. Vous devez donc sélectionner la Managed Instance avec la quantité de vCores disposant de la mémoire correspondante (par exemple, 5,1 Go/vCore dans Gen5). 
- En fonction de la latence d’E/S de référence du sous-système de fichiers, choisissez entre les niveaux de service Usage général (latence supérieure à 5 ms) et Critique pour l’entreprise (latence inférieure à 3 ms).
- En fonction du débit de référence, pré-allouez la taille des fichiers de données ou des fichiers journaux pour atteindre les performances d’E/S attendues.

Vous pouvez choisir des ressources de calcul et de stockage au moment du déploiement, puis les modifier plus tard sans temps d’arrêt pour votre application par le biais du [portail Azure](sql-database-scale-resources.md) :

![dimensionnement de l’instance managée](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Pour apprendre à créer l’infrastructure de réseau virtuel et une instance gérée, voir [Créer une instance gérée](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Il est important de toujours maintenir votre réseau virtuel de destination et le sous-réseau en adéquation avec la [Configuration requise de réseau virtuel d’instance gérée](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Toute incompatibilité risque de vous empêcher de créer des instances ou d’utiliser celles que vous avez déjà créées. Apprenez-en davantage sur la [création de réseaux](sql-database-managed-instance-create-vnet-subnet.md) et la [configuration de réseaux existants](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="select-migration-method-and-migrate"></a>Sélectionner une méthode de migration et effectuer la migration

L’option de déploiement d’instance gérée cible des scénarios utilisateur exigeant une migration massive de bases de données à partir d’implémentations de base de données locales ou IaaS. Ils constituent le meilleur choix lorsque vous avez besoin d’effectuer une migration « lift-and-shift » du backend des applications qui utilisent régulièrement des fonctionnalités au niveau de l’instance et/ou entre plusieurs bases de données. Si cela correspond à votre scénario, vous pouvez déplacer toute une instance vers un environnement correspondant dans Azure sans avoir à redéfinir l’architecture de vos applications.

Pour déplacer des instances SQL, vous devez planifier avec soin :

- La migration de toutes les bases de données qui ont besoin d’être colocalisées (celles qui s’exécutent sur la même instance)
- La migration des objets au niveau de l’instance dont votre application dépend, notamment les connexions, les informations d’identification, les travaux et opérateurs de l’Agent SQL, ainsi que les déclencheurs au niveau du serveur.

Une instance gérée est un service géré qui vous permet de déléguer certaines des activités courantes d’administration des bases de données à la plateforme puisqu’elles y sont intégrées. Ainsi, certaines données au niveau de l’instance n’ont pas besoin de migrer, notamment les travaux de maintenance pour les sauvegardes régulières ou la configuration Always On, étant donné que la [haute disponibilité](sql-database-high-availability.md) est intégrée.

Une instance gérée prend en charge les options de migration de base de données suivantes (actuellement ce sont les seules méthodes de migration prises en charge) :

- Azure Database Migration Service - Migration ne nécessitant quasiment aucun temps d’arrêt,
- Native `RESTORE DATABASE FROM URL` - Utilise des sauvegardes natives à partir de SQL Server et nécessite un temps d’arrêt.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime. Ce service simplifie les tâches nécessaires pour déplacer des bases de données SQL Server tierces existantes vers Azure. Les options de déploiement en préversion publique incluent des bases de données dans Azure SQL Database et des bases de données SQL Server dans une machine virtuelle Azure. DMS est la méthode recommandée de migration pour vos charges de travail d’entreprise.

Si vous utilisez SSIS (SQL Server Integration Services) sur votre serveur SQL Server local, DMS ne prend pas encore en charge la migration du catalogue SSIS (SSISDB) qui stocke les packages SSIS, mais vous pouvez provisionner Azure-SSIS IR (Integration Runtime) dans ADF (Azure Data Factory) pour créer une base de données SSISDB dans une instance gérée. Vous pouvez ensuite y redéployer vos packages. Voir [Créer Azure-SSIS IR dans ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Pour plus d’informations sur ce scénario et les étapes de configuration de DMS, voir [Migrer votre base de données locale vers une instance gérée à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Restauration native à partir d’une URL

La restauration de sauvegardes natives (fichiers .bak) issues de SQL Server local ou de [SQL Server sur les machines virtuelles](https://azure.microsoft.com/services/virtual-machines/sql-server/), disponible sur [Stockage Azure](https://azure.microsoft.com/services/storage/), est une des principales fonctionnalités de l’option de déploiement d’instance gérée qui permet d’effectuer rapidement et facilement une migration de base de données hors connexion.

Le diagramme suivant fournit une vue d’ensemble du processus :

![flux de migration](./media/sql-database-managed-instance-migration/migration-flow.png)

Le tableau suivant fournit des informations supplémentaires sur les méthodes que vous pouvez utiliser en fonction de la version de SQL Server source que vous exécutez :

|Étape|Moteur SQL et version|Méthode de sauvegarde/restauration|
|---|---|---|
|Placer la sauvegarde sur Stockage Azure|Avant SQL 2012 SP1 CU2|Charger le fichier .bak directement sur Stockage Azure|
||2012 SP1 CU2 - 2016|Sauvegarde directe utilisant la syntaxe [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) dépréciée|
||2016 et versions ultérieures|Sauvegarde directe utilisant [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurer à partir d’un Stockage Azure vers une instance gérée|[RESTORE FROM URL avec SAS CREDENTIAL](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Lorsque vous migrez une base de données protégée par [Transparent Data Encryption](transparent-data-encryption-azure-sql.md) vers une instance gérée à l’aide d’une option de restauration native, le certificat correspondant du serveur SQL Server local ou IaaS doit être migré avant la restauration de la base de données. Pour des instructions détaillées, voir [Migrer un certificat TDE vers une instance gérée](sql-database-managed-instance-migrate-tde-certificate.md).
> - La restauration de bases de données système n’est pas prise en charge. Pour effectuer la migration d’objets au niveau de l’instance (stockés dans des bases de données master et msdb), nous vous recommandons de les scripter et d’exécuter des scripts T-SQL sur l’instance de destination.

Pour obtenir un guide de démarrage rapide sur la restauration d’une sauvegarde de base de données dans une instance gérée à l’aide d’informations d’identification SAP, voir [Restaurer une sauvegarde de base de données dans une instance gérée](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Surveillance des applications

Après avoir effectué la migration vers Managed Instance, vous devez suivre le comportement de l’application et les performances de votre charge de travail. Ce processus comporte les activités suivantes :
- [Comparer les performances de la charge de travail s’exécutant sur l’instance Managed Instance](#compare-performance-with-the-baseline) avec celles du [référentiel de performance que vous avez créé sur l’instance SQL Server source](#create-performance-baseline).
- [Superviser les performances de votre charge de travail](#monitor-performance) en permanence pour identifier les problèmes potentiels et les améliorations.

### <a name="compare-performance-with-the-baseline"></a>Comparer les performances avec le référentiel

La première activité que vous devez mener immédiatement après une migration réussie consiste à comparer les performances de la charge de travail avec celles de la charge de travail de référence. L’objectif de cette activité veut confirmer que les performances de la charge de travail sur votre instance Managed Instance répondent à vos besoins. 

Dans la majorité des cas, la migration d’une base de données vers Managed Instance conserve les paramètres de la base de données et son niveau de compatibilité d’origine. Les paramètres d’origine sont conservés dans la mesure du possible, afin de réduire le risque de certaines dégradations de performances par rapport à votre instance SQL Server source. Si le niveau de compatibilité d’une base de données utilisateur s’élève à 100 voire plus avant la migration, il reste le même après la migration. Si le niveau de compatibilité d’une base de données utilisateur s’élève à 90 avant la migration, dans la base de données mise à niveau, il s’élève à 100, car il s’agit du niveau de compatibilité le plus faible pris en charge dans une instance gérée. Le niveau de compatibilité des bases de données système s’élève à 140. La migration vers Managed Instance étant en fait une migration vers la toute dernière version du moteur de base de données SQL Server, gardez bien à l’esprit que vous devez tester à nouveau les performances de votre charge de travail pour éviter certains problèmes de performances surprenants.

Comme prérequis, assurez-vous d’avoir effectué les activités suivantes :
- Aligner vos paramètres sur Managed Instance avec ceux de l’instance SQL Server source en examinant divers paramètres et configurations d’instance, de base de données et de tempdb. Veillez à ne pas changer de paramètres, tels que les niveaux de compatibilité ou le chiffrement, avant d’exécuter la première comparaison de performances, ou acceptez le risque que certaines nouvelles fonctionnalités activées par vous puissent affecter certaines requêtes. Pour réduire les risques liés à la migration, modifiez le niveau de compatibilité de la base de données uniquement après l’analyse des performances.
- Implémentez les [Conseils et bonnes pratiques en matière de stockage pour le niveau de service Usage général](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525), telles que la préallocation de la taille des fichiers en vue d’obtenir de meilleures performances.
- Découvrez les [principales différences d’environnement pouvant entraîner des écarts de performances entre Managed Instance et SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) et identifiez les risques pouvant avoir une incidence sur les performances.
- Veillez à conserver les options Magasin des requêtes et Réglage automatique activées sur votre instance Managed Instance. Ces fonctions vous permettent de mesurer les performances des charges de travail, et de corriger automatiquement les problèmes de performances potentiels. Apprenez à utiliser la fonction Magasin des requêtes en tant qu’outil idéal pour obtenir des informations sur les performances des charges de travail, avant et après la modification du niveau de compatibilité des bases de données, comme indiqué dans [Maintenir la stabilité des performances pendant la mise à niveau vers une version plus récente de SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Dès que vous avez préparé l’environnement qui est comparable autant que possible à votre environnement local, vous pouvez commencer à exécuter votre charge de travail et mesurer les performances. Le processus de mesure doit comporter les mêmes paramètres que ceux que vous avez mesurés [pendant la création des performances de référence de vos mesures de charge de travail sur l’instance SQL Server source](#create-performance-baseline).
Ainsi, vous devez comparer les paramètres des performances avec le référentiel et identifier les différences importantes.

> [!NOTE]
> Dans bien des cas, vous ne pouvez pas obtenir des performances correspondant exactement sur Managed Instance et sur SQL Server. Managed Instance est un moteur de base de données SQL Server, mais l’infrastructure et la configuration de la haute disponibilité qui y sont présentes peuvent amener quelques différences. Vous pouvez vous attendre à ce que certaines requêtes soient plus rapides, et d’autres peut-être plus lentes. L’objectif de la comparaison est de vérifier que les performances de charge de travail correspondent dans Managed Instance à celles observées sur SQL Server (en moyenne), et d’identifier s’il existe des requêtes critiques avec les performances qui ne ressemblent pas à vos performances d’origine.

Le résultat de la comparaison des performances peut se résumer ainsi :
- Les performances de charge de travail sur Managed Instance s’alignent sur celles enregistrées sur SQL Server, ou leur sont supérieures. En l’occurrence, vous confirmez de façon satisfaisante que la migration a réussi.
- La majorité des paramètres de performance et les requêtes dans la charge de travail fonctionnent bien, avec quelques exceptions de performances détériorées. Dans ce cas, vous devez identifier les écarts et leur degré d’importance. S’il existe quelques requêtes importantes accusant une détérioration des performances, vous devez chercher à savoir si les plans SQL sous-jacents ont été modifiés, ou si les requêtes ont atteint certaines limites de ressources. Dans ce cas, l’atténuation peut consister à appliquer certains indicateurs sur les requêtes critiques (par exemple, modification du niveau de compatibilité, utilisation de l’estimateur de cardinalité hérité) directement ou à l’aide de repères de plan, à reconstruire ou à créer des statistiques et des index pouvant avoir une incidence sur les plans. 
- La plupart des requêtes sont plus lentes sur Managed Instance, comparé à votre instance SQL Server source. Dans ce cas, essayez d’identifier les principales causes de cette différence, comme l’[atteinte de certaines limites de ressources]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) : limites des E/S, limite de mémoire, limite du débit du journal d’instance, etc. Si aucune limite de ressources ne peut être la cause de cette différence, essayez de changer le niveau de compatibilité de la base de données ou de changer des paramètres de base de données, comme l’estimation de la cardinalité existante, puis redémarrez le test. Examinez les recommandations fournies par les vues de Managed Instance ou du Magasin des requêtes pour identifier les requêtes dont les performances ont diminué.

> [!IMPORTANT]
> Managed Instance dispose de la fonction intégrée de correction automatique du plan, qui est activée par défaut. Cette fonction garantit que les requêtes s’étant déroulées correctement par le passé ne seront pas détériorées à l’avenir. Assurez-vous que cette fonction est activée et que vous avez exécuté la charge de travail suffisamment longtemps avec les anciens paramètres avant de passer aux nouveaux, pour permettre à Managed Instance de découvrir les plans et les performances de référence.

Effectuez la modification des paramètres ou mettez à niveau les niveaux de service pour converger vers la configuration optimale, jusqu’à atteindre les performances de charge de travail convenant à vos besoins.

### <a name="monitor-performance"></a>Analyser les performances

Managed Instance fournit de nombreux outils avancés de surveillance et de résolution des problèmes, et vous devez les utiliser pour surveiller les performances de votre instance. Voici quelques-uns des paramètres que vous devez surveiller :
- L’utilisation de l’UC sur l’instance pour déterminer le nombre de vCores que vous avez approvisionnés convient pour votre charge de travail.
- L’espérance de vie d’une page sur votre Managed Instance pour déterminer [si vous avez besoin de mémoire supplémentaire](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Des statistiques d’attente telles que `INSTANCE_LOG_GOVERNOR` et `PAGEIOLATCH` qui indiquent si vous rencontrez des problèmes d’E/S de stockage, plus particulièrement au niveau Usage général où vous devrez peut-être pré-allouer des fichiers pour obtenir de meilleures performances d’E/S.

## <a name="leverage-advanced-paas-features"></a>Tirer parti de fonctionnalités PaaS avancées

Lorsque vous êtes sur une plateforme complètement managée et que vous avez vérifié la correspondance des performances de charge de travail avec celles de SQL Server, profitez des avantages qui sont fournis automatiquement dans le cadre du service SQL Database. 

Même si vous n’effectuez aucune modification dans Managed Instance lors de la migration, il y a de fortes chances que vous activiez malgré tout quelques nouvelles fonctionnalités lorsque vous exploitez votre instance pour tirer parti des toutes dernières améliorations du moteur de base de données. Certaines modifications ne sont activées qu’une fois le [niveau de compatibilité de la base de données modifié](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Par exemple, vous n’êtes pas obligé de créer des sauvegardes sur une instance gérée : le service les effectue pour vous automatiquement. Vous n’avez plus à vous soucier de la planification, de l’exécution et de la gestion des sauvegardes. Une instance gérée vous offre la possibilité d’effectuer une restauration à partir de n’importe quel point dans le temps compris dans cette période de rétention à l’aide d’une [récupération jusqu’à une date et heure](sql-database-recovery-using-backups.md#point-in-time-restore). De plus, vous n’avez pas à vous soucier de la configuration de la [haute disponibilité](sql-database-high-availability.md) car elle est intégrée.

Pour renforcer la sécurité, prévoyez d’utiliser l’[authentification Azure Active Directory](sql-database-security-overview.md), l’[audit](sql-database-managed-instance-auditing.md), la [détection des menaces](sql-database-advanced-data-security.md), la [sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) et le [masquage dynamique des données](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking).

En plus des fonctionnalités avancées en matière de sécurité et de gestion, Managed Instance fournit un ensemble d’outils perfectionnés pouvant vous aider dans la [supervision et l’optimisation de votre charge de travail](sql-database-monitor-tune-overview.md). [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) vous permet d’analyser un vaste ensemble d’instances Managed Instances et de centraliser la supervision d’un grand nombre d’instances et de bases de données. [Le réglage automatique](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) dans Managed Instance surveille en permanence les performances de vos statistiques d’exécution du plan SQL, et résout automatiquement les problèmes de performances identifiés.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les instances gérées, voir [Qu’est-ce qu’une instance gérée ?](sql-database-managed-instance.md).
- Pour un didacticiel qui inclut une restauration à partir d’une sauvegarde, voir [Créer une instance gérée](sql-database-managed-instance-get-started.md).
- Pour un didacticiel présentant une migration à l’aide de DMS, voir [Migrer votre base de données locale vers une instance gérée à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
