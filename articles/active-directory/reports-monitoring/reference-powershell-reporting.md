---
title: Cmdlets Azure AD PowerShell pour la création de rapports | Microsoft Docs
description: Référence sur les cmdlets Azure AD PowerShell pour la création de rapports.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d34204b936a608158a0ca3e8af2264059ffc6aa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136550"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Applets de commande Azure AD PowerShell pour la création de rapports

> [!NOTE] 
> Ces applets de commande PowerShell ne fonctionnent actuellement qu’avec le module en [préversion Azure AD](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing). Notez que le module en préversion n’est pas proposé pour une utilisation en production. 

Avec les rapports Azure Active Directory (Azure AD), vous pouvez afficher des informations sur les activités concernant toutes les opérations d’écriture dans votre direction (journaux d’audit) et les données d’authentification (journaux de connexion). Bien que les informations soient disponibles à l’aide de MS API Graph, vous pouvez maintenant récupérer les mêmes données à l’aide des cmdlets PowerShell Azure AD pour la création de rapports.

Cet article vous donne une vue d’ensemble des cmdlets PowerShell à utiliser pour les journaux d’audit et les journaux de connexion.

## <a name="audit-logs"></a>Journaux d’audit

Les [journaux d’audit](concept-audit-logs.md) fournissent une traçabilité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc.

Accédez aux journaux d’audit à l’aide de la cmdlet « AzureADAuditDirectoryLogs ».


| Scénario                      | Commande PowerShell |
| :--                           | :--                |
| Nom d’affichage de l’application      | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync'" |
| Category                      | Get-AzureADAuditDirectoryLogs -Filter "category eq 'Application Management'" |
| Heure/date d’activité            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| Toutes les options ci-dessus              | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync' and category eq 'Application Management' and activityDateTime gt 2019-04-18"|


L’illustration suivante montre un exemple de cette commande. 

![Bouton « Synthèse des données »](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Journaux d’activité de connexion

Les journaux de [connexion](concept-sign-ins.md) fournissent des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.

Vous accédez aux journaux de connexion à l’aide de la cmdlet « AzureADAuditSignInLogs ».


| Scénario                      | Commande PowerShell |
| :--                           | :--                |
| Nom d’affichage de l’utilisateur             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| Heure/date de création              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z" (Everything since 5:30 pm on 4/18) |
| Statut                        | Get-AzureADAuditSignInLogs -Filter "status/errorCode eq 50105" |
| Nom d’affichage de l’application      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |
| Toutes les options ci-dessus              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' and status/errorCode ne 0 and appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


L’illustration suivante montre un exemple de cette commande. 

![Bouton « Synthèse des données »](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des rapports Azure AD](overview-reports.md).
- [Rapport de journaux d’audit](concept-audit-logs.md) 
- [Accès par programme aux rapports Azure AD](concept-reporting-api.md)
