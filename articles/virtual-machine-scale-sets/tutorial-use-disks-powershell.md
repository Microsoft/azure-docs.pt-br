---
title: Tutorial – Criar e usar discos para conjuntos de dimensionamento com o Azure PowerShell | Microsoft Docs
description: Saiba como usar o Azure PowerShell para criar e usar Managed Disks com conjuntos de dimensionamento de máquinas virtuais, incluindo como adicionar, preparar, listar e desanexar discos.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6035a6ddd690db456edfa5777ca2d41e4be8b919
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728592"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: criar e usar discos com um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell

Conjuntos de dimensionamento de máquinas virtuais usam discos para armazenar o sistema operacional da instância de VM, aplicativos e dados. Ao criar e gerenciar um conjunto de dimensionamento, é importante escolher um tamanho e uma configuração de disco apropriados para a carga de trabalho esperada. Este tutorial aborda como criar e gerenciar os discos de VM. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>Discos padrão do Azure
Quando um conjunto de dimensionamento é criado ou dimensionado, dois discos são automaticamente anexados a cada instância de VM. 

**Disco do sistema operacional** – os discos do sistema operacional podem ser dimensionados para até 2 TB e hospedar o sistema operacional da instância de VM. O disco do sistema operacional é rotulado */dev/sda* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. Devido a essa configuração, o disco do sistema operacional **não deve** hospedar aplicativos ou dados. Para aplicativos e dados, utilize um disco de dados, que é detalhado posteriormente neste artigo. 

**Disco temporário** – discos temporários utilizam uma unidade de estado sólido localizada no mesmo host do Azure que a instância da VM. Tratam-se de discos de alto desempenho que podem ser usados para operações como o processamento de dados temporário. No entanto, se a instância de VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo tamanho da instância de VM. Os discos temporários são rotulados */dev/sdb* e têm um ponto de montagem de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamanhos do disco temporário
| Type | Tamanhos comuns | Tamanho máximo do disco temporário (GiB) |
|----|----|----|
| [Propósito geral](../virtual-machines/windows/sizes-general.md) | Série A, B e D | 1600 |
| [Computação otimizada](../virtual-machines/windows/sizes-compute.md) | Série F | 576 |
| [Memória otimizada](../virtual-machines/windows/sizes-memory.md) | Série D, E, G e M | 6144 |
| [Armazenamento otimizado](../virtual-machines/windows/sizes-storage.md) | Série L | 5630 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Série N | 1440 |
| [Alto desempenho](../virtual-machines/windows/sizes-hpc.md) | Séries A e H | 2000 |


## <a name="azure-data-disks"></a>Discos de dados do Azure
Outros discos de dados podem ser adicionados caso você precise instalar aplicativos e armazenar dados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável e responsivo é desejado. Cada disco de dados tem uma capacidade máxima de 4 TB. O tamanho da instância de VM determina quantos discos de dados podem ser anexados. Para cada vCPU da VM, podem ser anexados dois discos de dados.

### <a name="max-data-disks-per-vm"></a>Máximo de discos de dados por VM
| Type | Tamanhos comuns | Máximo de discos de dados por VM |
|----|----|----|
| [Propósito geral](../virtual-machines/windows/sizes-general.md) | Série A, B e D | 64 |
| [Computação otimizada](../virtual-machines/windows/sizes-compute.md) | Série F | 64 |
| [Memória otimizada](../virtual-machines/windows/sizes-memory.md) | Série D, E, G e M | 64 |
| [Armazenamento otimizado](../virtual-machines/windows/sizes-storage.md) | Série L | 64 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Série N | 64 |
| [Alto desempenho](../virtual-machines/windows/sizes-hpc.md) | Série A e H | 64 |


## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual
O Azure fornece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard
O armazenamento Standard é apoiado por HDDs e oferece armazenamento e desempenho econômicos. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

### <a name="premium-disk"></a>Disco Premium
Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Esses discos são recomendados para as VMs que executam cargas de trabalho de produção. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS e FS. Ao escolher o tamanho de um disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for menor que 128 GB, o tipo de disco é P10. Se o tamanho do disco está entre 129 e 512 GB, o tamanho é P20. Acima de 512 GB, o tamanho é um P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium
|Tipo de disco de armazenamento Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| IOPS máxima por disco | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Taxa de transferência por disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, uma VM Standard_GS5 pode atingir o máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, consulte [Tamanhos de VM do Windows](../virtual-machines/windows/sizes.md).


## <a name="create-and-attach-disks"></a>Criar e anexar discos
Você pode criar e anexar discos ao criar um conjunto de dimensionamento ou com um conjunto de dimensionamento existente.

### <a name="attach-disks-at-scale-set-creation"></a>Anexar discos na criação do conjunto de dimensionamento
Crie um conjunto de dimensionamento de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Quando solicitado, forneça um nome de usuário e senha para as instâncias da VM. Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985.

Dois discos são criados com o parâmetro `-DataDiskSizeGb`. É o primeiro disco tem *64* GB de tamanho e o segundo, *128* GB. Quando solicitado, forneça suas próprias credenciais administrativas desejadas para as instâncias de VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

Leva alguns minutos para criar e configurar todos os recursos e as instâncias de VM do conjunto de dimensionamento.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anexar um disco ao conjunto de dimensionamento existente
Você também pode anexar discos a um conjunto de dimensionamento existente. Use o conjunto de dimensionamento criado na etapa anterior para adicionar outro disco com [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk). O exemplo a seguir anexa um disco adicional de *128* GB em um conjunto de dimensionamento existente:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Preparar os discos de dados
Os discos que são criados e anexados às suas instâncias de VM do conjunto de dimensionamento são discos brutos. Antes que você possa usá-los com seus dados e aplicativos, os discos devem ser preparados. Para preparar os discos, crie uma partição, crie um sistema de arquivos e monte-os.

Para automatizar o processo em várias instâncias de VM em um conjunto de dimensionamento, você pode usar a Extensão de Script Personalizado do Azure. Esta extensão pode executar scripts localmente em cada instância de VM, como preparar discos de dados anexados. Para obter mais informações, consulte a [Visão geral da Extensão de Script Personalizado](../virtual-machines/windows/extensions-customscript.md).


O exemplo a seguir executa um script de um repositório GitHub de exemplo em cada instância de VM com [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension), que prepara todos os discos de dados brutos anexados:


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Para confirmar que os discos foram preparados corretamente, faça a RDP a uma das instâncias de VM. 

Primeiro, obtenha o objeto do balanceador de carga com [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Depois, exiba as regras NAT de entrada com [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig). A lista de regras NAT de *FrontendPort* para cada instância de VM escutada pela RDP. Por fim, obtenha o endereço IP público do balanceador de carga com [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

Para conectar-se à sua VM, especifique seu próprio endereço IP público e número da porta da instância de VM necessária, conforme mostrado com os comandos anteriores. Quando solicitado, insira as credenciais usadas quando você criou o conjunto de dimensionamento. Caso use o Azure Cloud Shell, execute esta etapa de um prompt do PowerShell local ou do Cliente da Área de Trabalho Remota. O exemplo a seguir se conecta a uma instância de VM em *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Abra uma sessão do PowerShell local na instância de VM e examine os discos conectados com [Get-Disk](/powershell/module/storage/get-disk):

```powershell
Get-Disk
```

A saída de exemplo a seguir mostra que os três discos de dados estão anexados à instância de VM.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Examine os sistemas de arquivos e os pontos de montagem na instância de VM da seguinte maneira:

```powershell
Get-Partition
```

A saída de exemplo a seguir mostra que letras da unidade são atribuídas aos três discos de dados:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

Os discos em cada instância de VM em sei dimensionamento são automaticamente preparados da mesma maneira. À medida que seu conjunto de dimensionamento escala verticalmente, os discos de dados necessários são anexados às novas instâncias de VM. A Extensão de Script Personalizado também é executada para preparar os discos automaticamente.

Feche a sessão de conexão de área de trabalho remota com a instância de VM.


## <a name="list-attached-disks"></a>Listar discos anexados
Para exibir informações sobre discos anexados a um conjunto de dimensionamento, use [Get-AzVmss](/powershell/module/az.compute/get-azvmss) da seguinte maneira:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Na propriedade *VirtualMachineProfile.StorageProfile*, é exibida a lista *DataDisks*. São exibidas informações sobre o tamanho do disco, a camada de armazenamento e o LUN (número de unidade lógica). A saída de exemplo abaixo detalha os três discos de dados anexados ao conjunto de dimensionamento:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Desanexar um disco
Quando não precisar mais de determinado disco, você poderá desanexá-lo do conjunto de dimensionamento. O disco é removido de todas as instâncias de VM no conjunto de dimensionamento. Para desanexar um disco de um conjunto de dimensionamento, use [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) e especifique o LUN do disco. Os LUNs são mostrados na saída de [Get-AzVmss](/powershell/module/az.compute/get-azvmss) na seção anterior. O exemplo a seguir desanexa o LUN *3* do conjunto de dimensionamento:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o conjunto de dimensionamento e os discos, exclua o grupo de recursos e todos os seus recursos com [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). O parâmetro `-Force` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso. O parâmetro `-AsJob` retorna o controle ao prompt sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar e usar discos com conjuntos de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Siga para o próximo tutorial para aprender a usar uma imagem personalizada para suas instâncias de VM do conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Usar uma imagem personalizada para instâncias de VM do conjunto de dimensionamento](tutorial-use-custom-image-powershell.md)
