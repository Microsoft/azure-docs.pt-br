---
title: Extensão de máquina virtual de dependência do Azure Monitor para Linux
description: Implante o agente de dependência do Azure Monitor na máquina virtual Linux usando uma extensão de máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 03/29/2019
ms.openlocfilehash: d28f0a34f47942bba8776a0acd0bfe3aaf25df12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566287"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual de dependência do Azure Monitor para Linux

O recurso Mapa do Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. A extensão da máquina virtual do agente de dependência da VM do Azure para Linux é publicada e recebe suporte da Microsoft. A extensão instala o agente de dependência em máquinas virtuais do Azure. Este documento especifica as opções de plataformas, de configurações e de implantação com suporte para a extensão da máquina virtual do agente de dependência da VM do Azure.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do agente de dependência da VM do Azure para Linux pode ser executada nos sistemas operacionais com suporte listados na seção [Sistemas operacionais com suporte](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) do artigo de implantação do Azure Monitor para VMs.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema da extensão do agente de dependência de VM do Azure em uma VM para Linux do Azure. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/exemplo |
| ---- | ---- |
| apiVersion | 01-01-2015 |
| publicador | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Implantação de modelo

Você pode implantar extensões de VM do Azure com os modelos do Azure Resource Manager. Você pode usar o esquema JSON detalhado na seção anterior em um modelo do Azure Resource Manager para executar a extensão do agente de dependência de VM do Azure durante uma implantação de modelo do Azure Resource Manager.

O JSON de uma extensão de máquina virtual pode ser aninhado no recurso de máquina virtual. Como alternativa, você pode colocá-lo no nível raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/templates/child-resource-name-type.md).

O exemplo a seguir pressupõe que a extensão do agente de dependência está aninhada no recurso de máquina virtual. Quando você aninha o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual pai. O tipo reflete a configuração aninhada. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

Você pode usar o CLI do Azure para implantar a extensão de VM do agente de dependência em uma máquina virtual existente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o seguinte comando usando a CLI do Azure:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>Suporte

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
