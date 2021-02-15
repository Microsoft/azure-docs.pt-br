---
title: Extensão do driver NVIDIA GPU – VMs do Windows do Azure
description: Extensão do Microsoft Azure para instalar os Drivers NVIDIA GPU em série N de computação VMs que executam o Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: 82ed1d57d72a4479005d8bfd2234c124a97096ee
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965979"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Extensão de Driver NVIDIA GPU para Windows

## <a name="overview"></a>Visão geral

Essa extensão instala drivers de GPU NVIDIA em VMs série N do Windows. Dependendo da família VM, a extensão instala drivers CUDA ou grade. Quando você instalar drivers NVIDIA usando esta extensão, estará aceitando e concordando com os termos do [Contrato de Licença de Usuário Final da NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Durante o processo de instalação, a VM pode ser reinicializada para concluir a configuração do driver.

Instruções sobre a instalação manual dos drivers e as versões atuais com suporte estão disponíveis [aqui](../windows/n-series-driver-setup.md).
Uma extensão também está disponível para instalar drivers NVIDIA GPU em [VMs da série N do Linux](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A Extensão suporta os seguintes OS:

| Distribuição | Versão |
|---|---|
| Windows 10 | Núcleo |
| Windows Server 2016 | Núcleo |
| Windows Server 2012 R2 | Núcleo |

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão do Microsoft Azure para drivers de GPU NVIDIA requer que a VM de destino esteja conectada à Internet e tenha acesso.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.3",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriedades

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverWindows | string |
| typeHandlerVersion | 1,3 | INT |


## <a name="deployment"></a>Implantação

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager 

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem configuração pós-implantação.

A configuração do JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/templates/child-resource-name-type.md). 

O exemplo a seguir pressupõe que a extensão está aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.3",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
  }'
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o Azure PowerShell e a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte local:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\
```

### <a name="error-codes"></a>Códigos do Erro

| Código do Erro | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação concluída com êxito |
| 1 | Operação concluída com êxito. É necessário reiniciar. |
| 100 | Operação sem suporte ou não pôde ser concluída. | Possíveis causas: não há suporte para a versão do PowerShell, o tamanho da VM não é uma VM da série N, Falha ao fazer download de dados. Verifique os arquivos de log para determinar a causa do erro. |
| 240, 840 | Tempo limite da operação. | Operação de teste. |
| -1 | Exceção ocorreu. | Verifique os arquivos de log para determinar a causa da exceção. |
| -5x | Operação foi interrompida devido a reinicialização pendente. | Reinicializar VM. Instalação continuará após a reinicialização. Desinstalação deve ser invocada manualmente. |


### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões, consulte [Recursos e extensões da máquina virtual para Windows](features-windows.md).

Para obter mais informações sobre VMs série N, consulte [tamanhos de máquinas virtuais otimizados para GPU](../sizes-gpu.md).
