---
title: Résoudre les problèmes de machines virtuelles Windows 7 dans Windows Virtual Desktop - Azure
description: Apprenez à résoudre les problèmes liés aux machines virtuelles Windows 7 dans un environnement Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: 282a4d166e48a41015cb2f08496fe39419bdcaf7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374258"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Résoudre les problèmes de machines virtuelles Windows 7 dans Windows Virtual Desktop

Utilisez cet article pour résoudre les problèmes rencontrés lors de la configuration des machines virtuelles hôtes de session Windows Virtual Desktop.

## <a name="known-issues"></a>Problèmes connus

Windows 7 sur Windows Virtual Desktop ne prend pas en charge les fonctionnalités suivantes :

- Applications virtualisées (RemoteApps)
- Redirection de fuseau horaire
- Mise à l'échelle PPP automatique

Sous Windows 7, Windows Virtual Desktop peut uniquement virtualiser l'intégralité des bureaux.

Bien que la mise à l'échelle PPP automatique ne soit pas prise en charge, vous pouvez manuellement modifier la résolution sur votre machine virtuelle en cliquant avec le bouton droit de la souris sur l'icône du client Bureau à distance et en sélectionnant **Résolution**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Error: Impossible d'accéder au groupe d'utilisateurs du Bureau à distance

Si Windows Virtual Desktop ne parvient pas à vous trouver ou à trouver les informations d'identification de vos utilisateurs dans le groupe d'utilisateurs du Bureau à distance, l'un des messages d'erreur suivants peut s'afficher :

- « Cet utilisateur n'est pas membre du groupe d'utilisateurs du Bureau à distance »
- « Vous devez disposer des autorisations nécessaires pour vous connecter via les services Bureau à distance »

Pour y remédier, ajoutez l'utilisateur au groupe d'utilisateurs du Bureau à distance :

1. Ouvrez le portail Azure.
2. Sélectionnez la machine virtuelle sur laquelle le message d'erreur est apparu.
3. Sélectionnez **Exécuter une commande**.
4. Exécutez la commande suivante en remplaçant `<username>` par le nom de l'utilisateur que vous souhaitez ajouter :
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```