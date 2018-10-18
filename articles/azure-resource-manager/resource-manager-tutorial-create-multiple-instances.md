---
title: Créer plusieurs instances de ressources à l’aide d’Azure Resource Manager | Microsoft Docs
description: Découvrez comment créer un modèle Azure Resource Manager pour déployer plusieurs instances de ressources Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/10/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 63a18a6ae0ee4c6e0a01bd7ac4a26a4fb89746c2
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419478"
---
# <a name="tutorial-create-multiple-resource-instances-using-resource-manager-templates"></a>Tutoriel : Créer plusieurs instances de ressources à l’aide de modèles Resource Manager

Découvrez comment procéder à une itération dans votre modèle Azure Resource Manager pour créer plusieurs instances d’une ressource Azure. Dans le dernier tutoriel, vous avez modifié un modèle existant pour créer un compte de stockage Azure chiffré. Dans ce tutoriel, vous modifiez le même modèle pour créer trois instances de compte de stockage.

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Modifier le modèle
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/).
* Extension Outils Azure Resource Manager. Pour l’installation, consultez [Installer l’extension Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Le modèle utilisé dans ce démarrage rapide se nomme [Créer un compte de stockage standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Le modèle définit une ressource de compte de stockage Azure.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer le fichier sous le nom **azuredeploy.json** sur votre ordinateur local.

## <a name="edit-the-template"></a>Modifier le modèle

L’objectif de ce tutoriel est d’utiliser l’itération de ressource pour créer trois comptes de stockage.  L’exemple de modèle crée uniquement un compte de stockage. 

Dans Visual Studio Code, effectuez les quatre modifications suivantes :

![Créer plusieurs instances dans Azure Resource Manager](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Ajoutez un élément `copy` à la définition de ressource du compte de stockage. Dans l’élément copy, vous indiquez le nombre d’itérations et un nom pour cette boucle. La valeur de décompte doit être un entier positif et ne pas dépasser 800.
2. La fonction `copyIndex()` renvoie l’itération actuelle dans la boucle. `copyIndex()` est basé sur zéro. Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction copyIndex(). Par exemple, *copyIndex(1)*.
3. Supprimez l’élément **variables** car il n’est plus utilisé.
4. Supprimez l’élément **outputs**.

Le modèle complet ressemble à ceci :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Pour plus d’informations sur la création de plusieurs instances, consultez [Déployer plusieurs instances d’une ressource ou d’une propriété dans des modèles Azure Resource Manager](./resource-group-create-multiple.md)

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) dans le guide de démarrage rapide Visual Studio Code pour connaître la procédure de déploiement.

Pour répertorier tous les trois comptes de stockage, omettez le paramètre --name :

# <a name="clitabcli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)
```cli
az storage account list --resource-group <ResourceGroupName>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzureRmStorageAccount -ResourceGroupName <ResourceGroupName>
```

---

Comparez les noms de compte de stockage avec la définition de nom dans le modèle.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer plusieurs instances de compte de stockage. Jusqu’ici, vous avez créé un compte de stockage ou plusieurs instances de compte de stockage. Dans le tutoriel suivant, vous développez un modèle disposant de plusieurs ressources et types de ressources. Certaines ressources comportent des ressources dépendantes.

> [!div class="nextstepaction"]
> [Créer des ressources dépendantes](./resource-manager-tutorial-create-templates-with-dependent-resources.md)