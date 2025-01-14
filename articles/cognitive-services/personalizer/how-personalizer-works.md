---
title: Fonctionnement de Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer utilise le machine learning pour découvrir quelle action utiliser dans un contexte donné. Chaque boucle d’apprentissage a un modèle qui est entraîné exclusivement sur les données que vous avez envoyées via des appels de l’API de classement (Rank) et de récompense (Reward). Chaque boucle d’apprentissage est complètement indépendante des autres.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.openlocfilehash: 7c163dacae24749dbe309bca33bac016a3be7aa5
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002891"
---
# <a name="how-personalizer-works"></a>Fonctionnement de Personalizer

Personalizer utilise le machine learning pour découvrir quelle action utiliser dans un contexte donné. Chaque boucle d’apprentissage a un modèle qui est entraîné exclusivement sur les données que vous avez envoyées via des appels de l’API de **classement** (Rank) et de **récompense** (Reward). Chaque boucle d’apprentissage est complètement indépendante des autres. Créez une boucle d’apprentissage pour chaque partie ou chaque comportement de votre application que vous voulez personnaliser.

Pour chaque boucle, **appelez l’API de classement** en fonction du contexte actuel, avec :

* Liste des actions possibles : éléments de contenu à partir desquels sélectionner l’action à classer en premier.
* Liste des [caractéristiques du contexte](concepts-features.md) : les données contextuellement pertinentes, comme l’utilisateur, le contenu et le contexte.

L’API de **classement** décide d’utiliser :

* _Exploiter_ : Le modèle actuel pour déterminer la meilleure action en fonction des données antérieures.
* _Explorer_ : Sélectionne une action différente de l’action classée en premier.

L’API de **récompense** :

* Collecte des données pour entraîner le modèle en enregistrant les caractéristiques et les scores de récompense de chaque appel du classement.
* Utilise ces données pour mettre à jour le modèle en fonction des paramètres spécifiés dans la _stratégie d’apprentissage_.

## <a name="architecture"></a>Architecture

L’illustration suivante montre l’architecture du flux des appels des API de classement et de récompense :

![texte de remplacement](./media/how-personalizer-works/personalization-how-it-works.png "Fonctionnement de la personnalisation")

1. Personalizer utilise un modèle d’intelligence artificielle interne pour déterminer le classement de l’action.
1. Le service décide d’exploiter le modèle actuel ou d’explorer de nouveaux choix pour le modèle.  
1. Le résultat du classement est envoyé à EventHub.
1. Quand Personalizer reçoit la récompense, elle est envoyée à EventHub. 
1. Le classement et la récompense sont mis en corrélation.
1. Le modèle d’intelligence artificielle est mis à jour en fonction des résultats de la corrélation.
1. Le moteur d’inférence est mis à jour avec le nouveau modèle. 

## <a name="research-behind-personalizer"></a>Recherches derrière Personalizer

Personalizer est basé sur la science et la recherche de pointe dans le domaine de l’[apprentissage par renforcement](concepts-reinforcement-learning.md), notamment sur des articles, des activités de recherche et des domaines en cours d’exploration de Microsoft Research.

## <a name="terminology"></a>Terminologie

* **Boucle d’apprentissage** : Vous pouvez créer une boucle d’apprentissage pour chaque partie de votre application qui peut tirer parti de la personnalisation. Si vous avez plusieurs expériences à personnaliser, créez une boucle pour chacune d’elles. 

* **Actions** : Les actions sont les éléments de contenu, comme des produits ou des promotions, parmi lesquels choisir. Personalizer choisit l’action à montrer en premier à vos utilisateurs, qui s’appelle l’_action de récompense_, via l’API de classement. Chaque action peut avoir des caractéristiques envoyées avec la demande de classement.

* **Context** : Pour fournir un classement plus précis, fournissez des informations sur votre contexte, par exemple :
    * Votre utilisateur.
    * L’appareil sur lequel il travaille. 
    * L’heure actuelle.
    * D’autres données relatives à la situation actuelle.
    * L’historique des données sur l’utilisateur ou sur le contexte.

    Votre application spécifique peut avoir des informations de contexte différentes. 

* **[Caractéristiques](concepts-features.md)**  : Une unité d’informations sur un élément de contenu ou un contexte utilisateur.

* **Récompense** : Une mesure de la façon dont l’utilisateur a répondu à l’action retournée par l’API de classement, sous la forme d’un score compris entre 0 et 1. La valeur de 0 à 1 est définie par votre logique métier, en fonction de la façon dont le choix a aidé à atteindre vos objectifs métier de personnalisation. 

* **Exploration** : Le service Personalizer effectue une exploration quand, au lieu de retourner la meilleure action, il choisit une autre action pour l’utilisateur. Le service Personalizer évite les dérives et la stagnation, et peut s’adapter au comportement actuel de l’utilisateur en effectuant une exploration. 

* **Durée de l’expérience** : Temps pendant lequel le service Personalizer attend une récompense, à partir du moment où l’appel du classement s’est produit pour cet événement.

* **Événements inactifs** : Un événement inactif est un événement où vous avez appelé le classement, mais où vous n’êtes pas sûr que l’utilisateur verra jamais le résultat, en raison des décisions de l’application cliente. Les événements inactifs vous permettent de créer et de stocker les résultats de la personnalisation, puis de décider de les abandonner plus tard sans affecter le modèle de machine learning.

* **Modèle** : Un modèle Personalizer capture toutes les données apprises sur le comportement des utilisateurs, obtenant les données d’entraînement de la combinaison des arguments que vous envoyez aux appels de classement et de récompense, et avec un comportement d’entraînement déterminé par la stratégie d’apprentissage. 

* **Stratégie d’apprentissage** : La façon dont Personalizer entraîne un modèle sur chaque événement est déterminée par certains métaparamètres qui affectent le fonctionnement des algorithmes Machine Learning. Les nouvelles boucles de Personalizer commencent par une stratégie d’apprentissage par défaut, ce qui peut donner des performances modérées. Lors de l’exécution des [Évaluations](concepts-offline-evaluation.md), Personalizer peut créer de nouvelles stratégies d’apprentissage spécifiquement optimisées pour les cas d’utilisation de votre boucle. Personalizer s’exécutera considérablement mieux avec les stratégies optimisées pour chaque boucle spécifique, générées pendant l’évaluation.

## <a name="example-use-cases-for-personalizer"></a>Exemple de cas d’utilisation pour Personalizer

* Clarification et désambiguïsation de l’intention : aidez vos utilisateurs à vivre une meilleure expérience lorsque leur intention n’est pas claire en fournissant une option personnalisée pour chaque utilisateur.
* Suggestions par défaut pour les menus et les options : demandez au bot de suggérer l’élément le plus probable de manière personnalisée, avant de présenter un menu impersonnel ou une liste d’alternatives.
* Caractéristiques et ton du bot : pour les bots pouvant varier le ton, la verbosité et le style d’écriture, envisagez de personnaliser ces caractéristiques.
* Contenu des notifications et alertes : choisissez le texte à utiliser pour les alertes afin d’impliquer davantage les utilisateurs.
* Minutage des notifications et alertes : personnalisez l’apprentissage du moment opportun pour envoyer des notifications aux utilisateurs afin de les impliquer davantage.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Comment utiliser Personalizer dans une application web

L’ajout d’une boucle à une application web inclut les étapes suivantes :

* Déterminer l’expérience à personnaliser, les actions et fonctionnalités disponibles, les fonctionnalités à utiliser selon le contexte et la récompense à donner.
* Ajouter une référence au Kit de développement logiciel (SDK) Personnalisation dans votre application.
* Appeler l’API de classement lorsque vous êtes prêt à personnaliser.
* Stocker l’eventId. Vous enverrez une récompense avec l’API de récompense ultérieurement.
1. Appelez Activer pour l’événement une fois que vous êtes certain que l’utilisateur a vu votre page personnalisée.
1. Attendez que l’utilisateur sélectionne un contenu classé. 
1. Appelez l’API de récompense pour spécifier la qualité de la sortie de l’API de classement.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Comment utiliser Personalizer avec un bot conversationnel

Cet exemple montre comment utiliser la fonctionnalité Personnalisation pour faire une suggestion par défaut au lieu d’envoyer à l’utilisateur une série de menus ou de choix à chaque fois.

* Téléchargez le [code](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) pour cet exemple.
* Configurez votre solution de bot. Veillez à publier votre application LUIS. 
* Gérez les appels des API de classement et de récompense pour le bot.
    * Ajoutez du code pour gérer le traitement des intentions LUIS. Si la valeur **Aucune** est renvoyée en tant que première intention ou si le score de la première intention est inférieur au seuil de votre logique métier, envoyez la liste des intentions à Personalizer pour classer les intentions.
    * Affichez la liste des intentions à l’utilisateur sous la forme de liens sélectionnables, avec la première intention en haut du classement dans la réponse de l’API de classement.
    * Capturez la sélection de l’utilisateur et envoyez-la dans l’appel de l’API de récompense. 

### <a name="recommended-bot-patterns"></a>Modèles bot recommandés

* Faites des appels de l’API de classement de Personalizer chaque fois qu’il est nécessaire de lever une ambiguïté, par opposition à la mise en cache des résultats pour chaque utilisateur. Le résultat de la désambiguïsation de l’intention peut changer au fil du temps pour une personne, et le fait de permettre à l’API de classement d’explorer les changements accélérera l’apprentissage global.
* Choisissez une interaction commune à de nombreux utilisateurs afin de disposer de suffisamment de données à personnaliser. Par exemple, des questions préliminaires peuvent être plus adaptées que des clarifications demandées à un stade de la conversation que de rares utilisateurs atteignent.
* Utiliser le classement par ordre de priorité des appels d’API pour activer des conversations de la « première suggestion est bonne », où l’utilisateur se fait demander : « vous aimeriez X ? » ou « vouliez vous dire X ? » et l’utilisateur peut simplement confirmer ; par opposition aux options de l’utilisateur à partir d’un menu. Par exemple, si l’utilisateur dit « Je souhaiterais un café », le bot peut lui demander « Voulez-vous un double espresso ? ». De cette façon, le signal de récompense est également fort parce qu’il se rapporte directement à la suggestion.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Comment utiliser Personalizer avec une solution de recommandation

Utilisez votre moteur de recommandations pour filtrer un vaste catalogue afin de n’en retenir que quelques éléments qui peuvent ensuite être présentés comme une trentaine d’actions possibles envoyées à l’API de classement.

Vous pouvez utiliser des moteurs de recommandation avec Personalizer pour effectuer les opérations suivantes :

* Configurer la [solution de recommandation](https://github.com/Microsoft/Recommenders/). 
* Lorsque vous affichez une page, appeler le modèle de recommandation pour obtenir une courte liste de recommandations.
* Appeler la fonctionnalité Personnalisation pour classer la sortie d’une solution de recommandation.
* Envoyer des commentaires sur l’action de l’utilisateur avec l’appel de l’API de récompense.


## <a name="pitfalls-to-avoid"></a>Pièges à éviter

* N’utilisez pas Personalizer lorsque le comportement personnalisé n’est pas une chose commune à tous les utilisateurs, mais plutôt une chose qui devrait être retenue pour un utilisateur spécifique, ou qui figure dans une liste d’alternatives spécifique d’un utilisateur. Par exemple, utiliser Personalizer pour suggérer une commande de pizza à partir d’une liste de 20 options de menu possibles est utile, mais le contact à appeler à partir de la liste de contacts de l’utilisateur quand celui-ci a besoin d’aide pour garder les enfants (comme « Grand-mère ») n’est pas un élément personnalisable dans votre base d’utilisateurs.


## <a name="adding-content-safeguards-to-your-application"></a>Ajout de protections de contenu à votre application

Si votre application autorise des variations importantes du contenu affiché aux utilisateurs, et qu’une partie de ce contenu peut être préjudiciable ou inappropriée pour certains utilisateurs, vous devez vous assurer à l’avance que les protections adéquates sont en place pour empêcher les utilisateurs de voir du contenu inapproprié. la suivante :
    * Obtenir la liste des actions à classer.
    * Filtrer les celles qui ne sont pas viables pour le public.
    * Classer uniquement les actions viables.
    * Afficher à l’utilisateur l’action en tête du classement.

Dans certaines architectures, la séquence ci-dessus peut être difficile à implémenter. Dans ce cas, il existe une approche alternative pour l’implémentation des protections après le classement, mais il convient de prendre des dispositions pour que les actions qui ne relèvent pas de la protection ne soient pas utilisées pour entraîner le modèle de Personalizer. La marche à suivre est la suivante.

* Répertorier les actions à classer avec l’apprentissage désactivé.
* Classer les actions.
* Vérifier si l’action en tête du classement est viable.
    * Si l’action en tête du classement est viable, activer l’apprentissage pour ce classement, puis l’afficher à l’utilisateur.
    * Si l’action en tête du classement n’est pas viable, ne pas activer l’apprentissage pour ce classement, et décider selon votre propre logique ou sur la base d’une autre approche ce qu’il convient de montrer à l’utilisateur. Même si vous utilisez la deuxième option la mieux classée, n’activez pas l’apprentissage pour ce classement.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Vérification de l’efficacité appropriée de Personalizer

Vous pouvez vérifier périodiquement l’efficacité de Personalizer en effectuant des [évaluations hors connexion](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Étapes suivantes

Comprendre [où vous pouvez utiliser Personalizer](where-can-you-use-personalizer.md).
Effectuer des [évaluations hors connexion](how-to-offline-evaluation.md)
