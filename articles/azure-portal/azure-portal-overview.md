---
title: Vue d’ensemble du portail Azure | Microsoft Docs
description: Découvrez comment naviguer dans le portail Azure et l’utiliser pour gérer vos services
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 6e176a8b16129cd35fc011e14fcb36038f7c0144
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000342"
---
# <a name="azure-portal-overview"></a>Présentation du portail Azure

Cet article présente le portail Azure, identifie les éléments des pages du portail et vous aide à vous familiariser avec l’expérience de gestion sur le portail Azure.

## <a name="what-is-the-azure-portal"></a>Qu’est-ce que le portail Azure ?

Le portail Azure est une console web unifiée qui offre une alternative aux outils en ligne de commande. Avec le portail Azure, vous pouvez gérer votre abonnement Azure via une interface graphique utilisateur. Vous pouvez créer, gérer et superviser tout ce que vous voulez, de simples applications web à des déploiements cloud complexes, créer des tableaux de bord personnalisés pour une vue organisée des ressources et configurer les options d’accessibilité pour optimiser votre expérience.

Le portail Azure est conçu pour assurer résilience et disponibilité continue. Il est présent dans chaque centre de données Azure, ce qui le rend résilient aux pannes individuelles des centres de données et empêche également les ralentissements du réseau en étant proche des utilisateurs. Le portail Azure est mis à jour en permanence et les activités de maintenance ne nécessitent aucun temps d’arrêt.

## <a name="azure-home"></a>Accueil Azure

En tant que nouvel abonné aux services Azure, la première chose que vous voyez après vous être [connecté au portail](https://portal.azure.com) est la page **Accueil Azure**. Cette page compile les ressources qui vous aideront à tirer le meilleur parti de votre abonnement Azure. Nous avons inclus des liens vers des cours en ligne gratuits, de la documentation, des services essentiels et des sites utiles pour rester à jour et gérer les changements pour votre organisation. Pour un accès simple et rapide au travail en cours, nous montrons également la liste des ressources consultées en dernier. Vous ne pouvez pas personnaliser cette page, mais vous pouvez choisir de voir la page **Accueil Azure** ou **Tableau de bord Azure** comme vue par défaut. La première fois que vous vous connectez, une invite en haut de la page vous permet d’enregistrer votre préférence.

![Capture d’écran montrant le sélecteur de la vue par défaut](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Tableau de bord Azure

Les tableaux de bord fournissent une vue ciblée des ressources de votre abonnement qui vous intéressent le plus. Nous vous fournissons un tableau de bord par défaut pour vous aider à démarrer. Vous pouvez personnaliser ce tableau de bord pour avoir dans une même vue les ressources que vous utilisez le plus. Toutes les modifications que vous apportez à la vue par défaut affectent uniquement votre expérience. Toutefois, vous pouvez créer des tableaux de bord supplémentaires pour votre usage personnel, publier vos tableaux de bord personnalisés et les partager avec d’autres utilisateurs de votre organisation. Pour plus d’informations, consultez [Créer et partager des tableaux de bord dans le portail Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Visite guidée du portail

Il est utile de comprendre la disposition de base du portail et comment interagir avec lui. Ici, nous allons présenter les composants de l’interface utilisateur et certains éléments de la terminologie que nous utilisons pour donner des instructions. Pour une visite guidée plus détaillée du portail, consultez la leçon [Parcourir le portail](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

La barre latérale et l’en-tête de page du portail Azure sont des éléments globaux qui sont toujours présents. Ces fonctionnalités persistantes sont « l’interpréteur de commandes » de l’interface utilisateur associée à chaque service ou fonctionnalité, et l’en-tête permet d’accéder à des contrôles globaux. La page de configuration (parfois appelée « panneau ») d’une ressource peut également avoir un volet de gauche qui vous aidera à vous déplacer entre les fonctionnalités.

La figure ci-dessous étiquette les éléments de base du portail Azure, qui sont décrits individuellement dans le tableau suivant.

![Capture d’écran montrant une vue plein écran du portail et les clés des éléments d’interface utilisateur](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Clé|Description
|:---:|---|
|1|En-tête de la page. Apparaît en haut de chaque page du portail et contient les éléments globaux.|
|2| Recherche globale. Utilisez la barre de recherche pour trouver rapidement une ressource spécifique, un service ou de la documentation.|
|3|Contrôles globaux. Comme tous les éléments globaux, ces fonctionnalités sont persistantes sur le portail et incluent : Cloud Shell, filtre d’abonnement, notifications, paramètres du portail, aide et support, et envoyez-nous vos commentaires.|
|4|Votre compte. Consultez les informations sur votre compte, changez d’annuaire, déconnectez-vous ou connectez-vous avec un autre compte.|
|5\.|Barre latérale. La barre latérale est un élément global qui vous aide à naviguer entre les services. Vous pouvez réduire la barre latérale pour mieux vous concentrer sur le volet de travail.|
|6|Contrôle principal pour créer une ressource dans l’abonnement actuel. Recherchez ou parcourez la Place de marché Azure pour trouver le type de ressource que vous souhaitez créer.|
|7|Votre liste de favoris. Ajoutez ou supprimez des favoris à partir de la page **Tous les services**.|
|8|Volet de gauche. De nombreux services incluent un menu dans le volet de gauche pour vous aider à gérer le service.|
|9|Barre de commandes. Les contrôles de la barre de commandes sont contextuels et dépendent de vos actions.|
|10|Barre de navigation. Vous pouvez utiliser les liens de la barre de navigation pour remonter d’un niveau dans votre workflow.|
|11|Volet de travail.  Affiche les détails de la ressource à laquelle vous vous intéressez.|

## <a name="get-started-with-services"></a>Prise en main des services

Si vous êtes un nouvel abonné, vous devez créer une ressource avant de pouvoir la gérer. Sélectionnez **+Créer une ressource** pour voir les services disponibles dans la Place de marché Azure. Vous y trouverez des applications et des services provenant de centaines de fournisseurs, tous certifiés pour s’exécuter sur Azure.

Nous avons prérempli vos favoris dans la barre latérale avec des liens vers des services couramment utilisés.  Pour voir tous les services disponibles, sélectionnez **Tous les services** dans la barre latérale.

> [!TIP]
> La façon la plus rapide de rechercher une ressource, un service ou de la documentation consiste à utiliser *Recherche* dans l’en-tête global. Utilisez les liens de la barre de navigation pour revenir aux pages précédentes.
>
Regardez cette vidéo pour avoir une démonstration de l’utilisation de la recherche globale dans le portail Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Guide pratique pour utiliser la recherche globale dans le portail Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail où exécuter le portail Azure dans [Navigateurs et appareils pris en charge](../azure-portal/azure-portal-supported-browsers-devices.md)

* Restez connecté en déplacement avec l’[application mobile Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
