---
title: Contrôles de sécurité pour Azure ExpressRoute
description: Liste de vérification des contrôles de sécurité pour l’évaluation d’Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886313"
---
# <a name="security-controls-for-azure-expressroute"></a>Contrôles de sécurité pour Azure ExpressRoute

Cet article décrit les contrôles de sécurité intégrés dans Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A |  |
| Prise en charge de l’injection de réseau virtuel| N/A | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Chaque client est contenu dans son propre domaine de routage et acheminé vers son propre réseau virtuel |
| Prise en charge du tunneling forcé| N/A | Via le protocole de passerelle frontière (BGP). |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Supervision, métriques et alertes ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |
| Journalisation et audit du plan de données| Non |   |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Compte de service de passerelle pour Microsoft (GWM) (contrôleur) ; Accès JIT (juste-à-temps) pour le développement et l’exploitation. |
| Authorization|  OUI |Compte de service de passerelle pour Microsoft (GWM) (contrôleur) ; Accès JIT (juste-à-temps) pour le développement et l’exploitation. |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  N/A | ExpressRoute ne stocke pas les données client. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | N/A |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Non | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS. |


## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Via le fournisseur de ressources réseau (NRP). |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).