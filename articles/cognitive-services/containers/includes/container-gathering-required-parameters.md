---
title: Collecte des paramètres requis
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Paramètres pour tous les conteneurs de Cognitive Services
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 06f68d28d06dec7c9e738ad8cb50ca337fa840be
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101998"
---
## <a name="gathering-required-parameters"></a>Collecte des paramètres requis

Il existe trois paramètres principaux pour tous les conteneurs de Cognitive Services requis. Le contrat de licence utilisateur final (CLUF) doit être présent avec une valeur de `accept`. En outre, une URL de point de terminaison et une clé API sont nécessaires.

> [!NOTE]
> La seule exception à ces trois paramètres obligatoires est lorsque les conteneurs sont considérés comme « hors connexion ». Ces derniers ne signalent pas l’utilisation, ne sont pas mesurés et suivent une méthodologie de facturation différente.

### <a name="endpoint-uri-endpoint_uri"></a>URL du point de terminaison `{ENDPOINT_URI}`

La valeur URI du **point de terminaison** est disponible sur la page *Vue d’ensemble* du portail Azure de la ressource Cognitive Service correspondante. Accédez à la page *Vue d’ensemble*, pointez sur le point de terminaison et une icône `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> s’affiche. Copiez et utilisez si nécessaire.

![Collecter l’URI de point de terminaison pour une utilisation ultérieure](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Clés `{API_KEY}`

Cette clé est utilisée pour démarrer le conteneur et est disponible sur la page Clés de la ressource Cognitive Service correspondante sur le portail Azure. Accédez à la page *Clés*, puis cliquez sur l’icône `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span>.

![Obtenir l’une des deux clés pour une utilisation ultérieure](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Ces clés d’abonnement sont utilisées pour accéder à votre API Cognitive Service. Ne partagez pas vos clés. Stockez-les en toute sécurité, par exemple, à l’aide de Azure Key Vault. Nous vous recommandons également de régénérer ces clés régulièrement. Une seule clé est nécessaire pour effectuer un appel d’API. Lors de la régénération de la première clé, vous pouvez utiliser la deuxième clé pour un accès continu au service.