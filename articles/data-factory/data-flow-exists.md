---
title: Transformation Exists dans le flux de données de mappage Azure Data Factory | Microsoft Docs
description: Vérifier l’existence de lignes à l’aide de la transformation Exists dans le flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: bfc2a810d34f03fc0f10c486344c6dccec548305
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515122"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mappage d’une transformation Exists de flux de données

La transformation Exists est une transformation de filtrage de lignes qui vérifie si vos données existent dans une autre source ou un autre flux. Le flux de sortie comprend toutes les lignes du flux de gauche qui existent ou non dans le flux de droite. La transformation Exists est similaire à ```SQL WHERE EXISTS``` et ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Configuration

Choisissez le flux de données dont vous vérifiez l’existence dans la liste déroulante **Flux de droite**.

Indiquez si vous recherchez les données qui existent ou qui n’existent pas avec le paramètre **Type d’existence**.

Choisissez les colonnes clés que vous souhaitez comparer comme conditions d’existence. Par défaut, le flux de données recherche l’équivalence entre une colonne d’un flux et une colonne de l’autre flux. Pour effectuer une comparaison à l’aide d’une valeur de calcul, pointez sur la liste déroulante de la colonne, puis sélectionnez **Colonne calculée**.

![Paramètres d’existence](media/data-flow/exists.png "Exists 1")

### <a name="multiple-exists-conditions"></a>Conditions Exists multiples

Pour comparer plusieurs colonnes de chaque flux, ajoutez une nouvelle condition d’existence en cliquant sur l’icône plus en regard d’une ligne existante. Chaque condition supplémentaire est jointe par une instruction « and ». La comparaison de deux colonnes correspond à l’expression suivante :

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expression personnalisée

Pour créer une expression de forme libre qui contient des opérateurs autres que « and » et « equals to », cochez la case **Expression personnalisée**. Entrez une expression personnalisée par le biais du générateur d’expressions de flux de données en cliquant sur la zone bleue.

![Paramètres personnalisés de transformation Exists](media/data-flow/exists1.png "Transformation Exists personnalisée")

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<lefttream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: true | <false>,
        broadcast: 'none' | 'left' | 'right' | 'both'
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exemples

L’exemple ci-dessous illustre une transformation Exists nommée `checkForChanges`, qui utilise le flux de gauche `NameNorm2` et le flux de droite `TypeConversions`.  La condition d’existence est l’expression `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` qui retourne true si les colonnes `EMPID` et `Region` de chaque flux correspondent. Comme nous vérifions l’existence, `negate` a la valeur false. Par ailleurs, comme nous n’autorisons aucune diffusion dans l’onglet Optimiser, `broadcast` a la valeur `'none'`.

Dans l’expérience utilisateur Data Factory, cette transformation se présente comme dans l’image ci-dessous :

![Exemple de transformation Exists](media/data-flow/exists-script.png "Exemple de transformation Exists")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Étapes suivantes

Les transformations [Lookup](data-flow-lookup.md) et [Join](data-flow-join.md) sont similaires.
