---
title: 'Didacticiel : Bot Language Understanding C# v4'
titleSuffix: Azure Cognitive Services
description: À l’aide de C#, créez un bot conversationnel intégré avec compréhension de la langue (LUIS). Le bot est créé avec Bot Framework version 4 et le service du bot d’application web Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 0911747da38ed736a79e692fd511e5bfbfaf7439
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772909"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Didacticiel : Utiliser un Web App Bot activé avec Language Understanding dans C#

À l’aide de C#, créez un bot conversationnel intégré avec compréhension de la langue (LUIS). Le bot est conçu avec la ressource Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) et [Bot Framework V4](https://github.com/Microsoft/botbuilder-dotnet).

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer un bot d’application web. Ce processus crée une application LUIS pour vous.
> * Télécharger le projet de bot créé par le service de bot web
> * Démarrer le bot et l’émulateur localement sur votre ordinateur
> * Afficher les résultats d’énoncé dans le bot

## <a name="prerequisites"></a>Prérequis

* [Émulateur de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Créer une ressource bot d’application web

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.

1. Dans la zone de recherche, recherchez et sélectionnez **Bot d’application web**. Sélectionnez **Create** (Créer).

1. Dans **Service Bot**, fournissez les informations requises :

    |Paramètre|Objectif|Paramétrage suggéré|
    |--|--|--|
    |Nom du robot|Nom de la ressource|`luis-csharp-bot-` + `<your-name>`, par exemple, `luis-csharp-bot-johnsmith`|
    |Subscription|Abonnement où créer le bot.|Votre abonnement principal.
    |Resource group|Groupe logique de ressources Azure|Créer un groupe pour stocker toutes les ressources utilisées avec ce bot, et nommer le groupe `luis-csharp-bot-resource-group`.|
    |Location|Région Azure, qui peut différer de la région de création ou de publication LUIS.|`westus`|
    |Niveau tarifaire|Utilisé pour les limites de demande de service et la facturation.|`F0` est le niveau gratuit.
    |Nom de l’application|Utilisé comme sous-domaine durant le déploiement de votre bot sur le cloud (par exemple, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, par exemple, `luis-csharp-bot-johnsmith`|
    |Modèle de bot|Paramètres de Bot Framework : consultez le tableau suivant|
    |Emplacement de l’application LUIS|Doit être identique à la région de ressources LUIS|`westus`|
    |Plan App Service/Emplacement|Conservez la valeur fournie par défaut.|
    |Application Insights|Conservez la valeur fournie par défaut.|
    |ID d’application et mot de passe Microsoft|Conservez la valeur fournie par défaut.|

1. Dans le **modèle de bot**, sélectionnez les paramètres suivants, puis choisissez le bouton **Sélectionner** sous ceux-ci :

    |Paramètre|Objectif|Sélection|
    |--|--|--|
    |Version du SDK|Version de Bot Framework|**SDK v4**|
    |Langage du SDK|Langage de programmation de bot|**C#**|
    |Bot|Type de bot|**Bot de base**|
    
1. Sélectionnez **Create** (Créer). Le service de bot est alors créé et déployé sur Azure. Une partie de ce processus crée une application LUIS nommée `luis-csharp-bot-XXXX`. Ce nom est basé sur le nom de l’application /Azure Bot Service.

    [![Créer un bot d’application web](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Attendez que le service de bot soit créé avant de continuer.

## <a name="the-bot-has-a-language-understanding-model"></a>Le bot possède un modèle Language Understanding

Le processus de création du service de bot crée également une application LUIS avec des intentions et des exemples d’énoncés. Le bot fournit un mappage d’intentions à la nouvelle application LUIS pour les intentions suivantes : 

|Intentions LUIS du bot de base|Exemple d’énoncé|
|--|--|
|Réserver le vol|`Travel to Paris`|
|Annuler|`bye`|
|GetWeather|`what's the weather like?`|
|Aucun|Quoi que ce soit en dehors du domaine de l’application.|

## <a name="test-the-bot-in-web-chat"></a>Tester le bot dans la Discussion Web

1. Dans le Portail Azure du nouveau bot, sélectionnez **Tester dans le Web Chat**. 
1. Dans la zone de texte **Tapez votre message**, entrez `Book a flight from Seattle to Berlin tomorrow`. Le bot répond avec la vérification que vous souhaitez réserver un vol. 

    ![Capture d’écran du Portail Azure, saisie du mot « hello »](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Vous pouvez utiliser la fonctionnalité de test pour tester rapidement votre bot. Pour des tests plus complets, y compris le débogage, téléchargez le code du bot et utilisez Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Télécharger le code source du bot d’application web
Pour développer le code de bot d’application web, téléchargez-le et utilisez-le sur votre ordinateur local. 

1. Dans le portail Azure, sélectionnez **Générer** dans la section **Gestion du bot**. 

1. Sélectionnez **Télécharger le code source du bot**. 

    [![Télécharger le code source du bot d’application web pour un bot de base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Lorsque la boîte de dialogue contextuelle demande **Inclure les paramètres d’application dans le fichier zip téléchargé ?** , sélectionnez **Oui**.

1. Quand le code source est compressé, un message fournit un lien pour le télécharger. Sélectionnez le lien. 

1. Enregistrez le fichier zip sur votre ordinateur local et extrayez les fichiers. Ouvrez le projet dans avec Visual Studio 2019. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Examinez le code pour envoyer l’énoncé à LUIS et obtenir une réponse

1. Pour envoyer l’énoncé utilisateur au point de terminaison de prédiction LUIS, ouvrez le fichier **FlightBookingRecognizer.cs**. C’est à cet endroit qu’est envoyé à LUIS l’énoncé de l’utilisateur entré dans le bot. La réponse de LUIS est retournée par la méthode **RecognizeAsync**.  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. Ouvrez **Dialogs -> MainDialog.cs**. Cela capture l’énoncé et l’envoie à executeLuisQuery dans la méthode actStep. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Démarrer le code du bot dans Visual Studio

Dans Visual Studio 2019, démarrez le bot. Une fenêtre de navigateur s’ouvre avec le site web du bot d’application web à l’adresse `http://localhost:3978/`. Une page d’accueil s’affiche avec des informations sur votre bot.

![Une page d’accueil s’affiche avec des informations sur votre bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Utiliser l’émulateur de bot pour tester le bot

1. Lancez l’émulateur de bot et sélectionnez **Ouvrir le bot**.
1. Dans la boîte de dialogue **Ouvrir le bot**, entrez l’URL de votre bot, par exemple `http://localhost:3978/api/messages`. L’itinéraire `/api/messages` est l’adresse web du bot.
1. Entrez **l’ID d’application Microsoft** et le **mot de passe d’application Microsoft**, figurant dans le fichier **appsettings.json** à la racine du code bot que vous avez téléchargé.


1. Dans l’émulateur de bot, entrez `Book a flight from Seattle to Berlin tomorrow` et obtenez la même réponse pour le bot de base que celle que vous avez reçue dans le **Test dans le Chat Web**.

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Sélectionnez **Oui**. Le bot répond par un résumé de ses actions. 
1. Dans le journal de l’émulateur de bot, sélectionnez la ligne qui contient `Luis Trace`. Cela affiche la réponse JSON de LUIS pour l’intention et les entités de l’énoncé.

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres [exemples](https://github.com/microsoft/botframework-solutions) avec des bots conversationnels. 

> [!div class="nextstepaction"]
> [Créer une application Language Understanding avec un domaine de l’objet personnalisé](luis-quickstart-intents-only.md)
