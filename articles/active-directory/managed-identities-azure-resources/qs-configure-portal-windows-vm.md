---
title: Guide de configuration d’identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant le portail Azure
description: Instructions détaillées pour configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant le portail Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: b73a79676be559ad491bd7bb16691369dd8fa271
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158629"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et à désactiver des identités managées affectées par le système et par l’utilisateur pour une machine virtuelle Azure en utilisant le portail Microsoft Azure. 

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer de ces affectations de contrôle d'accès basé sur les rôles Azure :

    > [!NOTE]
    > Aucune attribution de rôle d'annuaire Azure AD supplémentaire n’est requise.

    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pour activer et supprimer une identité managée affectée par le système sur une machine virtuelle Azure.

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

Dans cette section, vous allez découvrir comment activer et désactiver l’identité affectée par le système pour une machine virtuelle en utilisant le portail Microsoft Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Activer une identité managée affectée par le système lors de la création d’une machine virtuelle

Pour activer l’identité managée affectée par le système pendant la création d’une machine virtuelle, dans la section **Identité** de l’onglet **Gestion**, redéfinissez **Managed Service Identity** sur **Actif**.  

![Activation de l’identité affectée par le système pendant la création d’une machine virtuelle](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Pour créer une machine virtuelle, consultez les Démarrages rapides suivants : 

- [Créer une machine virtuelle Windows avec le portail Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Créer une machine virtuelle Linux avec le portail Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Activer une identité managée affectée par le système sur une machine virtuelle existante

Pour activer l’identité managée affectée par le système sur une machine virtuelle initialement configurée sans elle :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.

2. Accédez à la machine virtuelle souhaitée et sélectionnez **Identité**.

3. Sous **Attribuée par le système**, **État**, sélectionnez **Activé** puis cliquez sur **Enregistrer** :

   ![Capture d’écran de la page Configuration](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Supprimer une identité managée affectée par le système d’une machine virtuelle

Si vous disposez d’une machine virtuelle qui n’a plus besoin d’identité managée affectée par le système :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle. 

2. Accédez à la machine virtuelle souhaitée et sélectionnez **Identité**.

3. Sous **Attribuée par le système**, **État**, sélectionnez **Désactivé** puis cliquez sur **Enregistrer** :

   ![Capture d’écran de la page Configuration](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

 Dans cette section, vous allez découvrir comment ajouter et supprimer une identité managée affectée par l’utilisateur sur une machine virtuelle en utilisant le portail Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Attribuer une identité affectée par l’utilisateur lors de la création d’une machine virtuelle

Actuellement, le portail Microsoft Azure ne prend pas en charge l’attribution d’une identité managée affectée par l’utilisateur lors de la création d’une machine virtuelle. Au lieu de cela, reportez-vous à l’un des articles de démarrage rapide suivants pour créer une machine virtuelle, puis passez à la section suivante pour obtenir des informations détaillées sur l’attribution d’une identité managée affectée par l’utilisateur à la machine virtuelle :

- [Créer une machine virtuelle Windows avec le portail Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Créer une machine virtuelle Linux avec le portail Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Attribuer une identité managée affectée par l’utilisateur à une machine virtuelle existante

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.
2. Accédez à la machine virtuelle souhaitée, cliquez sur **Identité**, **Attribuée par l’utilisateur**, puis sur **\+Ajouter**.

   ![Ajouter une identité managée affectée par l’utilisateur à une machine virtuelle](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Cliquez sur l’identité managée affectée par l’utilisateur que vous souhaitez ajouter à la machine virtuelle, puis cliquez sur **Ajouter**.

    ![Ajouter une identité managée affectée par l’utilisateur à une machine virtuelle](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Supprimer une identité managée affectée par l’utilisateur d’une machine virtuelle

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.
2. Accédez à la machine virtuelle souhaitée, cliquez sur **Identité**, puis sur **Affectée par l’utilisateur**. Cliquez ensuite sur le nom de l’identité managée affectée par l’utilisateur que vous souhaitez supprimer, puis sur **Supprimer** (cliquez sur **Oui** dans le volet de confirmation).

   ![Supprimer une identité managée affectée par l’utilisateur d’une machine virtuelle](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Étapes suivantes

- Utilisez le portail Azure pour accorder à une identité managée de machine virtuelle Azure l’[accès à une autre ressource Azure](howto-assign-access-portal.md).
