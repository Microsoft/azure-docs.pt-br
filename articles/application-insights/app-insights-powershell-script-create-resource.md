---
title: Script do PowerShell para criar um recurso do Application Insights | Microsoft Docs
description: "Automatize a criação de recursos do Application Insights."
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: bwren
ms.openlocfilehash: a828af9c7d207dd84cc626fc70206018fd67e2dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Script do PowerShell para criar um recurso do Application Insights


Quando você deseja monitorar um novo aplicativo, ou uma nova versão de um aplicativo, com o [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), você configura um novo recurso no Microsoft Azure. Este recurso é o local no qual os dados de telemetria do seu aplicativo são analisados e exibidos. 

Você pode automatizar a criação de um novo recurso usando o PowerShell.

Por exemplo, se você estiver desenvolvendo um aplicativo de dispositivo móvel, é provável que, a qualquer momento, haverá várias versões publicadas do seu aplicativo em uso por seus clientes. Não é recomendável obter os resultados da telemetria de diferentes versões misturadas. Por isso, obtenha seu processo de compilação para criar um novo recurso para cada compilação.

> [!NOTE]
> Se você quiser criar um conjunto de recursos ao mesmo tempo, considere [criar os recursos usando um modelo do Azure](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script para criar um recurso do Application Insights
Consulte as especificações do cmdlet relevante:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Script do PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount / Login-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>O que fazer com o iKey
Cada recurso é identificado por sua chave de instrumentação (iKey). O iKey é uma saída do script de criação de recursos. Seu script de compilação deve fornecer o iKey para o SDK do Application Insights inserido no seu aplicativo.

Há duas maneiras de disponibilizar o iKey para o SDK:

* No [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou no [código de inicialização](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Consulte também
* [Criar recursos de teste da Web e do Application Insights por meio de modelos](app-insights-powershell.md)
* [Configurar o monitoramento do diagnóstico do Azure com o PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Definir alertas usando o PowerShell](app-insights-powershell-alerts.md)

