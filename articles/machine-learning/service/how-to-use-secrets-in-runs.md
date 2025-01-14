---
title: Utiliser des secrets dans les cycles d’apprentissage
titleSuffix: Azure Machine Learning
description: Transmettre des secrets à des cycles d’apprentissage de manière sécurisée à l’aide du coffre de clés d’espace de travail
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4872ba8a707192cd61ec371fa982a076d410e918
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996571"
---
# <a name="use-secrets-in-training-runs"></a>Utiliser des secrets dans les cycles d’apprentissage

Cet article explique comment utiliser des secrets dans des cycles d’apprentissage en toute sécurité. Par exemple, pour vous connecter à une base de données externe pour interroger des données d’apprentissage, vous devez transmettre un nom d’utilisateur et un mot de passe permettant d’accéder au contexte d’exécution à distance. Le codage de telles valeurs dans des scripts d’apprentissage en texte clair n’est pas sûr, car il exposerait le secret. 

Au lieu de cela, votre espace de travail Azure Machine Learning a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) pour ressource associée. Ce Key Vault peut être utilisé pour transmettre des secrets aux exécutions distantes en toute sécurité via un ensemble d’API dans le Kit de développement logiciel (SDK) Python pour Azure Machine Learning

Le flux de base pour l’utilisation de secrets est le suivant :
 1. Sur l’ordinateur local, connectez-vous à Azure, puis à votre espace de travail.
 2. Sur l’ordinateur local, définissez un secret dans le coffre de clés de l’espace de travail.
 3. Commandes une exécution à distance.
 4. Dans l’exécution à distance, récupérez le secret à partir de la valeur de clé et utilisez-le.

## <a name="set-secrets"></a>Définir des secrets

Dans le Kit de développement logiciel (SDK) Python pour Azure Machine Learning, la classe [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) contient des méthodes pour définir des secrets. Dans votre session Python locale, obtenez d’abord une référence au coffre de clés de l’espace de travail, puis utilisez la méthode [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) pour définir un clé secrète à l’aide d’un nom et d’une valeur.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Ne placez pas la valeur du secret dans le code Python, car il n’est pas sûr de la stocker dans un fichier en texte clair. Au lieu de cela, obtenez la valeur du secret à partir d’une variable d’environnement telle qu’un secret de la build Azure DevOps, ou à partir d’une entrée d’utilisateur interactive.

Vous pouvez obtenir la liste des noms de secret à l’aide de la méthode [list_secrets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) La méthode __set_secret__ met à jour la valeur de secret si le nom existe.

## <a name="get-secrets"></a>Get secrets (Obtenir les secrets)

Dans votre code local, vous pouvez utiliser la méthode [Keyvault.get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) pour obtenir la valeur de secret par son nom.

Dans les exécutions demandées à l’aide de la commande [Experiment.submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-), utilisez la méthode [Run.get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-). Étant donné que l’exécution demandée a connaissance de son espace de travail, cette méthode raccourcit l’instanciation d’espace de travail et retourne directement la valeur de secret.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Veillez à ne pas exposer la valeur de secret en l’écrivant ou en l’imprimant.

Les méthodes set et get ont également des versions de traitement par lot [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) et [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) pour accéder simultanément à plusieurs secrets.

## <a name="next-steps"></a>Étapes suivantes

 * [Voir un exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Découvrir la sécurité en entreprise avec Azure Machine Learning](concept-enterprise-security.md)
