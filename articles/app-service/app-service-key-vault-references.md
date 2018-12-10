---
title: Références Key Vault dans App Service et Azure Functions | Microsoft Docs
description: Guide conceptuel de références et d’installation pour les références Azure Key Vault dans Azure App Service et Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.openlocfilehash: 6f7a05638e9893c989276c61355a301e4a67a6ed
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447611"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Utiliser des références Key Vault pour App Service et Azure Functions (préversion)

> [!NOTE] 
> Les références Key Vault sont actuellement en préversion.

Cette rubrique vous montre comment utiliser des secrets d’Azure Key Vault dans votre application App Service ou Azure Functions, sans avoir à modifier le code. [Azure Key Vault](../key-vault/key-vault-overview.md) est un service qui fournit une gestion centralisée des secrets, avec un contrôle total sur les stratégies d’accès et l’historique d’audit.

## <a name="granting-your-app-access-to-key-vault"></a>Autoriser votre application à accéder à Key Vault

Pour pouvoir lire les secrets dans Key Vault, vous devez créer un coffre et donner à votre application l’autorisation d’y accéder.

1. Créez un coffre de clés en suivant le [Guide de démarrage rapide de Key Vault](../key-vault/quick-create-cli.md).

1. Créez une [identité managée affectée par le système](app-service-managed-service-identity.md) pour votre application.

   > [!NOTE] 
   > Actuellement, les références Key Vault prennent uniquement en charge les identités managées affectées par le système. Vous ne pouvez pas utiliser d’identités affectées par l’utilisateur.

1. Créez une [stratégie d’accès dans Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) pour l’identité d’application que vous avez créée précédemment. Activez l’autorisation de secret « Get » sur cette stratégie.

## <a name="reference-syntax"></a>Syntaxe de référence

Une référence Key Vault est de la forme `@Microsoft.KeyVault({referenceString})`, où `{referenceString}` est remplacé par une des options suivantes :

> [!div class="mx-tdBreakAll"]
> | Chaîne de référence                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_URI_secret_                                                       | L’**URI_secret** doit être l’URI complet du plan de données d’un secret dans Key Vault, y compris une version, par exemple, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_Nom_coffre_;SecretName=_Nom_secret_;SecretVersion=_Version_secret_ | Le **Nom_coffre** doit être le nom de votre ressource Key Vault. Le **Nom_secret** doit être le nom du secret cible. La **Version_secret** doit être la version du secret à utiliser. |

> [!NOTE] 
> Dans la préversion actuelle, les versions sont obligatoires. Pendant la rotation des secrets, vous devez mettre à jour la version dans la configuration de votre application.

Par exemple, une référence complète ressemble à ce qui suit :

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Sinon :

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Paramètres d’application sources dans Key Vault

Les références Key Vault peuvent servir de valeurs pour les [Paramètres d’application](web-sites-configure.md#app-settings), ce qui vous permet de garder des secrets dans Key Vault au lieu de la configuration de site. Les paramètres d’application sont chiffrés au repos de manière sécurisée, mais si vous avez besoin de fonctionnalités de gestion des secrets, ils doivent être gardés dans Key Vault.

Afin d’utiliser une référence Key Vault pour un paramètre d’application, définissez la référence comme valeur du paramètre. Votre application peut référencer le secret par le biais de sa clé comme d’habitude. Le code n’a pas besoin d’être modifié.

> [!TIP]
> La plupart des paramètres d’application qui utilisent des références Key Vault doivent être marqués comme des paramètres d’emplacement, car vous devez avoir des coffres distincts pour chaque environnement.

### <a name="azure-resource-manager-deployment"></a>Déploiement Azure Resource Manager

Quand vous automatisez des déploiements de ressources par le biais de modèles Azure Resource Manager, vous pouvez avoir besoin de séquencer vos dépendances dans un ordre particulier pour que cette fonctionnalité fonctionne. Sinon, vous devez définir vos paramètres d’application comme leur propre ressource, au lieu d’utiliser une propriété `siteConfig` dans la définition de site. C’est parce que le site doit être défini en premier pour que le système puisse affecter l’identité et que cette identité puisse être utilisée dans la stratégie d’accès.

Un exemple de pseudo-modèle pour une application de fonction peut se présenter comme suit :

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Dans cet exemple, le déploiement du contrôle de code source varie selon les paramètres d’application. Il s’agit normalement d’un comportement non sécurisé, car la mise à jour du paramètre d’application se comporte de façon asynchrone. Toutefois, comme nous avons inclus le paramètre d'application `WEBSITE_ENABLE_SYNC_UPDATE_SITE`, la mise à jour est synchrone. Cela signifie que le déploiement de contrôle source commence uniquement une fois que les paramètres d’application ont été entièrement mis à jour.