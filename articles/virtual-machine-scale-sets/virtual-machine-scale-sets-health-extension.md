---
title: Use a extensão do Application Health com os conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como usar a extensão de integridade do aplicativo para monitorar a integridade de seus aplicativos implantados em conjuntos de dimensionamento de máquina virtual.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: d1cff1011e190e5fbb2874657cbdfbdc68bde0c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619817"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Usando a extensão do Application Health com os conjuntos de dimensionamento de máquinas virtuais
O monitoramento de integridade do seu aplicativo é um sinal importante para gerenciar e atualizar sua implantação. Os conjuntos de dimensionamento de máquinas virtuais do Azure fornecem suporte para [atualizações sem interrupção](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) incluindo [atualizações automáticas de imagem do sistema operacional](virtual-machine-scale-sets-automatic-upgrade.md), que dependem do monitoramento de integridade das instâncias individuais para atualizar sua implantação.

Esse artigo descreve como usar a extensão de integridade do aplicativo para monitorar a integridade de seus aplicativos implantados em conjuntos de dimensionamento de máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você esteja familiarizado com:
-   [Extensões da Máquina Virtual do Azure](../virtual-machines/extensions/overview.md)
-   [Modificando](virtual-machine-scale-sets-upgrade-scale-set.md) os Conjuntos de Dimensionamento de Máquinas Virtuais

## <a name="when-to-use-the-application-health-extension"></a>Quando usar a Extensão de Integridade do Aplicativo
A extensão de integridade do Application Health é implantada dentro de uma instância de conjunto de dimensionamento de máquinas virtuais e os relatórios de integridade da VM de dentro da instância de conjunto de dimensionamento. Você pode configurar a extensão para investigação em um ponto de extremidade do aplicativo e atualizar o status do aplicativo nessa instância. Esse status da instância é verificada pelo Azure para determinar se uma instância é elegível para operações de atualização.

Já que a extensão relata a integridade de dentro de uma VM, a extensão pode ser usada em situações em que investigações externas como investigações de Integridade do Aplicativo (que utilizam [investigações](../load-balancer/load-balancer-custom-probe-overview.md) personalizadas do Azure Load Balancer) não podem ser usadas.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão para VM do Application Health. A extensão requer no mínimo uma solicitação "tcp" ou "http" com uma porta associada ou caminho de solicitação, respectivamente.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publicador | `Microsoft.ManagedServices` | string |
| tipo | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Configurações

| NOME | Valor/Exemplo | Tipo de Dados
| ---- | ---- | ----
| protocol | `http` ou `tcp` | string |
| porta | Obrigatório quando o protocolo for `http`, não é permitido quando o protocolo for `tcp` | int |
| requestPath | Obrigatório quando o protocolo for `http`, não é permitido quando o protocolo for `tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Implantar a Extensão de Integridade do Aplicativo
Há várias maneiras de implantar a integridade da extensão do Application Health para o dimensionamento conforme detalhado nos exemplos a seguir.

### <a name="rest-api"></a>API REST

O exemplo a seguir adiciona a extensão do Application Health (com nome myHealthExtension) para o extensionProfile em um modelo de conjunto de dimensionamento de um conjunto de dimensionamento baseado em Windows.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Use `PATCH` para editar uma extensão já implantada.

### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de Integridade do Aplicativo à definição de modelo de conjunto de dimensionamento.

Os exemplos a seguir adicionam a extensão do Application Health ao `extensionProfile` no modelo de conjunto de dimensionamento de um conjunto de dimensionamento baseado em Windows. O exemplo usa o novo módulo Az PowerShell.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>CLI do Azure 2.0

Use [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão do Application Health a definição do modelo de conjunto de dimensionamento.

Os exemplos a seguir adicionam a extensão do Application Health em uma escala de conjunto de dimensionamento de um conjunto de dimensionamento baseado em Windows.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Solução de problemas
A saída de execução da extensão é registrada nos arquivos localizados nos seguintes diretórios:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Os logs também capturam periodicamente o status de integridade do aplicativo.

## <a name="next-steps"></a>Próximas etapas
Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
