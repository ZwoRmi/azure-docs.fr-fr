---
title: Ajouter des bibliothèques Apache Hive durant la création d’un cluster HDInsight - Azure
description: Découvrez comment ajouter des bibliothèques Apache Hive (fichiers jar) à un cluster HDInsight pendant la création du cluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: c3ef5362c4d97b8d805212f9cf813c7bc9c8c18c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059448"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Ajouter des bibliothèques Apache Hive personnalisées lors de la création de votre cluster HDInsight

Découvrez comment précharger des bibliothèques [Apache Hive](https://hive.apache.org/) dans HDInsight. Ce document contient des informations sur l’utilisation d’une action de script pour précharger des bibliothèques durant la création d’un cluster. Les bibliothèques ajoutées à l’aide de la procédure de ce document sont disponibles dans Hive : il n’est pas nécessaire d’utiliser [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) pour les charger.

## <a name="how-it-works"></a>Fonctionnement

Quand vous créez un cluster, vous pouvez utiliser une action de script pour modifier les nœuds de cluster au fur et à mesure qu’ils sont créés. Le script dans ce document accepte un seul paramètre, qui correspond à l’emplacement des bibliothèques. Cet emplacement doit se trouver dans un compte de stockage Azure, et les bibliothèques doivent être stockées sous forme de fichiers jar.

Lors de la création du cluster, le script énumère les fichiers, les copie sur le répertoire `/usr/lib/customhivelibs/` sur le nœud principal et le nœud de travail, puis les ajoute à la propriété `hive.aux.jars.path` dans le fichier `core-site.xml`. Pour les clusters basés sur Linux, il met également à jour le fichier `hive-env.sh` avec l'emplacement des fichiers.

> [!NOTE]  
> L’utilisation de l’action de script dans cet article rend les bibliothèques disponibles dans les scénarios suivants :
>
> * **HDInsight basé sur Linux** : quand vous utilisez un client Hive, **WebHCat** et **HiveServer2**.
> * **HDInsight basé sur Windows** : lorsque vous utilisez le client Hive et **WebHCat**.

## <a name="the-script"></a>Le script

**Emplacement du script**

Pour les **clusters Linux** : [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Pour les **clusters Windows** : [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Configuration requise**

* Les scripts doivent être appliqués à la fois aux **nœuds principaux** et aux **nœuds de travail**.

* Les fichiers jar à installer doivent être stockés dans le stockage d'objets Blob Azure dans un **seul conteneur**.

* Le compte de stockage contenant la bibliothèque de fichiers jar **doit** être lié au cluster HDInsight lors de la création. Il doit s’agir du compte de stockage par défaut ou d’un compte ajouté via la __configuration facultative__.

* Le chemin WASB vers le conteneur doit être spécifié en tant que paramètre à l'action de script. Par exemple, si les fichiers jar sont stockés dans un conteneur nommé **libs** sur un compte de stockage nommé **mystorage**, le paramètre serait **wasb://libs\@mystorage.blob.core.windows.net/** .

  > [!NOTE]  
  > Ce document suppose que vous avez déjà créé un compte de stockage et un conteneur d'objets blob et que vous avez déjà chargé les fichiers dans ce dernier.
  >
  > Si vous n'avez pas créé de compte de stockage, vous pouvez le faire via le [portail Azure](https://portal.azure.com). Vous pouvez ensuite utiliser un utilitaire tel que [Azure Storage Explorer](https://storageexplorer.com/) pour créer un conteneur dans le compte et y télécharger des fichiers.

## <a name="create-a-cluster-using-the-script"></a>Créer un cluster à l'aide du script

> [!NOTE]  
> Les étapes suivantes créent un cluster HDInsight sous Linux. Pour créer un cluster basé sur Windows, sélectionnez **Windows** comme système d'exploitation du cluster lors de sa création et utilisez le script Windows (PowerShell) plutôt que le script Bash.
>
> Vous pouvez également utiliser Azure PowerShell ou le Kit de développement logiciel (SDK) .NET HDInsight pour créer un cluster à l’aide de ce script. Pour plus d’informations sur ces méthodes, consultez [Personnaliser des clusters HDInsight à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrez l’approvisionnement d’un cluster à l’aide de la procédure décrite dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md), mais n’achevez pas l’approvisionnement.

2. Dans la section **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations suivantes :

   * **NOM** : entrez un nom convivial pour l’action de script.

   * **URI DU SCRIPT** : https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **PRINCIPAL** : cochez cette option.

   * **WORKER** : cochez cette option.

   * **ZOOKEEPER** : Laissez ce champ vide.

   * **PARAMÈTRES** : entrez l’adresse WASB du compte de stockage et du conteneur qui contiennent les fichiers jar. Par exemple, **wasb://libs\@mystorage.blob.core.windows.net/** .

3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration.

4. Dans la section **Configuration facultative**, sélectionnez **Comptes de stockage liés**, puis le lien **Ajouter une clé de stockage**. Sélectionnez le compte de stockage qui contient les fichiers jar. Utilisez ensuite les boutons **Sélectionner** pour enregistrer les paramètres et revenir à la section **Configuration facultative**.

5. Pour enregistrer la configuration facultative, utilisez le bouton **Sélectionner** situé en bas de la section **Configuration facultative**.

6. Continuez l’approvisionnement du cluster, comme indiqué dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md).

Lorsque la création du cluster est terminée, vous pouvez utiliser les fichiers jar ajoutés via ce script à partir de Hive, sans avoir à utiliser l’instruction `ADD JAR` .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur l’utilisation de Hive, consultez la rubrique [Utilisation d’Apachee Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
