---
title: Paramètres de flux de données de mappage d’Azure Data Factory
description: Découvrez comment paramétrer un flux de données de mappage à partir de pipelines Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387535"
---
# <a name="mapping-data-flow-parameters"></a>Paramètres de flux de données de mappage



La mappage des flux de données dans Azure Data Factory prend en charge l’utilisation des paramètres. Vous pouvez définir des paramètres à l’intérieur de votre définition de flux de données, utilisable ensuite dans toutes vos expressions. Les valeurs de paramètre peuvent être définies par le pipeline d’appel par le biais de l’activité Exécuter une activité de flux de données. Vous disposez de trois options pour définir les valeurs dans les expressions d’activité de flux de données :

* Utiliser le langage d’expression de flux de contrôle de pipeline pour définir une valeur dynamique
* Utiliser le langage d’expression de flux de données pour définir une valeur dynamique
* Utiliser un langage d’expression pour définir une valeur littérale statique

Utilisez cette fonctionnalité pour rendre vos flux de données polyvalents, flexibles et réutilisables. Vous pouvez définir les paramètres de flux de données et les expressions à l’aide de ces paramètres.

> [!NOTE]
> Pour utiliser des expressions de flux de contrôle de pipeline, votre paramètre de flux de données doit être de type chaîne.

## <a name="create-parameters-in-mapping-data-flow"></a>Créer des paramètres dans un flux de données de mappage

Pour ajouter des paramètres à votre flux de données, cliquez sur la partie vide du canevas de celui-ci afin d’accéder aux propriétés générales. Le volet des paramètres doit contenir un onglet dénommé « Paramètres ». Cliquez sur le bouton « Nouveau » pour générer un nouveau paramètre. Pour chaque paramètre, vous devez attribuer un nom, sélectionner un type et éventuellement définir une valeur par défaut.

![Créer des paramètres de flux de données](media/data-flow/create-params.png "Créer des paramètres de flux de données")

Les paramètres peuvent être utilisés dans n’importe quelle expression de flux de données. Les paramètres commencent par $ et sont immuables. Vous trouverez la liste des paramètres disponibles dans le Générateur d’expressions sous l’onglet « Paramètres ».

![Expression de paramètre de flux de données](media/data-flow/parameter-expression.png "Expression de paramètre de flux de données")

## <a name="use-parameters-in-your-data-flow"></a>Utiliser des paramètres dans votre flux de données

* Vous pouvez utiliser les valeurs de paramètre dans vos expressions de transformation. Vous trouverez la liste des paramètres sous l’onglet Paramètres dans le Générateur d’expressions. ![Utiliser des paramètres de flux de données](media/data-flow/params9.png "UUtiliser des paramètres de flux de données")

* Les paramètres sont également utilisés pour configurer les valeurs dynamiques de vos paramètres de transformation Source et Sink. Lorsque vous cliquez à l’intérieur des champs configurables, le lien « Ajouter du contenu dynamique » apparaît. En cliquant dessus, vous accédez à un Générateur d’expressions dans lequel vous pouvez définir des paramètres pour utiliser des valeurs dynamiques. ![Contenu dynamique de flux de données](media/data-flow/params6.png "DContenu dynamique de flux de données")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Définir les paramètres de flux de données de mappage à partir d’un pipeline

Une fois que vous avez créé votre flux de données avec des paramètres, vous pouvez l’exécuter à partir d’un pipeline avec l’activité Exécuter un flux de données. Après avoir ajouté l’activité à votre canevas de pipeline, les paramètres de flux de données disponibles vous sont présentés dans l’onglet « Paramètres » de l’activité.

![Définition d’un paramètre de flux de données](media/data-flow/parameter-assign.png "Définition d’un paramètre de flux de données")

Si votre type de données de paramètre est une chaîne de caractères, lorsque vous cliquez sur la zone de texte pour définir les valeurs des paramètres, vous pouvez choisir d’entrer un pipeline ou une expression de flux de données. Si vous choisissez l’expression de pipeline, le panneau d’expression de pipeline vous est présenté. Assurez-vous d’inclure les fonctions de pipeline dans la syntaxe d’interpolation de chaîne en utilisant `'@{<expression>}'`, par exemple :

```'@{pipeline().RunId}'```

Si votre paramètre n’est pas de type chaîne, le Générateur d’expressions Data Flow vous est toujours présenté. Ici, vous pouvez saisir n’importe quelle expression ou valeur littérale qui correspond au type de données du paramètre. Vous trouverez ci-dessous des exemples d’expression Data Flow et une chaîne littérale du Générateur d’expressions :

* ```toInteger(Role)```
* ```'this is my static literal string'```

Chaque flux de données de mappage peut comporter n’importe quelle combinaison de paramètres d’expression de pipeline et de flux de données. 

![Exemple de paramètres de flux de données](media/data-flow/parameter-example.png "Exemple de paramètres de flux de données")



## <a name="next-steps"></a>Étapes suivantes
* [Exécuter une activité de flux de données](control-flow-execute-data-flow-activity.md)
* [Expressions de flux de contrôle](control-flow-expression-language-functions.md)
