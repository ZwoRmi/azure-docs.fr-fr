---
title: Certificats et l’environnement Azure App Service
description: Explique de nombreux points sur les certificats dans un environnement ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.openlocfilehash: 3d417d560d8a88100f31def27c7db5f9b2493062
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167672"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificats et l’environnement App Service 

L’environnement ASE (App Service Environment) est un déploiement du service Azure App Service qui s’exécute dans votre réseau virtuel Azure (VNet). Il peut être déployé avec un point de terminaison d’application accessible par Internet ou un point de terminaison d’application qui se trouve dans votre réseau virtuel. Si vous déployez l’environnement ASE avec un point de terminaison accessible par Internet, ce déploiement est appelé ASE externe. Si vous déployez l’environnement ASE avec un point de terminaison de votre réseau virtuel, ce déploiement est appelé ASE ILB. Vous pouvez en savoir plus sur l’environnement ASE ILB dans le document [Créer et utiliser un équilibreur de charge interne avec un environnement App Service](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

L’environnement ASE est un système monolocataire. De ce fait, il y a des fonctionnalités qui sont disponibles uniquement avec un environnement ASE et qui ne le sont pas dans le service App Service multilocataire. 

## <a name="ilb-ase-certificates"></a>Certificats ASE ILB 

Si vous utilisez un environnement ASE externe, vous pouvez accéder à vos applications sur [nomapp].[nomase].p.azurewebsites.net. Par défaut, tous les environnements ASE, même les environnements ASE ILB, sont créés avec des certificats qui suivent ce format. Si vous avez un environnement ASE ILB, vous pouvez accéder aux applications en fonction du nom de domaine que vous spécifiez quand vous créez l’environnement ASE ILB. Pour que les applications prennent en charge SSL, vous devez charger des certificats. Pour disposer d’un certificat SSL valide, vous pouvez recourir à des autorités de certification internes, acheter un certificat à un émetteur externe, ou utiliser un certificat auto-signé. 

Deux options vous permettent de configurer des certificats avec votre environnement ASE ILB.  Vous pouvez définir un certificat par défaut avec caractères génériques pour l’environnement ASE ILB ou définir des certificats sur chaque application web de l’environnement ASE.  Quel que soit votre choix, les attributs de certificat suivants doivent être configurés correctement :

- **Objet** : Cet attribut doit être défini avec *.[votre-domaine-racine-ici] pour un certificat ASE ILB avec caractères génériques. Si vous créez le certificat pour votre application, celui-ci doit se présenter ainsi : [nomapp].[votre-domaine-racine-ici]
- **Autre nom de l’objet :** Cet attribut doit inclure à la fois *.[votre-domaine-racine-ici] et *.scm.[votre-domaine-racine-ici] pour le certificat ASE ILB avec caractères génériques. Si vous créez le certificat pour votre application, celui-ci doit se présenter ainsi : [nomapp].[votre-domaine-racine-ici] et [nomapp].scm.[votre-domaine-racine-ici].

Troisième variante, vous pouvez créer un certificat ASE ILB qui inclut les noms de toutes vos applications dans l’autre nom de l’objet du certificat au lieu d’utiliser une référence avec caractères génériques. Le problème avec cette méthode est que vous devez connaître à l’avance les noms des applications que vous placez dans l’environnement ASE ou que vous devez mettre à jour le certificat ASE ILB au fur et à mesure.

### <a name="upload-certificate-to-ilb-ase"></a>Charger le certificat dans l’environnement ASE ILB 

Une fois que vous avez créé un environnement ASE ILB dans le portail, vous devez définir le certificat pour cet environnement. Tant que le certificat n’est pas défini, l’environnement ASE affiche une bannière indiquant que le certificat n’a pas été défini.  

Le certificat que vous chargez doit être un fichier .pfx. Une fois que le certificat est chargé, l’environnement ASE procède à une opération d’échelle pour définir le certificat. 

Vous ne pouvez pas créer l’environnement ASE et charger le certificat en une seule action dans le portail ni même dans un modèle. Vous pouvez charger le certificat à l’aide d’un modèle dans une action séparée, comme décrit dans le document [Créer un environnement ASE à partir d’un modèle](./create-from-template.md).  

Si vous souhaitez créer rapidement un certificat auto-signé pour le test, vous pouvez utiliser le code suivant de PowerShell :

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     


## <a name="application-certificates"></a>Certificats d’application 

Les applications qui sont hébergées dans un environnement ASE peuvent utiliser les fonctionnalités de certificat orientées application qui sont disponibles dans le service App Service multilocataire. Ces fonctionnalités incluent :  

- Certificats SNI 
- SSL basé sur IP, uniquement pris en charge avec un environnement ASE externe.  Un environnement ASE ILB ne prend pas en charge SSL basé sur IP.
- Certificats hébergés KeyVault 

Les instructions pour charger et gérer ces certificats sont disponibles dans le tutoriel sur SSL App Service https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl.  Si vous vous contentez de configurer des certificats pour faire correspondre un nom de domaine personnalisé que vous avez affecté à votre application web, ces instructions suffisent. Si vous chargez le certificat pour une application web ASE ILB avec le nom de domaine par défaut, spécifiez le site scm dans l’autre nom de l’objet du certificat, comme indiqué plus haut. 

## <a name="tls-settings"></a>Paramètres TLS 

Vous pouvez configurer le paramètre TLS au niveau de l’application.  

## <a name="private-client-certificate"></a>Certificat client privé 

Un cas d’usage courant consiste à configurer votre application en tant que client dans un modèle client-serveur. Si vous sécurisez votre serveur avec un certificat d’autorité de certification privé, vous devez charger le certificat client sur votre application.  Les instructions suivantes permettent de charger les certificats dans le truststore des travailleurs sur lesquels votre application s’exécute. Si vous chargez le certificat sur une seule application, vous pouvez l’utiliser avec vos autres applications du même plan App Service sans avoir à recharger le certificat.

Pour charger le certificat sur votre application dans votre environnement ASE :

1. Générez un fichier *.cer* pour votre certificat. 
2. Accédez à l’application qui nécessite le certificat dans le portail Azure.
3. Accédez aux paramètres SSL de l’application. Cliquez sur Charger le certificat. Sélectionnez Public. Sélectionnez Ordinateur local. Donnez-lui un nom. Recherchez et sélectionnez votre fichier *.cer*. Sélectionnez Charger. 
4. Copiez l’empreinte numérique.
5. Accédez à Paramètres de l’application. Créez le paramètre d’application WEBSITE_LOAD_ROOT_CERTIFICATES avec l’empreinte numérique comme valeur. Si vous avez plusieurs certificats, vous pouvez les placer dans le même paramètre, séparés par des virgules et sans espace comme 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Le certificat est accessible par toutes les applications du même plan App Service que l’application qui a configuré ce paramètre. Si vous avez besoin qu’il soit disponible pour les applications d’un autre plan App Service, vous devez répéter l’opération Paramètres de l’application dans une application du plan App Service concerné. Pour vérifier que le certificat est défini, accédez à la console Kudu et émettez cette commande dir cert:\localmachine\root dans la console de débogage de PowerShell. 

Pour effectuer le test, vous pouvez créer un certificat auto-signé et générer un fichier *.cer* avec le code PowerShell suivant : 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
