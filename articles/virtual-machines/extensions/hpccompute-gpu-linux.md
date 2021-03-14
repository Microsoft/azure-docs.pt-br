---
title: Extensão do Driver GPU NVIDIA — VMs do Linux no Azure
description: Extensão do Microsoft Azure para instalar os Drivers NVIDIA GPU em série N de computação VMs que executam o Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: fa2b82f3246fd87830010f572ba23aa2df075053
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566219"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Extensão de Driver NVIDIA GPU para Linux

## <a name="overview"></a>Visão geral

Essa extensão instala drivers de GPU NVIDIA em VMs série N do Linux. Dependendo da família VM, a extensão instala drivers CUDA ou grade. Quando você instalar drivers NVIDIA usando esta extensão, estará aceitando e concordando com os termos do [Contrato de Licença de Usuário Final da NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Durante o processo de instalação, a VM pode ser reinicializada para concluir a configuração do driver.

Instruções sobre a instalação manual dos drivers e as versões atuais com suporte estão disponíveis [aqui](../linux/n-series-driver-setup.md).
Uma extensão também está disponível para instalar drivers NVIDIA GPU em [VMs da série N do Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

Esta extensão é compartível com as seguintes distribuições do sistema operacional, dependendo do suporte do driver para uma versão específica do sistema operacional.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |

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
    "type": "NvidiaGpuDriverLinux",
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
| type | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1,3 | INT |

### <a name="settings"></a>Configurações

Todas as configurações são opcionais. O comportamento padrão é não atualizar o kernel se não for necessário para a instalação do driver, instale o driver mais recente com suporte e o CUDA toolkit (conforme aplicável).

| Nome | Descrição | Valor Padrão | Valores válidos | Tipo de Dados |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Atualize o kernel, mesmo que não seja necessário para instalação do driver | false | verdadeiro, falso | booleano |
| driverVersion | NV: versão do driver GRID<br> NC/ND: versão do Kit de ferramentas CUDA. Os drivers mais recentes para o CUDA escolhido são instalados automaticamente. | mais recente | [Lista](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) de versões de driver com suporte | string |
| installCUDA | Instale o kit de ferramentas CUDA. Só é relevante para as VMs da série NC/ND. | true | verdadeiro, falso | booleano |


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
    "type": "NvidiaGpuDriverLinux",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir espelha os exemplos de Azure Resource Manager e PowerShell acima.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

O exemplo a seguir também adiciona duas configurações personalizadas opcionais como um exemplo para instalação de driver não padrão. Especificamente, ele atualiza o kernel do sistema operacional para o mais recente e instala um driver de versão do CUDA Toolkit específico. Novamente, observe que '--Settings ' são opcionais e default. Observe que a atualização do kernel pode aumentar os tempos de instalação da extensão. Além disso, escolher uma versão específica (mais antiga) do CUDA tolkit pode nem sempre ser compatível com os kernels mais recentes.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
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

A saída de execução de extensão é registrada no arquivo a seguir. Consulte esse arquivo para acompanhar o status de instalação de (qualquer execução longa), bem como para solucionar quaisquer falhas.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Códigos de saída

| Código de Saída | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação concluída com êxito |
| 1 | Uso incorreto de extensão | Verifique o log de saída de execução |
| 10 | Serviços de integração do Linux para Hyper-V e o Azure não disponível ou instalado | Verificação de saída de Ispci |
| 11 | GPU NVIDIA não encontrado nesse tamanho de VM | Use um [tamanho da VM e sistema operacional com suporte](../linux/n-series-driver-setup.md) |
| 12 | Oferta de imagem não suportada |
| 13 | Tamanho de VM não suportado | Usar uma VM da série N para implantar |
| 14 | Operação falhou | Verifique o log de saída de execução |


### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).

Para obter mais informações sobre VMs série N, consulte [tamanhos de máquinas virtuais otimizados para GPU](../sizes-gpu.md).
