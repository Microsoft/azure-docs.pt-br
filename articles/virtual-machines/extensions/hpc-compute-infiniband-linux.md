---
title: Extensão do driver InfiniBand – VMs Linux do Azure
description: Microsoft Azure extensão para instalar os drivers InfiniBand em VMs de computação da série H e N que executam o Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 1219f5befb4de81850681a53c10f03dc81249d53
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559724"
---
# <a name="infiniband-driver-extension-for-linux"></a>Extensão de driver InfiniBand para Linux

Essa extensão instala os drivers InfiniBand OFED nas VMs InfiniBand e SR-IOV (' r ' tamanhos) das [séries H](../sizes-hpc.md) e [N-Series](../sizes-gpu.md) que executam o Linux. Dependendo da família de VMs, a extensão instala os drivers apropriados para a NIC Connect-X.

As instruções sobre a instalação manual dos drivers do OFED estão disponíveis [aqui](../workloads/hpc/enable-infiniband.md#manual-installation).

Uma extensão também está disponível para instalar os drivers InfiniBand para [VMs do Windows](hpc-compute-infiniband-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

Esta extensão é compartível com as seguintes distribuições do sistema operacional, dependendo do suporte do driver para uma versão específica do sistema operacional.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 16, 4 LTS, 18, 4 LTS, 20, 4 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão de Microsoft Azure para drivers InfiniBand requer que a VM de destino esteja conectada ao e tenha acesso à Internet.

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
| type | InfiniBandDriverLinux | string |
| typeHandlerVersion | 1,1 | INT |



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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Adicionar extensão a um conjunto de dimensionamento de máquinas virtuais

O exemplo a seguir instala a extensão mais recente da versão 1,1 do InfiniBandDriverLinux em todas as VMs compatíveis com RDMA em um conjunto de dimensionamento de máquinas virtuais existente chamado *myVMSS* implantado no grupo de recursos chamado *MyResource* Group:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
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

A saída de execução de extensão é registrada no arquivo a seguir. Consulte esse arquivo para acompanhar o status da instalação, bem como para solucionar quaisquer falhas.

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Códigos de saída

A tabela a seguir descreve o significado e a ação recomendada com base nos códigos de saída do processo de instalação da extensão.

| Código de Saída | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação concluída com êxito |
| 1 | Uso incorreto de extensão | Verifique o log de saída de execução |
| 10 | Serviços de integração do Linux para Hyper-V e o Azure não disponível ou instalado | Verificação de saída de Ispci |
| 11 | O InfiniBand de Mellanox não foi encontrado neste tamanho de VM | Use um [tamanho da VM e sistema operacional com suporte](../sizes-hpc.md) |
| 12 | Oferta de imagem não suportada |
| 13 | Tamanho de VM não suportado | Usar uma VM [da série n e série](../sizes-hpc.md) [n-](../sizes-gpu.md)Series habilitadas para InfiniBand (' r ') para implantar |
| 14 | Operação falhou | Verifique o log de saída de execução |


### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode arquivar um incidente de suporte por meio do [site de suporte do Azure](https://azure.microsoft.com/support/options/). Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os tamanhos habilitados para InfiniBand (' r '), consulte VMs da [série H](../sizes-hpc.md) e [série N](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Saiba mais sobre os recursos e extensões de VMs do Linux](features-linux.md)