---
title: 'Démarrage rapide : Collecter des données auprès d’un ordinateur Linux hybride avec Azure Monitor'
description: Dans ce guide de démarrage rapide, vous allez apprendre à déployer l’agent Log Analytics pour les ordinateurs Linux s’exécutant en dehors d’Azure et à activer la collecte de données avec Azure Monitor Logs.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: magoedte
ms.custom: mvc, seo-javascript-september2019
ms.openlocfilehash: 72f50754a28f0bbf5648ae64299d28ff13e2ec31
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703050"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Démarrage rapide : Collecter des données à partir d’un ordinateur Linux dans un environnement hybride avec Azure Monitor

[Azure Monitor](../overview.md) est capable de collecter directement les données de vos ordinateurs Linux physiques ou virtuels de votre environnement dans un espace de travail Log Analytics pour ensuite procéder à une analyse et à une mise en corrélation détaillées. L’installation de l’[agent Log Analytics](../platform/log-analytics-agent.md) permet à Azure Monitor de collecter des données à partir d’un centre de données ou d’un autre environnement cloud. Ce guide de démarrage rapide montre comment configurer et collecter les données de votre serveur Linux en quelques étapes simples. Pour plus d’informations sur les machines virtuelles Linux Azure, consultez [Collecter des données sur les machines virtuelles Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Pour comprendre la configuration prise en charge, consultez [Systèmes d’exploitation Windows pris en charge](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) et [Configuration du pare-feu réseau](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Créer un espace de travail

1. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.

    ![Portail Azure](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. Cliquez sur **Créer**, puis sélectionnez des options pour les éléments suivants :

   * Attribuez un nom au nouvel **Espace de travail Log Analytics** comme *DefaultLAWorkspace*.  
   * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   * Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant qui contient une ou plusieurs machines virtuelles Azure.  
   * Sélectionnez l’**Emplacement** dans lequel vos machines virtuelles sont déployées.  Pour en savoir plus, découvrez dans quelles [régions Log Analytics est disponible](https://azure.microsoft.com/regions/services/).
   * Si vous créez un espace de travail dans un nouvel abonnement créé après le 2 avril 2018, il utilisera automatiquement le plan tarifaire *Par Go* et l’option pour sélectionner un niveau de tarification ne sera pas disponible.  Si vous créez un espace de travail pour un abonnement existant créé avant le 2 avril ou pour un abonnement lié à une inscription EA existante, sélectionnez le niveau tarifaire de votre choix.  Pour plus d’informations sur les différents niveaux proposés, consultez le [détail des tarifs de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Créer le panneau de ressources Log Analytics](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. Une fois que vous avez entré les informations requises dans le volet **Espace de travail Log Analytics**, sélectionnez **OK**.  

Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="obtain-workspace-id-and-key"></a>Obtenir l’ID et la clé d’espace de travail

Avant d’installer l’agent Log Analytics pour Linux, vous devez disposer de l’ID et de la clé d’espace de travail pour votre espace de travail Log Analytics.  Le script du wrapper de l’agent a besoin de ces informations pour configurer correctement l’agent et faire en sorte qu’il puisse communiquer avec Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. En haut à gauche dans le portail Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez **Log Analytics**. Lorsque vous écrivez, au fur et à mesure de votre saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.

2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez l’espace de travail créé précédemment. (Vous l’avez peut-être nommé **DefaultLAWorkspace**.)

3. Sélectionnez **Paramètres avancés** :

    ![Paramètres avancés de Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png) 
 
4. Sélectionnez **Sources connectées**, puis **Serveurs Linux**.

5. Des valeurs figurent à droite d’**ID de l’espace de travail** et de **Clé primaire**. Copiez-collez ces deux valeurs dans votre éditeur favori.

## <a name="install-the-agent-for-linux"></a>Installation de l’agent pour Linux

Les étapes suivantes configurent le programme d’installation de l’agent pour Log Analytics dans Azure et dans le cloud Azure Government.  

>[!NOTE]
>L’agent Log Analytics pour Linux ne peut pas être configuré pour envoyer des rapports à plus d’un espace de travail Log Analytics.  

Si votre ordinateur Linux a besoin d’un serveur proxy pour communiquer avec Log Analytics, la configuration du proxy peut être spécifiée sur la ligne de commande en incluant `-p [protocol://][user:password@]proxyhost[:port]`.  La propriété *proxyhost* accepte un nom de domaine complet ou l’adresse IP du serveur proxy. 

Par exemple : `https://user01:password@proxy01.contoso.com:30443`

1. Pour configurer l’ordinateur Linux en vue d’une connexion à un espace de travail Log Analytics, exécutez la commande suivante en fournissant l’ID d’espace de travail et la clé primaire copiés précédemment. La commande suivante télécharge l’agent, valide sa somme de contrôle et l’installe. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    La commande suivante inclut le paramètre proxy `-p` et un exemple de syntaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Pour configurer l’ordinateur Linux en vue d’une connexion à un espace de travail Log Analytics dans le cloud Azure Government, exécutez la commande suivante en fournissant l’ID d’espace de travail et la clé primaire copiés précédemment. La commande suivante télécharge l’agent, valide sa somme de contrôle et l’installe. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    La commande suivante inclut le paramètre proxy `-p` et un exemple de syntaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Redémarrez l’agent en exécutant la commande suivante : 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Collecter les données d’événements et de performances

Azure Monitor peut collecter les événements du journal Linux Syslog et des compteurs de performances que vous spécifiez en vue d’une analyse et d’un reporting à plus long terme. Il peut également prendre des mesures lorsqu’il détecte une condition particulière. Pour configurer la collecte d’événements à partir de Syslog Linux, ainsi que plusieurs compteurs de performances courants avec lesquels commencer, procédez comme suit.  

1. En bas à gauche du portail Azure, sélectionnez **Plus de services**. Dans la zone de recherche, entrez **Log Analytics**. Lorsque vous écrivez, au fur et à mesure de votre saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.

2. Sélectionnez **Données**, puis **Syslog**.  

3. Vous pouvez ajouter syslog en tapant le nom du journal. Entrez **Syslog**, puis sélectionnez le signe plus **+** .  

4. Dans le tableau, décochez les niveaux de gravité **Info**, **Avis** et **Débogage**. 

5. Sélectionnez **Enregistrer** en haut de la page pour enregistrer la configuration.

6. Sélectionnez **Données de performances Linux** pour activer la collecte des compteurs de performances sur un ordinateur Linux. 

7. Quand vous procédez à la configuration initiale des compteurs de performances Linux pour un nouvel espace de travail Log Analytics, la possibilité vous est offerte de créer rapidement plusieurs compteurs courants. Ils s’affichent avec une case à cocher en regard.

    ![Compteurs de performances Windows par défaut sélectionnés](media/quick-collect-azurevm/linux-perfcounters-default.png)

    Sélectionnez **Appliquer la configuration ci-dessous à mes machines**, puis **Ajouter les compteurs de performances sélectionnés**. Ils sont ajoutés et prédéfinis avec un intervalle d’échantillonnage de collecte de dix secondes.  

8. Sélectionnez **Enregistrer** en haut de la page pour enregistrer la configuration.

## <a name="view-data-collected"></a>Afficher les données collectées

À présent que vous avez activé la collecte de données, exécutons un exemple simple de recherche dans les journaux pour voir certaines données de l’ordinateur cible.  

1. Dans l’espace de travail sélectionné, dans le volet gauche, sélectionnez **Journaux**.

2. Dans la page de requête Journaux, tapez `Perf` dans l’éditeur de requête et sélectionnez **Exécuter**.
 
    ![Recherche dans les journaux Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Par exemple, la requête présentée dans l’image suivante a retourné 10 000 enregistrements de performances. Vos résultats seront bien moins abondants.

    ![Résultat de la recherche dans les journaux Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer l’agent de l’ordinateur Linux ainsi que l’espace de travail Log Analytics.  

Pour supprimer l’agent, exécutez la commande suivante sur l’ordinateur Linux. L’argument *--purge* supprime complètement l’agent et sa configuration.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Pour supprimer l’espace de travail, sélectionnez l’espace de travail Log Analytics que vous avez créé précédemment, puis, dans la page de ressource, sélectionnez **Supprimer**.

![Supprimer la ressource Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous collectez des données opérationnelles et de performances à partir de votre ordinateur Linux local, vous pouvez commencer aisément à explorer et à analyser les données que vous collectez *gratuitement*, et agir en conséquence.  

Pour savoir comment consulter et analyser les données, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Consulter ou analyser les données dans Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
