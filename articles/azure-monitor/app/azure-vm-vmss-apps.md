---
title: Monitorar o desempenho do aplicativo hospedado na VM do Azure e nos conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Monitoramento do desempenho de aplicativos para a VM do Azure e conjuntos de dimensionamento de máquinas virtuais do Azure. Tempo de resposta e carregamento do gráfico, informações de dependência e definir alertas sobre o desempenho.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: 69ae6ab6caedd9e89d277b92558931685d2ea320
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877305"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Monitorar o desempenho do aplicativo hospedado na VM do Azure e nos conjuntos de dimensionamento de máquinas virtuais do Azure

A habilitação do monitoramento em seus aplicativos Web baseados em .NET executados em [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/) e conjuntos de dimensionamento de [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) agora está mais fácil do que nunca. Obtenha todos os benefícios do uso de Application Insights sem modificar seu código.

Este artigo explicará como habilitar o monitoramento de Application Insights usando a extensão ApplicationMonitoringWindows e fornecer diretrizes preliminares para automatizar o processo para implantações em larga escala.

> [!IMPORTANT]
> A extensão ApplicationMonitoringWindows do Azure está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável para cargas de trabalho de produção. Alguns recursos podem não ter suporte e alguns podem ter recursos restritos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Habilitar o Application Insights

Há duas maneiras de habilitar o monitoramento de aplicativos para a VM do Azure e os aplicativos hospedados do conjunto de dimensionamento de máquinas virtuais do Azure:

* **Monitoramento de aplicativos baseados em agente** (Extensão ApplicationMonitoringWindows).
    * Esse método é o mais fácil de habilitar e nenhuma configuração avançada é necessária. Ele é geralmente chamado de monitoramento de "tempo de execução". Para VMs do Azure e conjuntos de dimensionamento de máquinas virtuais do Azure, é recomendável habilitar, no mínimo, esse nível de monitoramento. Depois disso, com base em seu cenário específico, você pode avaliar se a instrumentação manual é necessária.
    * Atualmente, há suporte apenas para aplicativos hospedados no IIS do .net.

* **Instrumentar manualmente o aplicativo por meio do código** instalando o SDK do Application insights.

    * Essa abordagem é muito mais personalizável, mas requer [a adição de uma dependência nos pacotes NuGet do SDK Application insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Esse método, também significa que você precisa gerenciar as atualizações para a versão mais recente dos pacotes por conta própria.

    * Se você precisar fazer chamadas à API personalizada para acompanhar eventos/dependências não capturadas por padrão com o monitoramento baseado em agente, você precisará usar esse método. Confira o [artigo API para eventos e métricas personalizados](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais.

> [!NOTE]
> Se o monitoramento baseado em agente e a instrumentação baseada em SDK manual forem detectados, somente as configurações de instrumentação manual serão respeitadas. Isso é para evitar que dados duplicados sejam enviados. Para saber mais sobre isso, confira a [seção solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-apps#troubleshooting) abaixo.

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>Gerenciar o monitoramento baseado em agente para aplicativos .NET na VM usando o PowerShell

Instalar ou atualizar a extensão de monitoramento de aplicativo para a VM
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

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Desinstalar extensão de monitoramento de aplicativo da VM
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Consultar status da extensão de monitoramento de aplicativo para a VM
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Obter lista de extensões instaladas para a VM
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>Gerenciar o monitoramento baseado em agente para aplicativos .NET no conjunto de dimensionamento de máquinas virtuais do Azure usando o PowerShell

Instalar ou atualizar a extensão de monitoramento de aplicativos para o conjunto de dimensionamento de máquinas virtuais do Azure
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

Desinstalar a extensão de monitoramento de aplicativo do conjunto de dimensionamento de máquinas virtuais do Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Consultar status da extensão de monitoramento de aplicativo para o conjunto de dimensionamento de máquinas virtuais do Azure
```powershell
# Not supported by extensions framework
```

Obter lista de extensões instaladas para o conjunto de dimensionamento de máquinas virtuais do Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Solução de problemas

Abaixo está nosso guia de solução de problemas passo a passo para o monitoramento baseado em extensão para aplicativos .NET em execução na VM do Azure e conjuntos de dimensionamento de máquinas virtuais do Azure.

> [!NOTE]
> Os aplicativos .NET Core, Java e node. js só têm suporte na VM do Azure e nos conjuntos de dimensionamento de máquinas virtuais do Azure por meio da instrumentação manual baseada em SDK e, portanto, as etapas a seguir não se aplicam a esses cenários.

A saída de execução da extensão é registrada nos arquivos localizados nos seguintes diretórios:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Próximas etapas
* Saiba como [implantar um aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configure testes da Web de disponibilidade](monitor-web-app-availability.md) para serem alertados se o ponto de extremidade estiver inativo.
