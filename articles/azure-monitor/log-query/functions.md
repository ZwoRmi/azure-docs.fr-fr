---
title: Utilisation de fonctions dans les requêtes de journal Azure Monitor | Microsoft Docs
description: Cet article décrit l’utilisation des fonctions pour appeler une requête à partir d’une autre requête de journal dans Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076699"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Utilisation de fonctions dans les requêtes de journal Azure Monitor

Pour utiliser une requête de journal avec une autre requête, vous pouvez l’enregistrer en tant que fonction. Cela vous permet de simplifier les requêtes complexes en les divisant en plusieurs parties et de réutiliser le code commun avec plusieurs requêtes.

## <a name="create-a-function"></a>Créer une fonction

Pour créer une fonction avec Log Analytics sur le portail Azure, cliquez sur **Enregistrer**, puis fournissez les informations indiquées dans le tableau suivant.

| Paramètre | Description |
|:---|:---|
| Nom           | Nom d’affichage de la requête dans l’**Explorateur de requêtes**. |
| Enregistrer sous        | Fonction |
| Alias de fonction | Nom court pour utiliser la fonction dans d’autres requêtes. Ne peut pas contenir d’espaces et doit être unique. |
| Category       | Catégorie pour organiser les fonctions et les requêtes enregistrées dans l’**Explorateur de requêtes**. |

> [!NOTE]
> Une fonction dans Azure Monitor ne peut pas contenir une autre fonction.




## <a name="use-a-function"></a>Utiliser une fonction
Utilisez une fonction en incluant son alias dans une autre requête. Elle peut être utilisée comme toute autre table.

## <a name="example"></a>Exemples
L’exemple de requête suivante retourne toutes les mises à jour de sécurité manquantes signalées le dernier jour. Enregistrez cette requête en tant que fonction avec l’alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Créez une autre requête et référencez la fonction _security_updates_last_day_ pour rechercher les mises à jour de sécurité nécessaires concernant SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous à d'autres leçons pour écrire des requêtes de journal Azure Monitor :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Jointures](joins.md)
- [Graphiques](charts.md)
