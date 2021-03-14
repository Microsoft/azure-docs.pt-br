---
title: Extensão de Diagnóstico do Azure para Windows
description: Monitorar VMs do Windows no Azure usando a Extensão de Diagnóstico do Azure
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e703a8f91f18467660ef7e1b91fdb034feeb00b1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549525"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Extensão de Diagnóstico do Azure para Windows

## <a name="overview"></a>Visão geral

A extensão de VM de Diagnóstico do Azure permite coletar dados de monitoramento, como contadores de desempenho e logs de eventos, da VM do Windows. Você pode especificar gradualmente quais dados deseja coletar e onde você deseja que os dados vão, como uma conta de Armazenamento do Azure ou um Hub de Eventos do Azure. Você também pode usar esses dados para criar gráficos no Portal do Azure ou criar alertas de métrica.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A Extensão de Diagnóstico do Azure pode ser executada no Windows 10 Client e Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="internet-connectivity"></a>Conectividade com a Internet

A Extensão de Diagnóstico do Azure para requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

[Os valores de propriedade e o esquema da Extensão de Diagnóstico do Azure são descritos neste documento.](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a extensão de Diagnóstico do Azure durante uma implantação de modelo do Azure Resource Manager. Consulte [Usar monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](../extensions/diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

A CLI do Azure pode ser usado para implantar a extensão de Diagnóstico do Azure para uma máquina virtual existente. Substitua as configurações protegidas e propriedades de configurações por JSON válido do esquema da extensão acima. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzVMDiagnosticsExtension` pode ser usado para adicionar a extensão de Diagnóstico do Azure para uma máquina virtual existente. Consulte também [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows](../extensions/diagnostics-windows.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Consulte este artigo](../../azure-monitor/agents/diagnostics-extension-troubleshooting.md) para obter um guia de solução de problemas mais abrangente para a extensão de Diagnóstico do Azure.

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre a Extensão de Diagnóstico do Azure](../../azure-monitor/agents/diagnostics-extension-overview.md)
* [Examine o esquema e as versões de extensão](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)
