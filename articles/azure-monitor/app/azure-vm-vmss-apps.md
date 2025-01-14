---
title: Surveiller les performances des applications hébergées sur les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Analyse des performances des applications pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/26/2019
ms.openlocfilehash: 4eb515d63d746e2f1f0b96431848a8b450d09358
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678218"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Déployer Azure Monitor Application Insights Agent sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure

Désormais, l’activation de la supervision pour vos applications web .NET qui s’exécutent sur des [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/) et des [groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) est plus facile que jamais. Bénéficiez de tous les avantages de l’utilisation de Application Insights sans modifier votre code.

Cet article vous explique comment activer la supervision Application Insights à l’aide du module Application Insights Agent. De plus, il fournit une aide préliminaire à l’automatisation des déploiements à grande échelle.

> [!IMPORTANT]
> Azure Application Insights Agent pour .NET est en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou n’être pas prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Activer Application Insights

Il existe deux manières d’activer la supervision des applications pour les applications hébergées sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure :

* **Sans code** via Application Insights Agent
    * Cette méthode est la plus facile à activer, car elle ne nécessite aucune configuration avancée. Elle est souvent appelée supervision « runtime ».

    * Pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure, nous recommandons au minimum d’activer ce niveau de supervision. Après cela, en fonction de votre scénario spécifique, vous pouvez évaluer si une instrumentation manuelle est nécessaire.

    * Le module Application Insights Agent collecte automatiquement les mêmes signaux de dépendance prêts à l’emploi que le kit SDK .NET. Pour en savoir plus, consultez [Collecte automatique de dépendance](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net).
        > [!NOTE]
        > Actuellement, seules les applications hébergées sur .NET IIS sont prises en charge. Utilisez un kit SDK pour instrumenter les applications ASP.NET Core, Java et Node.js hébergées sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure.

* **Avec du code** via le kit SDK

    * Cette approche est beaucoup plus personnalisable, mais elle nécessite d’[ajouter une dépendance sur les packages NuGet du SDK Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Cette méthode implique également de gérer vous-même l’installation des mises à jour vers la dernière version des packages.

    * Utilisez cette méthode si vous devez effectuer des appels d’API personnalisés pour suivre les événements/dépendances qui ne sont pas capturés par défaut avec la supervision basée sur un agent. Pour en savoir plus, consultez l’[article sur l’API Application Insights pour les événements et mesures personnalisés](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics).

> [!NOTE]
> Si les deux méthodes, la supervision basée sur un agent et l’instrumentation manuelle basée sur un SDK, sont détectées, seuls les paramètres de l’instrumentation manuelle sont appliqués. Cela évite que des données en double soient envoyées. Pour en savoir plus, consultez la [section de résolution des problèmes](#troubleshooting) ci-après.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gérer Application Insights Agent pour les applications .NET sur des machines virtuelles Azure à l’aide de PowerShell

> [!NOTE]
> Avant d’installer le module Application Insights Agent, vous avez besoin d’une clé d’instrumentation. [Créez une ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource), ou copiez la clé d’instrumentation à partir d’une ressource Application Insights existante.

> [!NOTE]
> Vous découvrez PowerShell ? Consultez le [Guide de prise en main](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Installer ou mettre à jour le module Application Insights Agent en tant qu’extension pour des machines virtuelles Azure
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Vous pouvez installer ou mettre à jour le module Application Insights Agent en tant qu’extension sur plusieurs machines virtuelles à grande échelle, à l’aide d’une boucle PowerShell.

Désinstaller l’extension Application Insights Agent d’une machine virtuelle Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Interroger l’état de l’extension Application Insights Agent pour une machine virtuelle Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Obtenir la liste des extensions installées pour une machine virtuelle Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Vous pouvez également voir les extensions installées dans le [panneau de la machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) au sein du portail.

> [!NOTE]
> Vérifiez l’installation en cliquant sur Flux de métriques temps réel dans la ressource Application Insights associée à la clé d’instrumentation utilisée pour déployer l’extension Application Insights Agent. Si vous envoyez des données provenant de plusieurs machines virtuelles, sélectionnez les machines virtuelles Azure cibles sous Nom du serveur. Il peut s’écouler jusqu’à une minute avant le début du transfert des données.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gérer Application Insights Agent pour les applications .NET sur des groupes de machines virtuelles identiques Azure à l’aide de PowerShell

Installer ou mettre à jour le module Application Insights Agent en tant qu’extension pour un groupe de machines virtuelles identiques Azure
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Désinstaller l’extension de supervision des applications sur des groupes de machines virtuelles identiques Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Interroger l’état de l’extension de supervision des applications pour des groupes de machines virtuelles identiques Azure
```powershell
# Not supported by extensions framework
```

Obtenir la liste des extensions installées pour des groupes de machines virtuelles identiques Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Résolution de problèmes

Vous trouverez des conseils de résolution des problèmes liés à l’extension Application Insights Monitoring Agent pour les applications .NET qui s’exécutent sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure.

> [!NOTE]
> Dans la mesure où les applications .NET Core, Java et Node.js sont uniquement prises en charge sur les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure via une instrumentation manuelle basée sur le kit SDK, les étapes ci-dessous ne s’appliquent pas à ces scénarios.

La sortie de l’exécution de l’extension est journalisées dans des fichiers figurant dans les répertoires suivants :
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [déployer une application sur un groupe de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configuration des tests de disponibilité web](monitor-web-app-availability.md), pour recevoir des alertes en cas d’interruption de votre point de terminaison.
