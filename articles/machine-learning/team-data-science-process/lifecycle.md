---
title: Cycle de vie du processus TDSP
description: Étapes nécessaires pour exécuter vos projets de science des données
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 40d1af6e6258b5026853532f7963a76d4fc389db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837882"
---
# <a name="the-team-data-science-process-lifecycle"></a>Cycle de vie du processus TDSP

Le processus TDSP fournit un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie présente les étapes, du début à la fin, que les projets suivent généralement quand ils sont exécutés. Si vous utilisez un autre cycle de vie de science des données comme le processus [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) (Cross Industry Standard Process for Data Mining), la découverte [KDD](https://wikipedia.org/wiki/Data_mining#Process) (Knowledge Discovery in Databases) ou le processus personnalisé de votre organisation, vous pouvez toujours utiliser le processus TDSP basé sur des tâches. 

Ce cycle de vie est conçu pour les projets de science des données destinés à faire partie d’applications intelligentes. Ces applications déploient des modèles d’apprentissage automatique ou d’intelligence artificielle pour l’analytique prédictive. Les projets de science des données exploratoires et les projets d’analytique ad hoc peuvent également tirer parti de l’utilisation de ce processus. Cependant, certaines des étapes décrites ici peuvent ne pas être nécessaires pour ces projets. 

## <a name="five-lifecycle-stages"></a>Cinq étapes de cycle de vie

Le cycle de vie du processus TDSP se compose de cinq phases majeures qui sont exécutées de manière itérative. Ces étapes sont les suivantes :

   1. [Présentation de l’entreprise](lifecycle-business-understanding.md)
   2. [Acquisition et compréhension des données](lifecycle-data.md)
   3. [Modélisation](lifecycle-modeling.md)
   4. [Déploiement](lifecycle-deployment.md)
   5. [Acceptation du client](lifecycle-acceptance.md)

Voici une représentation visuelle du cycle de vie TDSP : 

![Cycle de vie TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Le cycle de vie du processus TDSP est construit comme une suite d’étapes itérées qui fournissent des conseils sur les tâches indispensables à l’utilisation de modèles prédictifs. Vous déployez les modèles prédictifs dans l’environnement de production que vous envisagez d’utiliser pour générer les applications intelligentes. Ce cycle de vie a pour objectif de faire progresser un projet de science des données vers un point final d’engagement clair. La science des données est un exercice de recherche et de découverte. L’aptitude à communiquer les tâches à votre équipe et à vos clients à l’aide d’un ensemble bien défini d’artefacts utilisant des modèles normalisés contribue à éviter les malentendus. L’utilisation de ces modèles augmente également la probabilité d’achèvement réussi d’un projet de science des données complexe.

Pour chaque phase, nous fournissons les informations suivantes :

   * **Objectifs** : objectifs spécifiques.
   * **Marche à suivre** : plan des tâches spécifiques et conseils sur la façon de les exécuter.
   * **Artefacts** : livrables et support pour les produire.

## <a name="next-steps"></a>Étapes suivantes

Nous indiquons des procédures pas à pas complètes qui illustrent toutes les étapes du processus correspondant à des scénarios spécifiques. L’article [Example walkthroughs](walkthroughs.md) (Exemples de procédures pas à pas) contient une liste des scénarios ainsi que des liens et des descriptions de miniatures. Les procédures pas à pas montrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples sur l’exécution de procédures dans les processus TDSP utilisant Azure Machine Learning Studio, consultez [Utilisation du processus de science des données avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
