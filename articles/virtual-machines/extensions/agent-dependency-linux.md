---
title: Extensão da máquina virtual do Azure Monitor de dependência para Linux | Microsoft Docs
description: Implante o agente de dependência do Monitor do Azure na máquina virtual Linux usando uma extensão da máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 16491ccc8a9cdc24b82c4c4e27705fa30da61088
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800241"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do Azure Monitor de dependência para Linux

O recurso Mapa do Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. A extensão de máquina virtual do agente de dependência de VM do Azure para Linux é publicada e suporte da Microsoft. A extensão instala o agente de dependência em máquinas virtuais do Azure. Este documento detalha as plataformas com suporte, configurações e opções de implantação para a extensão de máquina virtual do agente de dependência de VM do Azure para Linux.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do agente de dependência de VM do Azure para Linux pode ser executada em sistemas operacionais com suporte listados na [sistemas operacionais com suporte](../../azure-monitor/insights/vminsights-onboard.md#supported-operating-systems) seção do Monitor do Azure para o artigo de implantação de VMs.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente de dependência de VM do Azure em uma VM do Linux do Azure. 

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

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publicador | Microsoft.Azure.Monitoring.DependencyAgent |
| tipo | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a extensão do agente de dependência de VM do Azure durante uma implantação de modelo do Azure Resource Manager. 

O JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

O exemplo a seguir pressupõe que a extensão do agente de dependência é aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.


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

Ao inserir o JSON da extensão na raiz do modelo, o nome do recurso inclui uma referência na máquina virtual pai e o tipo reflete a configuração aninhada. 

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

A CLI do Azure pode ser usada para implantar o extensão de VM do agente de dependência em uma máquina virtual existente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
