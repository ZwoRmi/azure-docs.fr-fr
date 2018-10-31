---
title: Guide pratique pour installer et utiliser les vues Log Analytics pour Azure Active Directory (préversion) | Microsoft Docs
description: Découvrez comment installer et utiliser les vues Log Analytics pour Azure Active Directory (préversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b1333eda0fa0bffdf3852f24414511f55acc090e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395485"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Installer et utiliser les vues Log Analytics pour Azure Active Directory

Les vues Log Analytics pour Azure Active Directory facilitent la recherche et l’analyse des journaux d’activité Azure AD dans votre locataire Azure AD. Les journaux d’activité Azure AD sont les suivants :

* Journaux d’audit : le [rapport d’activité des journaux d’audit](concept-audit-logs.md) vous permet de consulter l’historique de toutes les tâches effectuées dans votre locataire.
* Journaux de connexion : le [rapport d’activité de connexion](concept-sign-ins.md) vous permet d’identifier qui a effectué les tâches consignées dans les journaux d’audit.

## <a name="prerequisites"></a>Prérequis

Pour utiliser les vues Log Analytics, vous devez préalablement :

* Créer un espace de travail Log Analytics dans votre abonnement Azure. Découvrez comment [créer un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Effectuer les étapes nécessaires pour [envoyer les journaux d’activité Azure AD dans votre espace de travail Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Télécharger les vues du [dépôt GitHub](https://aka.ms/AADLogAnalyticsviews) sur votre ordinateur local.

## <a name="install-the-log-analytics-views"></a>Installer les vues Log Analytics

1. Accédez à votre espace de travail Log Analytics. Pour cela, accédez d’abord au [portail Azure](https://portal.azure.com) et sélectionnez **Tous les services**. Entrez **Log Analytics** dans la zone de texte, puis sélectionnez **Log Analytics**. Sélectionnez l’espace de travail vers lequel vous avez envoyé les journaux d’activité (voir les prérequis).
2. Sélectionnez **Concepteur de vues**, sélectionnez **Importer**, puis sélectionnez **Choisir un fichier** pour importer les vues à partir de votre ordinateur local.
3. Sélectionnez les vues que vous avez téléchargées (voir les prérequis) et sélectionnez **Enregistrer** pour enregistrer les vues importées. Effectuez cette opération pour la vue **Événements de provisionnement de compte Azure AD**, la vue **Événements de connexion** et la vue **Consentement des utilisateurs**.

## <a name="use-the-views"></a>Utiliser les vues

1. Accédez à votre espace de travail Log Analytics. Pour cela, accédez d’abord au [portail Azure](https://portal.azure.com) et sélectionnez **Tous les services**. Entrez **Log Analytics** dans la zone de texte, puis sélectionnez **Log Analytics**. Sélectionnez l’espace de travail vers lequel vous avez envoyé les journaux d’activité (voir les prérequis).

2. Quand vous êtes dans l’espace de travail, sélectionnez **Récapitulatif de l’espace de travail**. Vous devez normalement avoir les trois vues suivantes :

    * **Événements de provisionnement de compte Azure AD** : cette vue affiche les rapports d’audit de l’activité de provisionnement. Elle indique notamment le nombre de nouveaux utilisateurs attribués et les échecs d’attribution, le nombre d’utilisateurs mis à jour et les échecs de mise à jour, ainsi que le nombre d’utilisateurs dont l’attribution a été annulée et les échecs d’annulation d’attribution.    
    * **Événements de connexion** : cette vue affiche les rapports les plus pertinents relatifs à la supervision de l’activité de connexion, par exemple, les connexions par application, utilisateur et appareil, ainsi qu’un récapitulatif de l’ensemble des connexions effectuées.
    * **Consentement des utilisateurs** : cette vue affiche les rapports relatifs au consentement des utilisateurs, comme le consentement donné par utilisateur, les connexions par les utilisateurs qui ont donné leur consentement ainsi que les connexions par application pour toutes les applications basées sur le consentement. 

3. Sélectionnez la vue qui vous intéresse pour accéder aux différents rapports associés. Vous pouvez également définir des alertes sur les paramètres de rapport de votre choix. Par exemple, définissons une alerte pour chaque erreur de connexion rencontrée. Pour ce faire, sélectionnez la vue **Événements de connexion**, sélectionnez le rapport **Erreurs de connexion rencontrées**, puis sélectionnez **Analytics** pour ouvrir la page de détails, avec la requête actuelle sous-jacente au rapport. 

    ![Détails](./media/howto-install-use-log-analytics-views/details.png)


4. Sélectionnez **Définir une alerte**, puis sélectionnez **Quand la recherche de journal personnalisée est définie sur &lt;logique non définie&gt;** sous la section **Critères d’alerte**. Étant donné que nous voulons être alertés à chaque erreur de connexion, définissez l’option **Seuil** de la logique d’alerte par défaut à **1**, puis sélectionnez **Terminé**. 

    ![Configurer la logique du signal](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Entrez un nom et une description pour l’alerte et définissez le niveau de gravité à **Avertissement**.

    ![Créer une règle](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Sélectionnez le groupe d’actions auquel envoyer l’alerte. Il s’agit généralement d’une équipe que vous souhaitez avertir par e-mail ou SMS, ou d’une tâche qui est automatisée avec des webhooks, des runbooks, des fonctions, des applications logiques ou des solutions ITSM externes. Découvrez comment [créer et gérer des groupes d’action dans le portail Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Sélectionnez **Créer une règle d’alerte** pour créer l’alerte. Vous serez maintenant alerté à chaque erreur de connexion.

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour analyser les journaux d’activité dans Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Bien démarrer avec Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)