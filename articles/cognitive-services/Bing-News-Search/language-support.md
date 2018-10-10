---
title: Prise en charge linguistique - API Recherche d’actualités Bing
titleSuffix: Azure Cognitive Services
description: Liste des langages naturels, des pays et des régions pris en charge par l’API Recherche d’actualités Bing.
services: cognitive-services
author: MikeDodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 09/25/2018
ms.author: v-gedod
ms.openlocfilehash: af5d0b5664e74d5d8490951a45bffea120f024b9
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435273"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Langages et régions pris en charge par l’API Recherche d’actualités Bing

L’API Recherche d’actualités Bing prend en charge de nombreux pays/régions, dont beaucoup possèdent plusieurs langues. Spécifier un pays/une région avec une requête sert principalement à affiner les résultats de la recherche en fonction des centres d’intérêt dans ce pays/cette région. En outre, les résultats peuvent contenir des liens vers Bing, servant à localiser l’expérience utilisateur de Bing en fonction du pays/de la région ou de la langue spécifiée.

Vous pouvez spécifier un pays/une région à l’aide du paramètre de requête `cc`. Si vous spécifiez un pays/une région, vous devez également spécifier un ou plusieurs codes de langue à l’aide de l’en-tête HTTP `Accept-Language`. Les langues prises en charge varient selon le pays/la région ; elles sont indiquées pour chaque pays/région dans le tableau Marchés.

Vous pouvez également indiquer le marché à l’aide du paramètre de requête `mkt` et d’un code issu du tableau **Marchés**. Le fait d’indiquer un marché spécifie simultanément un pays/une région et une langue par défaut. Dans ce cas, le paramètre de requête `setLang` peut être défini sur un code de langue. Généralement, il s’agit de la même langue que celle spécifiée par `mkt`, sauf si l’utilisateur préfère voir Bing dans une autre langue.

## <a name="supported-markets-for-news-search-endpoint"></a>Marchés pris en charge pour le point de terminaison de recherche d’actualités

Pour le point de terminaison `/news/search`, le tableau suivant répertorie les valeurs de code de marché que vous pouvez utiliser pour spécifier le paramètre de requête `mkt`. Bing retourne uniquement le contenu pour ces marchés. La liste est susceptible d’être modifiée.  

Pour obtenir la liste des codes pays/régions que vous pouvez spécifier dans le paramètre de requête `cc`, consultez [Codes pays](#countrycodes).  

|Pays/région|Langage|Code du marché|  
|---------------------|--------------|-----------------|
|Danemark|Danois|da-DK|
|Autriche|Allemand|de-AT|
|Suisse|Allemand|de-CH|
|Allemagne|Allemand|de-DE|
|Australie|Français|en-AU|
|Canada|Français|en-CA|
|Royaume-Uni|Français|en-GB|
|Indonésie|Français|en-ID|
|Irlande|Français|en-IE|
|Inde|Français|en-IN|
|Malaisie|Français|en-MY|
|Nouvelle-Zélande|Français|en-NZ|
|République des Philippines|Français|en-PH|
|Singapour|Français|en-SG|
|États-Unis|Français|fr-FR|
|Français|général|en-WW|
|Français|général|en-XA|
|Afrique du Sud|Français|en-ZA|
|Argentine|Espagnol|es-AR|
|Chili|Espagnol|es-CL|
|Espagne|Espagnol|es-ES|
|Mexique|Espagnol|es-MX|
|États-Unis|Espagnol|es-US|
|Espagnol|général|es-XL|
|Finlande|Finnois|fi-FI|  
|France|Français|fr-BE|
|Canada|Français|fr-CA|
|Belgique|Néerlandais|nl-BE|
|Suisse|Français|fr-CH|
|France|Français|fr-FR|  
|Italie|Italien|it-IT|
|Hong Kong (R.A.S.)|Chinois traditionnel|zh-HK|  
|Taïwan|Chinois traditionnel|zh-TW|
|Japon|Japonais|ja-JP|  
|Corée du Sud|Coréen|ko-KR|  
|Pays-bas|Néerlandais|nl-NL|  
|République populaire de Chine|Chinois|zh-CN|  
|Brésil|Portugais|pt-br|
|Russie|Russe|ru-RU|  
|Suède|Suédois|sv-SE|  
|Turquie|Turc|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Marchés pris en charge pour le point de terminaison des actualités
Pour le point de terminaison `/news`, le tableau suivant répertorie les valeurs de code de marché que vous pouvez utiliser pour spécifier le paramètre de requête `mkt`. Bing retourne uniquement le contenu pour ces marchés. La liste est susceptible d’être modifiée.  

Pour obtenir la liste des codes pays/régions que vous pouvez spécifier dans le paramètre de requête `cc`, consultez [Codes pays](#countrycodes).  

|Pays/région|Langage|Code du marché|  
|---------------------|--------------|-----------------|
|Danemark|Danois|da-DK|
|Allemagne|Allemand|de-DE|
|Australie|Français|en-AU|
|Royaume-Uni|Français|en-GB|
|États-Unis|Français|fr-FR|
|Français|général|en-WW|
|Chili|Espagnol|es-CL|
|Mexique|Espagnol|es-MX|
|États-Unis|Espagnol|es-US|
|Finlande|Finnois|fi-FI|  
|Canada|Français|fr-CA|
|France|Français|fr-FR|  
|Italie|Italien|it-IT|
|Brésil|Portugais|pt-br|
|République populaire de Chine|Chinois|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Marchés pris en charge pour le point de terminaison des tendances d’actualités
Pour le point de terminaison `/news/trendingtopics`, le tableau suivant répertorie les valeurs de code de marché que vous pouvez utiliser pour spécifier le paramètre de requête `mkt`. Bing retourne uniquement le contenu pour ces marchés. La liste est susceptible d’être modifiée.  

Pour obtenir la liste des codes pays/régions que vous pouvez spécifier dans le paramètre de requête `cc`, consultez [Codes pays](#countrycodes).  

|Pays/région|Langage|Code du marché|  
|---------------------|--------------|-----------------|
|Allemagne|Allemand|de-DE|
|Australie|Français|en-AU|
|Royaume-Uni|Français|en-GB|
|États-Unis|Français|fr-FR|
|Canada|Français|en-CA|
|Inde|Français|en-IN|
|France|Français|fr-FR|
|Canada|Français|fr-CA|
|Brésil|Portugais|pt-br|
|République populaire de Chine|Chinois|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Codes de pays  

Vous trouverez ci-dessous les codes pays/régions que vous pouvez spécifier dans le paramètre de requête `cc`. La liste est susceptible d’être modifiée.  

|Pays/région|Code pays|  
|---------------------|------------------|  
|Argentine|AR|  
|Australie|AU|  
|Autriche|AT|  
|Belgique|BE|  
|Brésil|BR|  
|Canada|CA|  
|Chili|CL|  
|Danemark|DK|  
|Finlande|FI|  
|France|FR|  
|Allemagne|DE|  
|Hong Kong (R.A.S.)|HK|  
|Inde|IN|  
|Indonésie|ID|  
|Italie|IT|  
|Japon|JP|  
|Corée du Sud|KR|  
|Malaisie|MY|  
|Mexique|MX|  
|Pays-bas|NL|  
|Nouvelle-Zélande|NZ|  
|Norvège|NON|  
|République populaire de Chine|CN|  
|Pologne|PL|  
|Portugal|PT|  
|République des Philippines|PH|  
|Russie|RU|  
|Arabie Saoudite|SA|  
|Afrique du Sud|ZA|  
|Espagne|ES|  
|Suède|SE|  
|Suisse|CH|  
|Taïwan|TW|  
|Turquie|TR|  
|Royaume-Uni|GB|  
|États-Unis|FR|

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les points de terminaison de recherche d’actualités Bing, consultez [Référence de l’API de recherche d’actualité v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).