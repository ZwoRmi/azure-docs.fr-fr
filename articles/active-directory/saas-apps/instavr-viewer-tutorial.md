---
title: 'Tutoriel : Intégration d’Azure Active Directory à InstaVR Viewer | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et InstaVR Viewer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.openlocfilehash: c63e7d03c0fc17e9892617aaeca94803c671acea
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194852"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Tutoriel : Intégration d’Azure Active Directory à InstaVR Viewer

Dans ce tutoriel, vous allez apprendre à intégrer InstaVR Viewer à Azure Active Directory (Azure AD).
L’intégration d'InstaVR Viewer à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à InstaVR Viewer.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à InstaVR Viewer (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à InstaVR Viewer, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement pour lequel l’authentification unique InstaVR Viewer est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* InstaVR Viewer prend en charge l'authentification unique initiée par **SP**
* InstaVR Viewer prend en charge l’approvisionnement **Juste-à-temps** des utilisateurs

## <a name="adding-instavr-viewer-from-the-gallery"></a>Ajout d'InstaVR Viewer à partir de la galerie

Pour configurer l’intégration d'InstaVR Viewer à Azure AD, vous devez ajouter InstaVR Viewer, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter InstaVR Viewer à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **InstaVR Viewer**, sélectionnez **InstaVR Viewer** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![InstaVR Viewer dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec InstaVR Viewer, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur InstaVR Viewer associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec InstaVR Viewer, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique InstaVR Viewer](#configure-instavr-viewer-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Créer un utilisateur de test InstaVR Viewer](#create-instavr-viewer-test-user)** pour avoir un équivalent de Britta Simon dans InstaVR Viewer lié à la représentation Azure AD associée.
5. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec InstaVR Viewer, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **InstaVR Viewer**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL InstaVR Viewer](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, entrez une URL au format suivant : `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`.
    
    > [!NOTE]
    > Il n’existe pas de modèle fixe pour l’URL de connexion. Elle est générée lorsque le client InstaVR Viewer procède à l'empaquetage web. Elle est unique pour chaque client et package. Pour obtenir l'URL de connexion exacte, vous devez vous connecter à votre instance InstaVR Viewer et procéder à un empaquetage web.

    b. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`. 
    
    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Vous devez remplacer cette valeur par la valeur de l'identificateur réelle, ce qui est expliqué plus loin dans ce tutoriel.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **certificat (en base64)** et le **fichier de métadonnées de fédération**  en fonction des options définies par rapport à vos besoins, puis enregistrez-les sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadata-certificatebase64.png)

6. Dans la section **Configurer InstaVR Viewer**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-instavr-viewer-single-sign-on"></a>Configurer l'authentification unique InstaVR Viewer

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous au site de votre entreprise InstaVR Viewer en tant qu’administrateur.

2. Cliquez sur l'**icône Utilisateur**, puis sélectionnez **Compte**.

    ![Configuration d'InstaVR Viewer ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Faites défiler jusqu’à la section **Authentification SAML** et effectuez les étapes suivantes :

    ![Configuration d'InstaVR Viewer ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Dans la zone de texte **URL SSO**, collez l’**URL de connexion** que vous avez copiée sur le portail Azure.

    b. Dans la zone de texte **URL de déconnexion**, collez l’**URL de déconnexion** que vous avez copiée sur le portail Azure.

    c. Dans la zone de texte **ID d'entité**, collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure.

    d. Pour charger votre fichier de certificat téléchargé, cliquez sur **Mettre à jour**.

    e. Pour charger votre fichier de métadonnées de fédération téléchargé, cliquez sur **Mettre à jour**.

    f. Copiez la valeur **ID d’entité** et collez-la dans la zone de texte **Identificateur (ID d’entité)** de la section **Configuration SAML de base** du portail Azure.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à InstaVR Viewer.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **InstaVR Viewer**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, entrez et sélectionnez **InstaVR Viewer**.

    ![Lien InstaVR Viewer dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-instavr-viewer-test-user"></a>Créer un utilisateur de test InstaVR Viewer

Dans cette section, un utilisateur appelé Britta Simon est créé dans InstaVR Viewer. InstaVR Viewer prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans InstaVR Viewer, il en est créé un après l’authentification. Si vous rencontrez des problèmes, contactez l'[équipe de support technique InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous au site de votre entreprise InstaVR Viewer en tant qu’administrateur.

2. Dans le volet de navigation de gauche, sélectionnez **Package**, puis **Créer un package web**.

    ![Configuration d'InstaVR Viewer ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Sélectionnez **Télécharger**.

    ![Configuration d'InstaVR Viewer ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Sélectionnez **Ouvrir la page hébergée**, après quoi elle sera redirigée vers Azure AD à des fins de connexion.

    ![Configuration d'InstaVR Viewer ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Entrez vos informations d’identification Azure AD pour vous connecter à Azure AD via SSO.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)