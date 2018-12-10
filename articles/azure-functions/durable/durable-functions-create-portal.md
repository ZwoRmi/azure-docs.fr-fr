---
title: Créer des fonctions Durable Functions à l’aide du portail Azure
description: Découvrez comment installer l’extension Durable Functions pour Azure Functions pour le développement de portails.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: acbba991e6dcce56fad7f27c45f85214cc8fc707
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637004"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Créer des fonctions Durable Functions à l’aide du portail Azure

L’extension [Fonctions durables](durable-functions-overview.md) d’Azure Functions est fournie dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Cette extension doit être installée dans votre application de fonction. Cet article explique comment installer ce package, pour vous permettre de développer des fonctions durables dans le portail Azure.

>[!NOTE]
>
>* Si vous développez des fonctions durables dans C#, pensez plutôt à utiliser le [développement Visual Studio 2017](durable-functions-create-first-csharp.md).
* Si vous développez des fonctions durables dans JavaScript, pensez plutôt à utiliser le **développement Visual Studio Code**.
>
>La création de fonctions Durable Functions à l’aide de JavaScript n’est pas encore prise en charge dans le portail. Utilisez Visual Studio Code à la place.

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une application de fonction pour héberger l’exécution d’une fonction. Une application de fonction vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Vous devez créer une application de fonction C#, car les modèles JavaScript ne sont pas encore pris en charge pour Durable Functions.  

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Par défaut, l’application de fonction créée utilise la version 2.x du runtime Azure Functions. L’extension Durable Functions fonctionne sur les deux versions 1.x et 2.x du runtime Azure Functions. Toutefois, les modèles ne sont disponibles que lorsque vous ciblez la version 2.x du runtime.

## <a name="create-an-orchestrator-function"></a>Créer une fonction d’orchestrateur

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre application de fonction, sélectionnez **Dans le portail**, puis **Continuer**. Sinon, passez à l’étape 3.

   ![Page de démarrage rapide des fonctions sur le portail Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Choisissez **Autres modèles**, puis **Terminer et afficher les modèles**.

    ![Page de démarrage rapide Functions permettant de choisir d’autres modèles](./media/durable-functions-create-portal/add-first-function.png)

1. Dans le champ de recherche, tapez `durable`, puis choisissez le modèle **Starter HTTP Durable Functions**.

1. Si vous y êtes invité, sélectionnez **Installer** pour installer l’extension Azure DurableTask et toutes les dépendances dans l’application de fonction. Il vous suffit d’installer l’extension une seule fois pour une application de fonction donnée. Une fois l’installation réussie, sélectionnez **Continuer**.

    ![Installer des extensions de liaison](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Une fois l’installation terminée, nommez la nouvelle fonction `HttpStart` et choisissez **Créer**. La fonction créée est utilisée pour démarrer l’orchestration.

1. Créez une autre fonction dans l’application de fonction, cette fois en utilisant le modèle **Orchestrateur Durable Functions**. Nommez votre nouvelle fonction d’orchestration `HelloSequence`.

1. Créez une troisième fonction nommée `Hello` à l’aide du modèle **Activité Durable Functions**.

## <a name="test-the-durable-function-orchestration"></a>Tester l’orchestration de la fonction durable

1. Revenez à la fonction **HttpStart**, choisissez **</> Obtenir l’URL de la fonction** et **copiez** l’URL. Cette URL vous permet de démarrer la fonction **HelloSequence**.

1. Utilisez un outil HTTP tel que Postman ou cURL pour envoyer une requête POST à l’URL que vous avez copiée. L’exemple suivant est une commande cURL qui envoie une requête POST à la fonction durable :

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Dans cet exemple, `{your-function-app-name}` est le domaine qui est le nom de votre application de fonction. Le message de réponse contient un ensemble de points de terminaison d’URI que vous pouvez utiliser pour surveiller et gérer l’exécution, qui ressemble à l’exemple suivant :

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Appelez l’URI de point de terminaison `statusQueryGetUri` et l’état actuel de la fonction durable s’affiche. Il peut se présenter comme dans cet exemple :

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continuez d’appeler le point de terminaison `statusQueryGetUri` jusqu’à ce que l’état passe à **Terminé**, et qu’une réponse s’affiche, similaire à l’exemple suivant : 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Votre première fonction durable est maintenant opérationnelle dans Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les modèles courants de fonction durable](durable-functions-overview.md)