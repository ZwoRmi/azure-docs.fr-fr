---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à SurveyMonkey Enterprise | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SurveyMonkey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a2d9b57-27b5-4d9f-b52c-09b692872cf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce7f40c300a86acd101d1b38cfef4b2af91c4085
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772669"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à SurveyMonkey Enterprise

Dans ce tutoriel, vous allez apprendre à intégrer SurveyMonkey Enterprise à Azure Active Directory (Azure AD). Quand vous intégrez SurveyMonkey Enterprise à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SurveyMonkey Enterprise.
* Permettre à vos utilisateurs de se connecter automatiquement à SurveyMonkey Enterprise avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SurveyMonkey Enterprise pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SurveyMonkey Enterprise prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

## <a name="adding-surveymonkey-enterprise-from-the-gallery"></a>Ajout de SurveyMonkey Enterprise à partir de la galerie

Pour configurer l’intégration de SurveyMonkey Enterprise à Azure AD, vous devez ajouter SurveyMonkey Enterprise à votre liste d’applications SaaS gérées, depuis la galerie.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SurveyMonkey Enterprise** dans la zone de recherche.
1. Sélectionnez **SurveyMonkey Enterprise** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-surveymonkey-enterprise"></a>Configurer et tester l’authentification unique Azure AD pour SurveyMonkey Enterprise

Configurez et testez l’authentification unique Azure AD avec SurveyMonkey Enterprise à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur SurveyMonkey Enterprise associé.

Pour configurer et tester l’authentification unique Azure AD avec SurveyMonkey Enterprise, effectuez les modules suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SurveyMonkey Enterprise](#configure-surveymonkey-enterprise-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SurveyMonkey Enterprise](#create-surveymonkey-enterprise-test-user)** pour obtenir un équivalent de B.Simon dans SurveyMonkey Enterprise lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **SurveyMonkey Enterprise**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer SurveyMonkey Enterprise**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SurveyMonkey Enterprise.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SurveyMonkey Enterprise**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-surveymonkey-enterprise-sso"></a>Configurer l’authentification unique SurveyMonkey Enterprise

Pour configurer l’authentification unique côté **SurveyMonkey Enterprise**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées depuis le portail Azure à l’[équipe du support technique SurveyMonkey Enterprise](mailto:support@selerix.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-surveymonkey-enterprise-test-user"></a>Créer un utilisateur de test SurveyMonkey Enterprise

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans SurveyMonkey Enterprise. Collaborez avec l’ [équipe du support technique de SurveyMonkey Enterprise](mailto:support@selerix.com) pour ajouter les utilisateurs à la plateforme SurveyMonkey Enterprise. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette SurveyMonkey Enterprise dans le panneau d’accès doit vous connecter automatiquement à l’application SurveyMonkey Enterprise pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer SurveyMonkey Enterprise avec Azure AD](https://aad.portal.azure.com/)