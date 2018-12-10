---
title: Création de filtres avec le kit SDK .NET Azure Media Services
description: Cette rubrique décrit comment créer des filtres pour que votre client puisse les utiliser pour diffuser des sections spécifiques d'un flux. Media Services crée des manifestes dynamiques pour obtenir cette diffusion sélective.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 23e83b98288f9ac1fe23e01b9a91d81daa3b0f47
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632379"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Créer des filtres à l’aide du kit SDK .NET Media Services

Quand vous transmettez votre contenu à un client (événements de streaming en direct ou vidéo à la demande), le fichier manifeste de l’actif multimédia par défaut ne permet peut-être pas au client d’interagir avec le contenu comme il le voudrait. Avec Azure Media Services, vous pouvez définir des filtres de compte et des filtres d’actif multimédia à appliquer à votre contenu. Pour plus d’informations, consultez [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

Cette rubrique explique comment utiliser le SDK .NET Media Services pour définir un filtre pour un actif multimédia Vidéo à la demande, et créer des [filtres de compte](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) et des [filtres d’actif multimédia](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

## <a name="prerequisites"></a>Prérequis 

- Lire la rubrique [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 
- Obtenir les informations nécessaires pour [accéder aux API](access-api-cli-how-to.md)
- Lire la rubrique [Charger, encoder et transmettre en continu des vidéos à l’aide d’Azure Media Services](stream-files-tutorial-with-api.md) pour voir comment [démarrer avec le SDK .NET](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Définir un filtre  

Dans .NET, vous configurez les sélections de pistes avec les classes [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) et [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet). 

Le code suivant définit un filtre qui inclut toutes les pistes audio en anglais contenant EC-3 et toutes les pistes vidéo ayant une vitesse de transmission entre 0 et 1 000 000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Language, "en-us", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Créer des filtres de compte

Le code suivant montre comment utiliser .NET pour créer un filtre de compte qui inclut toutes les sélections de pistes [définies ci-dessus](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Créer des filtres d’actif multimédia

Le code suivant montre comment utiliser .NET pour créer un filtre d’actif multimédia qui inclut toutes les sélections de pistes [définies ci-dessus](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="next-steps"></a>Étapes suivantes

[Transmettre des vidéos en streaming](stream-files-tutorial-with-api.md) 

