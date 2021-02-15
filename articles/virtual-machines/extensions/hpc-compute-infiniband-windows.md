---
title: Extensão do driver InfiniBand – VMs do Windows do Azure
description: Microsoft Azure extensão para instalar drivers InfiniBand em VMs de computação da série H e N que executam o Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: amverma
ms.openlocfilehash: 767d6da7701261836b367ccad121bf3569b43b72
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260156"
---
# <a name="infiniband-driver-extension-for-windows"></a>Extensão de driver InfiniBand para Windows

Essa extensão instala os drivers InfiniBand ND (para os drivers não habilitados para SR-IOV) e OFED (para SR-IOV-habilitado) (' r ' tamanhos) das VMs [série H](../sizes-hpc.md) e [série N](../sizes-gpu.md) que executam o Windows. Dependendo da família de VMs, a extensão instala os drivers apropriados para a NIC Connect-X.

Uma extensão também está disponível para instalar os drivers InfiniBand para [VMs do Linux](hpc-compute-infiniband-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

Esta extensão é compartível com as seguintes distribuições do sistema operacional, dependendo do suporte do driver para uma versão específica do sistema operacional. Observe a NIC InfiniBand apropriada para os tamanhos de VM da série H e N de interesse.

| Distribuição | Drivers de NIC InfiniBand |
|---|---|
| Windows 10 | CX5, CX6 |
| Windows Server 2019 | CX5, CX6 |
| Windows Server 2016 | CX3-pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-pro, CX5, CX6 |
| Windows Server 2012 | CX3-pro, CX5, CX6 |

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
| type | InfiniBandDriverWindows | string |
| typeHandlerVersion | 1.2 | INT |



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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Adicionar extensão a um conjunto de dimensionamento de máquinas virtuais

O exemplo a seguir instala a extensão mais recente da versão 1,2 do InfiniBandDriverWindows em todas as VMs compatíveis com RDMA em um conjunto de dimensionamento de máquinas virtuais existente chamado *myVMSS* implantado no grupo de recursos chamado *MyResource* Group:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
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

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Códigos de saída

A tabela a seguir descreve o significado e a ação recomendada com base nos códigos de saída do processo de instalação da extensão.

| Código do Erro | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação concluída com êxito |
| 3010 | Operação concluída com êxito. É necessário reiniciar. |
| 100 | Operação sem suporte ou não pôde ser concluída. | Possíveis causas: versão do PowerShell sem suporte, o tamanho da VM não é uma VM habilitada para InfiniBand, falha ao baixar dados. Verifique os arquivos de log para determinar a causa do erro. |
| 240, 840 | Tempo limite da operação. | Operação de teste. |
| -1 | Exceção ocorreu. | Verifique os arquivos de log para determinar a causa da exceção. |

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode arquivar um incidente de suporte por meio do [site de suporte do Azure](https://azure.microsoft.com/support/options/). Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os tamanhos habilitados para InfiniBand (' r '), consulte VMs da [série H](../sizes-hpc.md) e [série N](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Saiba mais sobre os recursos e extensões de VMs do Linux](features-linux.md)
