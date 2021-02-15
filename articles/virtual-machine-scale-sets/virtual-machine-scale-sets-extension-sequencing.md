---
title: Usar o sequenciamento de extensão com conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como sequenciar o provisionamento de extensões durante a implantação de várias extensões em conjuntos de dimensionamento de máquinas virtuais.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83121024"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Sequenciar o provisionamento de extensões em conjuntos de dimensionamento de máquinas virtuais
As extensões de máquina virtual do Azure fornecem funcionalidades como a configuração após a implantação e gerenciamento, monitoramento, segurança e muito mais. Implantações de produção normalmente usam uma combinação de várias extensões configuradas para as instâncias de VM para obter os resultados desejados.

Ao usar várias extensões em uma máquina virtual, é importante garantir que as extensões que exigem os mesmos recursos de sistema operacional não estão tentando adquirir esses recursos ao mesmo tempo. Algumas extensões também dependem de outras extensões para fornecer as configurações necessárias, tais como as configurações de ambiente e os segredos. Sem a ordenação correta e o sequenciamento em vigor, as implantações de extensões dependentes podem falhar.

Este artigo fornece detalhes sobre como você pode sequenciar extensões a serem configuradas para as instâncias de VM em conjuntos de dimensionamento de máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você esteja familiarizado com:
-   [Extensões](../virtual-machines/extensions/overview.md) de máquina virtual do Azure
-   [Modificando](virtual-machine-scale-sets-upgrade-scale-set.md) os Conjuntos de Dimensionamento de Máquinas Virtuais

## <a name="when-to-use-extension-sequencing"></a>Quando usar o sequenciamento de extensões
Sequenciar extensões não é obrigatório para conjuntos de dimensionamento e, a menos que especificado, as extensões podem ser provisionadas em uma instância do conjunto de dimensionamento em qualquer ordem.

Por exemplo, se seu modelo de conjunto de dimensionamento tem duas extensões – ExtensãoA e ExtensãoB – especificadas no modelo, então qualquer uma das sequências de provisionamento a seguir pode ocorrer:
-   ExtensãoA -> ExtensãoB
-   ExtensãoB -> ExtensãoA

Se seu aplicativo requer que a Extensão A seja sempre provisionada antes da Extensão B, você deve usar o sequenciamento de extensões, conforme descrito neste artigo. Com o sequenciamento de extensões, apenas uma sequência ocorrerá:
-   ExtensãoA -> ExtensãoB

Qualquer extensão não especificada em uma sequência de provisionamento definida pode ser provisionado a qualquer momento, incluindo antes, depois ou durante uma sequência definida. O sequenciamento de extensões só determina que uma extensão específica será provisionada após outra extensão específica. Isso não afetará o provisionamento de nenhuma outra extensão definida no modelo.

Por exemplo, se seu modelo de conjunto de dimensionamento tem três extensões – Extensão A, Extensão B e Extensão C – especificadas no modelo e a Extensão C é definida para ser provisionada depois da Extensão A, qualquer uma das sequências de provisionamento a seguir pode ocorrer:
-   ExtensãoA -> ExtensãoC -> ExtensãoB
-   ExtensãoB -> ExtensãoA -> ExtensãoC
-   ExtensãoA -> ExtensãoB -> ExtensãoC

Se você precisar garantir que nenhuma outra extensão seja provisionada durante a execução da sequência de extensão definida, é recomendável sequenciar todas as extensões em seu modelo de conjunto de dimensionamento. No exemplo acima, a Extensão B pode ser definida para ser provisionada após a Extensão C, de modo que apenas uma sequência possa ocorrer:
-   ExtensãoA -> ExtensãoC -> ExtensãoB


## <a name="how-to-use-extension-sequencing"></a>Como usar o sequenciamento de extensões
Para sequenciar o provisionamento da extensão, você precisa atualizar a definição da extensão no modelo do conjunto de dimensionamento para incluir a propriedade "provisionAfterExtensions", que aceita uma matriz de nomes de extensão. As extensões mencionadas no valor de matriz da propriedade precisam ser totalmente definidas no modelo de conjunto de dimensionamento.

### <a name="template-deployment"></a>Implantação de Modelo
O exemplo a seguir define um modelo em que o conjunto de dimensionamento tem três extensões, ExtensãoA, ExtensãoB e ExtensãoC, de modo que as extensões são provisionadas na ordem:
-   ExtensãoA -> ExtensãoB -> ExtensãoC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Uma vez que a propriedade "provisionAfterExtensions" aceita uma matriz de nomes de extensão, o exemplo acima pode ser modificado, de modo que ExtensãoC é provisionado após ExtensãoA e ExtensãoB, mas nenhuma ordenação é necessária entre ExtensãoA e ExtensãoB. O modelo a seguir pode ser usado para obter esse cenário:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>API REST
O exemplo a seguir adiciona uma nova extensão chamada ExtensãoC para um modelo de conjunto de dimensionamento. ExtensãoC tem dependências de ExtensãoA e ExtensãoB, que já foram definidas no modelo de conjunto de dimensionamento.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Se a ExtensãoC foi definida anteriormente no modelo de conjunto de dimensionamento e você agora deseja adicionar as respectivas dependências, você pode executar um `PATCH` para editar as propriedades da extensão já implantada.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Alterações nas instâncias de conjunto de dimensionamento existente são aplicadas na próxima [atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de Integridade do Aplicativo à definição de modelo de conjunto de dimensionamento. O sequenciamento de extensões requer o uso do Az PowerShell 1.2.0 ou posterior.

O exemplo a seguir adiciona a extensão do [Application Health](virtual-machine-scale-sets-health-extension.md) ao `extensionProfile` em um modelo de conjunto de dimensionamento de um conjunto de dimensionamento baseado em Windows. A extensão de Integridade do Aplicativo será provisionada após o provisionamento da [Extensão do Script Personalizado](../virtual-machines/extensions/custom-script-windows.md), já definida no conjunto de dimensionamento.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão do Application Health a definição do modelo de conjunto de dimensionamento. O sequenciamento de extensões requer o uso da CLI do Azure 2.0.55 ou posterior.

Os exemplos a seguir adicionam a [extensão de Integridade do Aplicativo](virtual-machine-scale-sets-health-extension.md) ao modelo de conjunto de dimensionamento de um conjunto de dimensionamento baseado em Windows. A extensão de Integridade do Aplicativo será provisionada após o provisionamento da [Extensão do Script Personalizado](../virtual-machines/extensions/custom-script-windows.md), já definida no conjunto de dimensionamento.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Solucionar problemas

### <a name="not-able-to-add-extension-with-dependencies"></a>Não é possível adicionar a extensão com dependências?
1. Verifique se as extensões especificadas em provisionAfterExtensions estão definidas no modelo de conjunto de dimensionamento.
2. Verifique que não há dependências circulares sendo introduzidas. Por exemplo, a sequência a seguir não é permitida: Extensiona-> ExtensionB-> ExtensionC-> Extensãoa
3. Verifique que qualquer extensão para a qual você assumir dependências tenha uma propriedade "configurações" nas "propriedades" da extensão. Por exemplo, se a ExtensãoB precisa ser provisionada após a ExtensãoA, a ExtensãoA precisa ter o campo "configurações" nas "propriedades" da ExtensãoA. Você pode especificar uma propriedade "configurações" vazia se a extensão não exige nenhuma configuração obrigatória.

### <a name="not-able-to-remove-extensions"></a>Não é possível remover extensões?
Assegure que as extensões sendo removidas não estejam listadas em provisionAfterExtensions para nenhuma outra extensão.

## <a name="next-steps"></a>Próximas etapas
Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
