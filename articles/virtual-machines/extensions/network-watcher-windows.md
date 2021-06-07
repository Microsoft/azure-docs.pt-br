---
title: Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows
description: Implante o Agente do Observador de Rede na máquina virtual Windows usando uma extensão da máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 02/14/2017
ms.openlocfilehash: d336a39714712e5436086e22ad24fc942a7d850a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563533"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual do Agente do Observador de Rede para Windows

## <a name="overview"></a>Visão geral

O [Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitoramento de desempenho, diagnóstico e análise de rede que permite o monitoramento de redes do Azure. A extensão de máquina virtual do Agente do Observador de Rede é um requisito para capturar o tráfego de rede sob demanda e outras funcionalidades avançadas em máquinas virtuais do Azure.


Este documento detalha as opções com suporte de plataformas e implantação para a extensão da máquina virtual do Agente do Observador de Rede para Windows. A instalação do agente não interrompe nem requer um reinício da máquina virtual. É possível implantar a extensão em máquinas virtuais que você implanta. Se a máquina virtual for implantada por um serviço do Azure, verifique a documentação do serviço para determinar se ele permite ou não a instalação de extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do agente do observador de rede para Windows pode ser executada em versões do Windows Server 2008 R2, 2012, 2012 R2, 2016 e 2019. O Servidor Nano não é suportado neste momento.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Algumas das funcionalidades do Agente do Observador de Rede exigem que a máquina virtual de destino esteja conectada à Internet. Sem a capacidade de estabelecer conexões de saída, o Agente do Observador de Rede não poderá carregar as capturas de pacote para sua conta de armazenamento. Para obter mais detalhes, confira a [documentação do Observador de Rede](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do Agente do Observador de Rede. A extensão não requer e não é compatível com nenhuma configuração fornecida pelo usuário e é baseada em uma configuração padrão.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| editor | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Implantação de modelo

Você pode implantar extensões de VM do Azure com os modelos do Azure Resource Manager. Você pode usar o esquema JSON detalhado na seção anterior em um modelo do Azure Resource Manager para executar a extensão do Agente do Observador de Rede durante uma implantação de modelo do Azure Resource Manager.

## <a name="powershell-deployment"></a>Implantação do PowerShell

Use o comando `Set-AzVMExtension` para implantar a extensão de máquina virtual do Agente do Observador de Rede em uma máquina virtual existente:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Solução de problemas e suporte

### <a name="troubleshooting"></a>Solução de problemas

Você pode recuperar dados sobre o estado das implantações de extensão por meio do portal do Azure e do PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando o módulo do Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A saída de execução da extensão é registrada nos arquivos localizados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, veja a documentação Guia de Usuário do Observador de Rede ou contate os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
