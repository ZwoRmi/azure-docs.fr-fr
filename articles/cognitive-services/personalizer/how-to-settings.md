---
title: Configurer les paramètres - Personalizer
titleSuffix: Azure Cognitive Services
description: La configuration du service porte sur la façon dont le service traite les récompenses, la fréquence à laquelle le service effectue une exploration, la fréquence à laquelle le modèle est réentraîné et la quantité de données stockées.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: diberry
ms.openlocfilehash: bad581fbc53292b5a7c25157ef839e07f33e131e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827893"
---
# <a name="personalizer-settings"></a>Paramètres de Personalizer

La configuration du service porte sur la façon dont le service traite les récompenses, la fréquence à laquelle le service effectue une exploration, la fréquence à laquelle le modèle est réentraîné et la quantité de données stockées.

## <a name="create-personalizer-resource"></a>Créer une ressource Personalizer

Créez une ressource Personalizer pour chaque boucle de rétroaction. 

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Le lien précédent vous permet d'accéder à la page **Créer** du service Personalizer. 
1. Entrez le nom de votre service, puis sélectionnez un abonnement, un emplacement, un niveau tarifaire et un groupe de ressources.
1. Sélectionnez la confirmation, puis **Créer**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Configurer les paramètres du service dans le portail Azure

1. Connectez-vous au [Portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Recherchez votre ressource Personalizer. 
1. Dans la section **Gestion des ressources**, sélectionnez **Paramètres**.

    Avant de quitter le portail Azure, copiez l’une de vos clés de ressource à partir de la page **Clés**. Vous en aurez besoin pour utiliser le [SDK Personalizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Configurer les paramètres de récompense pour la boucle de rétroaction en fonction du cas d’usage

Configurez les paramètres du service pour l’utilisation des récompenses par votre boucle de rétroaction. Les modifications apportées aux paramètres suivants réinitialisent le modèle actuel de Personalizer et le réentraînent avec les 2 derniers jours de données :

![Configurer les paramètres de récompense pour la boucle de rétroaction](media/settings/configure-model-reward-settings.png)

|Paramètre|Objectif|
|--|--|
|Temps d’attente des récompenses|Définit la durée pendant laquelle Personalizer collecte les valeurs des récompenses pour un appel du classement, en commençant au moment où l’appel du classement se produit. Vous définissez cette valeur en vous posant cette question : « Combien de temps Personalizer doit-il attendre les appels des récompenses ? » Toute récompense arrivant après cette fenêtre de temps est journalisée, mais elle n’est pas utilisée pour l’apprentissage.|
|Récompense par défaut|Si aucun appel de récompense n’est reçu par Personalizer pendant la fenêtre Temps d’attente des récompenses associée à un appel du classement, Personalizer affecte la récompense par défaut. Par défaut, et dans la plupart des scénarios, la récompense par défaut est égale à zéro.|
|Agrégation de récompenses|Si plusieurs récompenses sont reçues pour le même appel de l’API de classement, cette méthode d’agrégation est utilisée : **somme** ou **le plus ancien**. « Le plus ancien » sélectionne le score reçu le plus ancien et ignore le reste. C’est utile si vous voulez une seule récompense entre des appels éventuellement en double. |

Après avoir changé ces paramètres, veillez à sélectionner **Enregistrer**.

### <a name="exploration-setting"></a>Paramètre Exploration 

La personnalisation est en mesure de découvrir de nouveaux modèles et de s’adapter aux changements de comportement des utilisateurs au fil du temps en explorant des alternatives. Le paramètre **Exploration** détermine le pourcentage des appels du classement qui reçoivent une réponse donnée via une exploration. 

Les modifications apportées à ce paramètre réinitialisent le modèle actuel de Personalizer et le réentraînent avec les 2 derniers jours de données.

![Le paramètre Exploration détermine le pourcentage des appels du classement qui reçoivent une réponse donnée via une exploration](media/settings/configure-exploration-setting.png)

Après avoir changé ce paramètre, veillez à sélectionner **Enregistrer**.

### <a name="model-update-frequency"></a>Fréquence de mise à jour du modèle

Le dernier modèle, qui a été entraîné à l’aide des appels d’API Reward de chaque événement actif, n’est pas utilisé automatiquement par les appels Rank de Personalizer. La **fréquence de mise à jour des modèles** définit la fréquence à laquelle le modèle utilisé par l’appel Rank est mis à jour. 

Les fréquences élevées de mise à jour des modèles sont utiles lorsque vous souhaitez suivre de près les modifications apportées au comportements des utilisateurs, par exemple, dans le cas de sites d’actualités en direct, de contenu viral ou d’enchères en direct. Dans de tels scénarios, vous pouvez utiliser une fréquence de 15 minutes. Pour la plupart des cas d’usage, une fréquence de mise à jour moins élevée restera efficace. Une mise à jour effectuée toutes les minutes peut se révéler utile lorsque vous déboguez le code d’une application à l’aide de Personalizer, lorsque vous effectuez des démonstrations ou lorsque vous testez interactivement certains aspects de Machine Learning.

![La fréquence de mise à jour du modèle définit la fréquence à laquelle un nouveau modèle Personalizer est réentraîné.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Après avoir changé ce paramètre, veillez à sélectionner **Enregistrer**.

### <a name="data-retention"></a>Conservation des données

**Période de conservation des données** définit le nombre de jours que Personalizer conserve les journaux de données. Les journaux de données du passé sont nécessaires pour effectuer des [évaluations hors connexion](concepts-offline-evaluation.md), qui sont utilisées pour mesurer l’efficacité de Personalizer et pour optimiser la stratégie d’apprentissage.

Après avoir changé ce paramètre, veillez à sélectionner **Enregistrer**.

## <a name="export-the-personalizer-model"></a>Exporter le modèle de Personalizer

Dans la section Gestion des ressources pour **Modèle et stratégie**, passez en revue la création du modèle et la date de la dernière mise à jour, puis exportez le modèle actuel. Vous pouvez utiliser le portail Azure ou les API Personalizer pour exporter un fichier de modèle à des fins d’archivage. 

![Exporter le modèle actuel de Personalizer](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importer et exporter la stratégie d’apprentissage

Dans la section Gestion des ressources pour **Modèle et stratégie**, importez une nouvelle stratégie d’apprentissage ou exportez la stratégie d’apprentissage actuelle.
Vous pouvez obtenir des fichiers de stratégie d’apprentissage à partir d’exportations précédentes ou télécharger les stratégies optimisées découvertes lors des évaluations hors connexion. Les modifications manuelles apportées à ces fichiers affectent les performances de machine learning et la précision des évaluations hors connexion, et Microsoft ne peut pas garantir la précision du machine learning et des évaluations, ni des exceptions de service résultant de stratégies modifiées manuellement.

## <a name="clear-data-for-your-learning-loop"></a>Effacer les données de votre boucle d'apprentissage

1. Sur le portail Azure, pour votre ressource Personalizer, accédez à la page **Modèle et stratégie** et sélectionnez **Effacer les données**.
1. Pour effacer toutes les données et rétablir l'état d'origine de la boucle d'apprentissage, cochez les 3 cases.

    ![Sur le portail Azure, effacez les données de la ressource Personalizer.](./media/settings/clear-data-from-personalizer-resource.png)

    |Paramètre|Objectif|
    |--|--|
    |Données de personnalisation et de récompense consignées.|Ces données de journalisation sont utilisées lors des évaluations hors connexion. Effacez les données si vous réinitialisez votre ressource.|
    |Réinitialisez le modèle Personalizer.|Ce modèle change à chaque nouvelle formation. Cette fréquence de formation est spécifiée à la section **Fréquence du modèle de chargement** de la page **Paramètres**. |
    |Définissez la stratégie d'apprentissage sur le paramètre par défaut.|Si vous avez modifié la stratégie d'apprentissage dans le cadre d'une évaluation hors connexion, la stratégie d'apprentissage d'origine est rétablie.|

1. Sélectionnez **Effacer les données sélectionnées** pour lancer le processus de suppression. L'état est signalé dans les notifications Azure, en haut à droite. 

## <a name="next-steps"></a>Étapes suivantes

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Découvrir la disponibilité dans la région](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
