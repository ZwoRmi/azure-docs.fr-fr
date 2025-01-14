---
title: Joindre une machine virtuelle CoreOS à Azure AD Domain Services | Microsoft Docs'
description: Découvrez comment configurer une machine virtuelle CoreOS et comment la joindre à un domaine managé Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/14/2019
ms.author: iainfou
ms.openlocfilehash: c0c298a9aa0b9d46ec2c7510cdb5c3ba1c8c84af
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075549"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Joindre une machine virtuelle CoreOS à un domaine managé Azure AD Domain Services

Pour permettre aux utilisateurs de se connecter à des machines virtuelles dans Azure en utilisant un ensemble unique d’informations d’identification, vous pouvez joindre les machines virtuelles à un domaine managé Azure Active Directory Domain Services (AD DS). Quand vous joignez une machine virtuelle à un domaine managé Azure AD DS, les comptes d’utilisateurs et les informations d’identification du domaine peuvent être utilisés pour se connecter aux serveurs et les gérer. Les appartenances aux groupes du domaine managé Azure AD DS sont également appliquées pour vous permettre de contrôler l’accès aux fichiers ou aux services sur la machine virtuelle.

Cet article vous montre comment joindre une machine virtuelle CoreOS à un domaine managé Azure AD DS.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, le premier tutoriel [crée et configure une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Créer une machine virtuelle CoreOS et s’y connecter

Si vous disposez d’une machine virtuelle Linux CoreOS dans Azure, connectez-vous-y en utilisant SSH, puis passez à l’étape suivante pour [commencer à configurer la machine virtuelle](#configure-the-hosts-file).

Si vous avez besoin de créer une machine virtuelle Linux CoreOS ou si vous souhaitez créer une machine virtuelle de test dans le cadre de cet article, vous pouvez employer l’une des méthodes suivantes :

* [Portail Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interface de ligne de commande Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Au moment de créer la machine virtuelle, faites attention aux paramètres de réseau virtuel de sorte que la machine virtuelle puisse communiquer avec le domaine managé Azure AD DS :

* Déployez la machine virtuelle dans le réseau virtuel où vous avez activé Azure Active Directory Domain Services ou dans un réseau virtuel appairé.
* Déployez la machine virtuelle dans un sous-réseau différent de celui de votre instance Azure AD Domain Services.

Une fois la machine virtuelle déployée, suivez les étapes pour vous connecter à la machine virtuelle avec SSH.

## <a name="configure-the-hosts-file"></a>Configurer le fichier hosts

Pour que le nom d’hôte de la machine virtuelle soit correctement configuré pour le domaine managé, modifiez le fichier */etc/hosts* et définissez le nom d’hôte :

```console
sudo vi /etc/hosts
```

Dans le fichier *hosts*, mettez à jour l’adresse *localhost*. Dans l’exemple suivant :

* *contoso.com* est le nom de domaine DNS de votre domaine managé Azure AD DS.
* *coreos* est le nom d’hôte de votre machine virtuelle CoreOS que vous joignez au domaine managé.

Mettez à jour ces noms avec vos propres valeurs :

```console
127.0.0.1 coreos coreos.contoso.com
```

Quand vous avez terminé, enregistrez et quittez le fichier *hosts* à l’aide de la commande `:wq` de l’éditeur.

## <a name="configure-the-sssd-service"></a>Configurer le service SSSD

Mettez à jour la configuration SSSD */etc/sssd/sssd.conf*.

```console
sudo vi /etc/sssd/sssd.conf
```

Spécifiez votre propre nom de domaine managé Azure AD DS pour les paramètres suivants :

* *domains* TOUT EN MAJUSCULES
* *[domain/CONTOSO]*  où CONTOSO est écrit TOUT EN MAJUSCULES
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* TOUT EN MAJUSCULES

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = CONTOSO.COM

[domain/CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Joindre la machine virtuelle au domaine managé

Une fois le fichier de configuration SSSD mis à jour, joignez la machine virtuelle au domaine managé.

1. Dans un premier temps, utilisez la commande `adcli info` pour vérifier que vous pouvez afficher les informations sur le domaine managé Azure AD DS. L’exemple suivant obtient des informations pour le domaine *CONTOSO.COM*. Spécifiez votre propre nom de domaine managé Azure AD DS TOUT EN MAJUSCULES :

    ```console
    sudo adcli info CONTOSO.COM
    ```

   Si la commande `adcli info` ne trouve pas votre domaine managé Azure AD DS, examinez les étapes de dépannage suivantes :

    * Vérifiez que le domaine est accessible à partir de la machine virtuelle. Essayez `ping contoso.com` pour voir si une réponse positive est retournée.
    * Vérifiez que la machine virtuelle est déployée dans le réseau virtuel où le domaine managé Azure AD DS est disponible ou dans un réseau virtuel appairé.
    * Vérifiez que les paramètres de serveur DNS du réseau virtuel ont été mis à jour pour pointer vers les contrôleurs de domaine du domaine managé Azure AD DS.

1. Joignez à présent la machine virtuelle au domaine managé Azure AD DS à l’aide de la commande `adcli join`. Spécifiez un utilisateur qui appartient au groupe *AAD DC Administrators*. Si nécessaire, [ajoutez un compte d’utilisateur à un groupe dans Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Là encore, le nom de domaine managé Azure AD DS doit être entré TOUT EN MAJUSCULES. Dans l’exemple suivant, le compte nommé `contosoadmin@contoso.com` est utilisé pour initialiser Kerberos. Entrez votre propre compte d’utilisateur qui est membre du groupe *AAD DC Administrators*.

    ```console
    sudo adcli join -D CONTOSO.COM -U contosoadmin@CONTOSO.COM -K /etc/krb5.keytab -H coreos.contoso.com -N coreos
    ```

    La commande `adcli join` ne retourne pas d’informations dans le cas où la jonction de la machine virtuelle au domaine managé Azure AD DS a abouti.

1. Pour appliquer la configuration de jonction de domaine, démarrez le service SSSD :
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Se connecter à la machine virtuelle à l’aide d’un compte de domaine

Pour vérifier que la machine virtuelle a bien été jointe au domaine managé Azure AD DS, démarrez une nouvelle connexion SSH en utilisant un compte d’utilisateur du domaine. Vérifiez qu’un répertoire de base a été créé et que l’appartenance au groupe du domaine est appliquée.

1. Créez une connexion SSH à partir de votre console. Utilisez un compte de domaine qui appartient au domaine managé en utilisant la commande `ssh -l`, comme `contosoadmin@contoso.com`, puis entrez l’adresse de votre machine virtuelle, par exemple *coreos.contoso.com*. Si vous utilisez Azure Cloud Shell, utilisez l’adresse IP publique de la machine virtuelle plutôt que le nom DNS interne.

    ```console
    ssh -l contosoadmin@CONTOSO.com coreos.contoso.com
    ```

1. Vérifiez maintenant que les appartenances aux groupes sont correctement résolues :

    ```console
    id
    ```

    Vos appartenances aux groupes du domaine managé Azure AD DS doivent s’afficher.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des difficultés à connecter la machine virtuelle au domaine managé Azure AD DS ou à vous connecter avec un compte de domaine, consultez [Résoudre les problèmes de jonction à un domaine](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
