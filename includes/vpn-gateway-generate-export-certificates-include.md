---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/10/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf384f29bcc82c80c94e1408f535c592b8b728cb
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285736"
---
## <a name="rootcert"></a>Créer un certificat racine auto-signé

Utilisez la cmdlet New-SelfSignedCertificate pour créer un certificat racine auto-signé. Pour obtenir des informations sur des paramètres supplémentaires, consultez [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Sur un ordinateur sous Windows 10 ou Windows Server 2016, ouvrez une console Windows PowerShell avec élévation de privilèges. Ces exemples ne fonctionnent pas avec « Essayez-le » Azure Cloud Shell. Vous devez les exécuter localement.
2. Utilisez l’exemple suivant pour créer le certificat racine auto-signé. L’exemple suivant crée un certificat racine auto-signé nommé « P2SRootCert », automatiquement installé dans « Certificates-Current User\Personal\Certificates ». Vous pouvez afficher le certificat en ouvrant *certmgr.msc* ou *Gérer les certificats utilisateur*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>Générer un certificat client

Chaque ordinateur client qui se connecte à un réseau virtuel avec une connexion de point à site doit avoir un certificat client installé. Vous générez un certificat client à partir du certificat racine auto-signé, puis l’exportez et l’installez. Si le certificat client n’est pas installé, l’authentification échoue. 

Les étapes suivantes vous guident dans la génération d’un certificat client à partir d’un certificat racine auto-signé. Vous pouvez générer plusieurs certificats clients à partir d’un même certificat racine. Lorsque vous générez des certificats clients suivant les étapes ci-dessous, le certificat client est automatiquement installé sur l’ordinateur que vous avez utilisé pour générer le certificat. Si vous souhaitez installer un certificat client sur un autre ordinateur client, vous pouvez exporter le certificat.

Les exemples utilisent la cmdlet New-SelfSignedCertificate pour générer un certificat client qui expire au bout d’un an. Pour obtenir des informations sur des paramètres supplémentaires, telles que la définition d’une valeur d’expiration différente pour le certificat client, consultez [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Exemple 1

Utilisez cet exemple si vous n’avez pas fermé la console PowerShell après avoir créé le certificat racine auto-signé. Cet exemple continue à partir de la section précédente et utilise la variable « $cert » déclarée. Si vous avez fermé la console PowerShell après la création du certificat racine auto-signé, ou que vous créez des certificats clients supplémentaires dans une nouvelle session de console PowerShell, suivez les étapes décrites dans [l’exemple 2](#ex2).

Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ».  Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemple 2

Si vous créez des certificats clients supplémentaires ou que vous n’utilisez pas la même session PowerShell que pour créer votre certificat racine auto-signé, suivez les étapes suivantes :

1. Identifiez le certificat racine auto-signé installé sur l’ordinateur. Cette applet de commande renvoie la liste des certificats installés sur votre ordinateur.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. Recherchez le nom du sujet dans la liste renvoyée, puis copiez l’empreinte qui se trouve à côté dans un fichier texte. Dans l’exemple suivant, il y a deux certificats. Le nom CN est le nom du certificat racine auto-signé à partir duquel vous souhaitez générer un certificat enfant. Dans ce cas, « P2SRootCert ».

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Déclarez une variable pour le certificat racine avec l’empreinte de l’étape précédente. Remplacez THUMBPRINT par l’empreinte du certificat racine à partir duquel vous souhaitez générer un certificat enfant.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Par exemple, avec l’empreinte numérique pour P2SRootCert de l’étape précédente, la variable ressemble à ceci :

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ». Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>Exporter la clé publique du certificat racine (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exporter le certificat racine autosigné et la clé privée pour les stocker (facultatif)

Vous souhaiterez peut-être exporter le certificat racine autosigné et le stocker à des fins de sauvegarde. Si nécessaire, vous pouvez l’installer ultérieurement sur un autre ordinateur et générer d’autres certificats clients. Pour exporter le certificat racine auto-signé au format .pfx, sélectionnez le certificat racine et suivez les étapes décrites dans la section [Exporter un certificat client](#clientexport).

## <a name="clientexport"></a>Exporter le certificat client

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
