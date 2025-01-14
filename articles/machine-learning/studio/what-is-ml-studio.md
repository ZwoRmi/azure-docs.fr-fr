---
title: Qu’est-ce que c’est
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio est un outil de glisser-déplacer qui permet de créer rapidement des modèles à partir d’une bibliothèque prête à l’emploi d’algorithmes et de modules.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: 4ec9cff652bf1badf526d490547ad78de31ac5da
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677993"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio - De quoi s'agit-il ?
Microsoft Azure Machine Learning Studio est un outil collaboratif fonctionnant par glisser-déplacer qui vous permet de générer, tester et déployer des solutions d'analyse prédictive à partir de vos données. Machine Learning Studio publie des modèles en tant que services web pouvant facilement être consommés par des applications personnalisées ou des outils décisionnels tels qu'Excel.

Machine Learning Studio : là où convergent votre connaissance des données, l'analyse prédictive, les ressources cloud et vos données.


## <a name="the-machine-learning-studio-interactive-workspace"></a>Espace de travail interactif de Machine Learning Studio
Pour développer un modèle d’analyse prédictive, vous utilisez généralement les données d’une ou plusieurs sources que vous transformez et analysez par diverses manipulations de données et fonctions statistiques. Vous générez ensuite un ensemble de résultats. Le développement d'un modèle de ce type est un processus itératif. Quand vous modifiez les diverses fonctions et leurs paramètres, vos résultats convergent jusqu'à ce que l'efficacité du modèle formé vous donne satisfaction.

**Azure Machine Learning Studio** offre un espace de travail visuel et interactif qui vous permet de générer, tester et répéter facilement un modèle d'analyse prédictive. Vous faites glisser des ***jeux de données*** et des ***modules*** d’analyse sur un canevas interactif, en les connectant ensemble pour former une ***expérience***, que vous exécutez dans Machine Learning Studio. Pour affiner votre modèle, vous modifiez l’expérience, enregistrez une copie si vous le souhaitez et l’exécutez de nouveau. Quand vous êtes prêt, vous pouvez convertir votre ***expérience de formation*** en une ***expérience prédictive***, puis la publier en tant que ***service web*** afin que votre modèle soit accessible à d’autres.

Aucune programmation n'est nécessaire : il suffit de visualiser la connexion des jeux de données et des modules pour construire votre modèle d'analyse prédictive.

![Diagramme d’Azure Machine Learning Studio : créez des expériences, lisez les données de nombreuses sources, écrivez des données évaluées, écrivez des modèles.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Téléchargez le diagramme de vue d’ensemble de Machine Learning Studio
Téléchargez le diagramme **Vue d’ensemble des capacités de Microsoft Azure Machine Learning Studio** et obtenez une vue d’ensemble des fonctionnalités de Machine Learning Studio. Imprimez le diagramme au format tabloïd (11 x 17 pouces) pour le conserver à portée de main.

**Téléchargez le diagramme ici : [Vue d’ensemble des fonctionnalités de Microsoft Azure Machine Learning Studio](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Vue d’ensemble des fonctionnalités de Microsoft Azure Machine Learning Studio](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Prise en main de Machine Learning Studio
Quand vous ouvrez [Machine Learning Studio](https://studio.azureml.net) pour la première fois, la page **Accueil** s’affiche. À partir de là, vous pouvez afficher la documentation, des vidéos et des webinaires, ainsi que rechercher d’autres ressources précieuses.

Cliquez sur le menu supérieur gauche ![Menu](./media/what-is-ml-studio/menu.png) pour voir apparaître différentes options.
### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
À ce stade, deux options s’offrent à vous : **Accueil**, la page qui s’est affichée au démarrage, et **Studio**.

Cliquez sur **Studio**. Vous êtes dirigé vers **Azure Machine Learning Studio**. Vous êtes invité à vous connecter à l’aide de votre compte Microsoft, ou de votre compte professionnel ou scolaire. Une fois que vous êtes connecté, les onglets suivants apparaissent sur la gauche :

* **PROJETS** - Collections d’expériences, de DataSets, de notebooks et d’autres ressources représentant un projet spécifique
* **EXPÉRIENCES** : expériences que vous avez créées et exécutées ou enregistrées comme brouillons
* **SERVICES WEB** : services que vous avez déployés à partir de vos expériences web
* **NOTEBOOKS** : notebooks Jupyter que vous avez créés
* **JEUX DE DONNÉES** : jeux de données que vous avez téléchargés dans Studio
* **MODÈLES FORMÉS** : modèles que vous avez formés dans les expériences, puis enregistrés dans Studio
* **PARAMÈTRES** : ensemble des paramètres que vous pouvez utiliser pour configurer votre compte et vos ressources.

### <a name="gallery"></a>Galerie
Cliquez sur **Galerie** pour accéder à la **[galerie Azure AI](https://gallery.azure.ai/)** . La galerie est l’endroit où la communauté des développeurs et des chercheurs en science des données peut partager des solutions créées à l’aide des composants de Cortana Intelligence Suite.

Pour plus d’informations sur la galerie, voir [Partager et découvrir des solutions dans la galerie Azure AI](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Composants d'une expérience
Une expérience se compose de jeux de données qui fournissent des données aux modules d'analyse que vous connectez ensemble pour construire un modèle d'analyse prédictive. En particulier, une expérience valide a les caractéristiques suivantes :

* L'expérience comporte au moins un jeu de données et un module
* Il est possible de connecter des jeux de données uniquement à des modules
* Les modules peuvent être connectés à des jeux de données ou à d'autres modules
* Tous les ports d'entrée des modules doivent comporter une connexion au flux de données
* Tous les paramètres obligatoires de chaque module doivent être configurés

Vous pouvez créer une expérience à partir de zéro, ou utiliser un exemple d’expérience existante comme modèle. Pour plus d’informations, consultez [Copier des exemples d’expérience pour créer des expériences d’apprentissage automatique](sample-experiments.md).

Pour obtenir un exemple de création d'une expérience simple, consultez la rubrique [Création d'une expérience simple dans Azure Machine Learning Studio](create-experiment.md).

Pour obtenir une procédure pas à pas expliquant de manière plus complète comment créer une solution d’analyse prédictive, consultez [Développer une solution prédictive avec Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Groupes de données
Un jeu de données représente des données téléchargées dans Machine Learning Studio de façon à les utiliser dans la procédure de modélisation. Machine Learning Studio fournit divers exemples de jeux de données utilisables pour vos expériences ; vous pouvez télécharger vers le serveur d'autres jeux de données si vous en avez besoin. Voici quelques exemples de jeux de données fournis :

* **Données sur la quantité de litres au 100 pour différents véhicules automobiles** : valeurs de quantité de litres au 100 pour des automobiles identifiées par leur nombre de cylindres, leur puissance, etc.
* **Données sur le cancer du sein** : données de diagnostics sur le cancer du sein.
* **Données sur les feux de forêts** : tailles des incendies de forêts au nord-est du Portugal.

Au moment de créer une expérience, vous pouvez effectuer un choix dans la liste des jeux de données disponibles à gauche du canevas.

Pour obtenir une liste des exemples de jeux de données inclus dans Machine Learning Studio, consultez [Utilisation des exemples de jeux de données dans Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Modules
Un module est un algorithme que vous appliquez à vos données. Machine Learning Studio comporte divers modules, allant de fonctions de saisie des données à des procédures de formation, de notation et de validation. Voici quelques exemples de modules fournis :

* [Convertir en ARFF][convert-to-arff] : convertit un jeu de données sérialisé .NET au format ARFF (Attribute-Relation File Format).
* [Statistiques élémentaires de calcul][elementary-statistics] : calcule des statistiques élémentaires (par exemple, moyenne, écart type, etc.).
* [Régression linéaire][linear-regression] : crée un modèle en ligne de régression linéaire basée sur une descente de gradient.
* [Noter le modèle][score-model] : note un modèle de classification ou de régression formé.

Lorsque vous créez une expérience, vous pouvez utiliser la liste des modules à gauche du canevas.

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Quand vous sélectionnez un module dans le canevas, ses paramètres sont affichés dans le volet **Propriétés** à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle.

Pour savoir comment parcourir la vaste bibliothèque d’algorithmes de machine learning disponibles, consultez [Guide pratique pour choisir des algorithmes pour Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Déploiement d'un service web d'analyse prédictive
Une fois votre modèle d'analyse prédictive prêt, vous pouvez le déployer comme un service web directement à partir de Machine Learning Studio. Pour plus de détails sur ce processus, consultez [Déploiement d’un service web Azure Machine Learning](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>En quoi Machine Learning Studio est-il différent du service Azure Machine Learning ?

[Azure Machine Learning service](../service/overview-what-is-azure-ml.md) propose les deux kits SDK **plus** une interface visuelle (préversion) qui vous permet de préparer rapidement les données, d’entraîner puis de déployer des modèles Machine Learning. Cette interface visuelle (préversion) offre une expérience similaire de glisser-déplacer vers Studio. Cependant, contrairement à la plateforme de calcul propriétaires de Studio, l’interface visuelle utilise vos propres ressources de calcul et est entièrement intégré dans Azure Machine Learning service.

Voici une comparaison rapide.

|| Machine Learning Studio | Azure Machine Learning service :<br/>Interface visuelle|
|---| --- | --- |
|| Disponibilité générale (GA) | En préversion|
|Modules pour l’interface| Divers | Jeu initial de modules courants|
|Cibles de calcul d’entraînement| Cible de calcul propriétaire, prise en charge CPU uniquement| Prend en charge le calcul Azure Machine Learning, GPU ou CPU<br/>(Autres calculs pris en charge dans le SDK)|
|Cibles de calcul de déploiement| Format de service web propriétaire, non personnalisable | Options de sécurité d’entreprise et Azure Kubernetes Service. <br/>([Autres calculs](../service/how-to-deploy-and-where.md) pris en charge dans le SDK) |
|Entraînement de modèle automatisé et optimisation des hyperparamètres | Non | Pas encore dans l’interface visuelle. <br/> (Pris en charge dans le SDK et sur le portail Azure). | 

Essayer l’interface visuelle (préversion) avec le [Didacticiel : Prédire le prix de voitures à l’aide de l’interface visuelle](../service/ui-tutorial-automobile-price-train-score.md)

> [!NOTE]
> Les modèles créés dans Studio ne peuvent pas être déployés ni gérés par Azure Machine Learning service. En revanche, les modèles créés et déployés dans l’interface visuelle du service peuvent être gérés via l’espace de travail Azure Machine Learning service.

## <a name="free-trial"></a>Essai gratuit

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Étapes suivantes
Apprenez les principes de base de l’analyse prédictive et de l’apprentissage automatique en suivant un [guide de démarrage rapide](create-experiment.md) pas à pas et en [développant à partir d’exemples](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
