---
title: Obtention des langues prises en charge à l’aide de l’API de traduction de texte Translator Text et de PHP | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous obtenez une liste des langues prises en charge pour la traduction, la translittération et la recherche dans le dictionnaire, ainsi que des exemples d’utilisation de l’API de traduction de texte Translator Text avec PHP dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: 95b48f15ffe8cea14f9ffb7612193b819e03f5f3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "43769688"
---
# <a name="quickstart-get-supported-languages-with-php"></a>Démarrage rapide : Obtenir les langues prises en charge avec PHP

Dans ce démarrage rapide, vous obtenez une liste des langues prises en charge pour la traduction, la translittération et la recherche dans le dictionnaire, ainsi que des exemples d’utilisation de l’API de traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

Vous avez besoin de [PHP 5.6.x](http://php.net/downloads.php) pour exécuter ce code.

Pour utiliser l’API de traduction de texte Translator Text, vous avez également besoin d’une clé d’abonnement. Consultez [Comment s’inscrire à l’API de traduction de texte Translator Text](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Requête Langues

Le code suivant permet d’obtenir une liste des langues prises en charge pour la traduction, la translittération et la recherche dans le dictionnaire, ainsi que des exemples, à l’aide de la méthode [Langues](./reference/v3-0-languages.md).

1. Créez un projet PHP dans votre éditeur de code favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
$key = 'ENTER KEY HERE';

$host = "https://api.cognitive.microsofttranslator.com";
$path = "/languages?api-version=3.0";

$output_path = "output.txt";

function GetLanguages ($host, $path, $key) {

    $headers = "Content-type: text/xml\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'GET'
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$result = GetLanguages ($host, $path, $key);

// Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
// We want to avoid escaping any Unicode characters that result contains. See:
// http://php.net/manual/en/function.json-encode.php
$json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
$out = fopen($output_path, 'w');
fwrite($out, $json);
fclose($out);
?>
```

## <a name="languages-response"></a>Réponse Langues

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Explorez l’exemple de code pour ce démarrage rapide et d’autres, y compris la traduction et la translittération, ainsi que d’autres exemples de projets de l’API de traduction de texte Translator Text sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples PHP sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=php)