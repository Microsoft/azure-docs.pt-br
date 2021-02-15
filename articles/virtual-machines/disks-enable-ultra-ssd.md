---
title: Ultra discos para VMs – Azure Managed disks
description: Saiba mais sobre os ultra discos para VMs do Azure
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 12/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 9c3c1acbc2606d882ad45744457137be5014bc4c
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093478"
---
# <a name="using-azure-ultra-disks"></a>Usando os ultra discos do Azure

Este artigo explica como implantar e usar um ultra Disk, para obter informações conceituais sobre ultra disks, consulte [quais tipos de disco estão disponíveis no Azure?](disks-types.md#ultra-disk).

Os ultra discos do Azure oferecem alta taxa de transferência, IOPS alta e armazenamento de disco consistente de baixa latência para VMs (máquinas virtuais) IaaS do Azure. Essa nova oferta fornece o melhor em desempenho de linha, nos mesmos níveis de disponibilidade que nossas ofertas de discos atuais. Um grande benefício dos ultra discos é a capacidade de alterar dinamicamente o desempenho do SSD junto com suas cargas de trabalho sem a necessidade de reiniciar suas VMs. Os discos Ultra são adequados para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas.

## <a name="ga-scope-and-limitations"></a>Limitações e escopo de GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinar a disponibilidade da região e do tamanho da VM

### <a name="vms-using-availability-zones"></a>VMs que usam zonas de disponibilidade

Para aproveitar os ultra discos, você precisa determinar em qual zona de disponibilidade você está. Nem toda região dá suporte a todos os tamanhos de VM com ultra discos. Para determinar se a região, a zona e o tamanho da VM dão suporte a ultra discos, execute um dos seguintes comandos, certifique-se de substituir os valores de **região**, **vmSize** e **assinatura** primeiro:

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

A resposta será semelhante ao formulário abaixo, em que X é a zona a ser usada para implantação na sua região escolhida. X pode ser 1, 2 ou 3.

Preservar o valor de **zonas** , representa sua zona de disponibilidade e você precisará dela para implantar um ultra Disk.

|ResourceType  |Nome  |Localização  |Zonas  |Restrição  |Funcionalidade  |Valor  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se não houver resposta do comando, o tamanho da VM selecionado não terá suporte com ultra discos na região selecionada.

Agora que você sabe em qual zona implantar, siga as etapas de implantação neste artigo para implantar uma VM com um ultra Disk anexado ou anexar um ultra Disk a uma VM existente.

### <a name="vms-with-no-redundancy-options"></a>VMs sem opções de redundância

Ultra discos implantados em regiões selecionadas devem ser implantados sem nenhuma opção de redundância, por enquanto. No entanto, nem todo tamanho de disco que dá suporte a ultra discos pode estar nessa região. Para determinar quais tamanhos de disco dão suporte a ultra discos, você pode usar qualquer um dos trechos de código a seguir. Certifique-se de substituir `vmSize` os `subscription` valores e primeiro:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

A resposta será semelhante ao seguinte formulário, `UltraSSDAvailable   True` indica se o tamanho da VM dá suporte a ultra discos nessa região.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Implantar um ultra Disk usando o Azure Resource Manager

Primeiro, determine o tamanho da VM a ser implantado. Para obter uma lista de tamanhos de VM com suporte, consulte a seção [escopo e limitações do GA](#ga-scope-and-limitations) .

Se você quiser criar uma VM com vários ultra discos, consulte o exemplo [criar uma VM com vários ultra discos](https://aka.ms/ultradiskArmTemplate).

Se você pretende usar seu próprio modelo, certifique-se de que **apiVersion** para `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` esteja definido como `2018-06-01` (ou posterior).

Defina a SKU do disco como **UltraSSD_LRS**, em seguida, defina a capacidade do disco, o IOPS, a zona de disponibilidade e a taxa de transferência em Mbps para criar um ultra Disk.

Depois que a VM for provisionada, será possível particionar e formatar os discos de dados e configurá-los para suas cargas de trabalho.


## <a name="deploy-an-ultra-disk"></a>Implantar um ultra Disk

# <a name="portal"></a>[Portal](#tab/azure-portal)

Esta seção aborda a implantação de uma máquina virtual equipada com um ultra Disk como um disco de dados. Ele pressupõe que você tenha familiaridade com a implantação de uma máquina virtual, se não tiver, consulte nosso [início rápido: criar uma máquina virtual do Windows no portal do Azure](./windows/quick-create-portal.md).

- Entre no [portal do Azure](https://portal.azure.com/) e navegue até implantar uma máquina virtual (VM).
- Certifique-se de escolher um [tamanho de VM e uma região com suporte](#ga-scope-and-limitations).
- Selecione **zona de disponibilidade** em **Opções de disponibilidade**.
- Preencha as entradas restantes com seleções de sua escolha.
- Escolha **Discos**.

![Captura de tela de fluxo de criação de VM, folha básica.](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Na folha discos, selecione **Sim** para **habilitar a compatibilidade de ultra Disk**.
- Selecione **criar e anexar um novo disco** para anexar um ultra Disk agora.

![Captura de tela de fluxo de criação de VM, folha de disco, ultra está habilitada e criar e anexar um novo disco é realçado.](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Na folha **criar um novo disco** , insira um nome e, em seguida, selecione **alterar tamanho**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-create-new-disk-flow.png" alt-text="Captura de tela de criar uma nova folha de disco, alterar o tamanho realçado.":::


- Altere o **tipo de armazenamento** para **ultra Disk**.
- Altere os valores de **tamanho de disco personalizado (GIB)**, **IOPS de disco** e **taxa de transferência de disco** para aqueles de sua escolha.
- Selecione **OK** em ambas as folhas.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Captura de tela da folha selecionar um tamanho de disco, ultra Disk selecionado para o tipo de armazenamento, outros valores realçados.":::

- Continue com a implantação da VM, ela será a mesma que você implantaria qualquer outra VM.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, determine o tamanho da VM a ser implantado. Consulte a seção [escopo e limitações do GA](#ga-scope-and-limitations) para obter uma lista de tamanhos de VM com suporte.

Você deve criar uma VM que seja capaz de usar ultra disks, a fim de anexar um ultra Disk.

Substitua ou defina as **$vmname**, **$rgname**, **$diskname**, **$Location**, **$password**$user **variáveis com** seus próprios valores. Defina **$Zone**  para o valor da zona de disponibilidade obtida do [início deste artigo](#determine-vm-size-and-region-availability). Em seguida, execute o seguinte comando da CLI para criar uma VM ultra habilitada:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, determine o tamanho da VM a ser implantado. Consulte a seção [escopo e limitações do GA](#ga-scope-and-limitations) para obter uma lista de tamanhos de VM com suporte.

Para usar ultra disks, você deve criar uma VM que seja capaz de usar ultra disks. Substitua ou defina as variáveis **$resourcegroup** e **$vmName** com seus próprios valores. Defina **$Zone** para o valor da zona de disponibilidade obtida do [início deste artigo](#determine-vm-size-and-region-availability). Em seguida, execute o seguinte comando [New-AzVm](/powershell/module/az.compute/new-azvm) para criar uma VM ultra habilitada:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>Criar e anexar o disco

Depois que a VM tiver sido implantada, você poderá criar e anexar um ultra Disk a ela, usar o seguinte script:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>Implantar um tamanho de setor de byte de 512 bytes

# <a name="portal"></a>[Portal](#tab/azure-portal)

Atualmente, o portal do Azure não dá suporte à criação de um ultra Disk com um tamanho de setor de 512 bytes. Você pode criar um ultra Disk com um tamanho de setor de 512 bytes usando o módulo Azure PowerShell ou a CLI do Azure, em vez disso.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, determine o tamanho da VM a ser implantado. Consulte a seção [escopo e limitações do GA](#ga-scope-and-limitations) para obter uma lista de tamanhos de VM com suporte.

Você deve criar uma VM que seja capaz de usar ultra disks, a fim de anexar um ultra Disk.

Substitua ou defina as **$vmname**, **$rgname**, **$diskname**, **$Location**, **$password**$user **variáveis com** seus próprios valores. Defina **$Zone**  para o valor da zona de disponibilidade obtida do [início deste artigo](#determine-vm-size-and-region-availability). Em seguida, execute o seguinte comando da CLI para criar uma VM com um ultra Disk com um tamanho de setor de 512 bytes:

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, determine o tamanho da VM a ser implantado. Consulte a seção [escopo e limitações do GA](#ga-scope-and-limitations) para obter uma lista de tamanhos de VM com suporte.

Para usar ultra disks, você deve criar uma VM que seja capaz de usar ultra disks. Substitua ou defina as variáveis **$resourcegroup** e **$vmName** com seus próprios valores. Defina **$Zone** para o valor da zona de disponibilidade obtida do [início deste artigo](#determine-vm-size-and-region-availability). Em seguida, execute o seguinte comando [New-AzVm](/powershell/module/az.compute/new-azvm) para criar uma VM ultra habilitada:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

Para criar e anexar um ultra Disk que tem um tamanho de setor de 512 bytes, você pode usar o seguinte script:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>Anexar um ultra Disk

# <a name="portal"></a>[Portal](#tab/azure-portal)

Como alternativa, se sua VM existente estiver em uma zona de região/disponibilidade que seja capaz de usar ultra disks, você poderá usar ultra discos sem precisar criar uma nova VM. Habilitando ultra discos em sua VM existente e, em seguida, anexando-os como discos de dados. Para habilitar a compatibilidade de ultra Disk, você deve parar a VM. Depois de parar a VM, você pode habilitar a compatibilidade e reiniciar a VM. Quando a compatibilidade estiver habilitada, você poderá anexar um disco ultra:

- Navegue até sua VM e interrompa-a, aguarde até que ela seja desalocada.
- Depois que a VM tiver sido desalocada, selecione **discos**.
- Selecione **Edit** (Editar).

![Captura de tela de uma folha de disco de VM existente, editar é realçado.](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selecione **Sim** para **habilitar a compatibilidade de ultra Disk**.

![Captura de tela de habilitar a compatibilidade de ultra Disk.](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Clique em **Salvar**.
- Selecione **adicionar disco de dados** e, em seguida, no menu suspenso para **nome** , selecione **criar disco**.

![Captura de tela da folha de disco, adicionando um novo disco.](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Preencha um nome para o novo disco e selecione **alterar tamanho**.
- Altere o **tipo de conta** para **ultra Disk**.
- Altere os valores de **tamanho de disco personalizado (GIB)**, **IOPS de disco** e **taxa de transferência de disco** para aqueles de sua escolha.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Captura de tela da folha selecionar um tamanho de disco, ultra Disk selecionado para o tipo de armazenamento, outros valores realçados.":::

- Selecione **OK** e, em seguida, selecione **criar**.
- Depois de retornar à folha do disco, selecione **salvar**.
- Inicie sua VM novamente.

![Captura de tela da folha discos em sua VM.](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Como alternativa, se sua VM existente estiver em uma zona de região/disponibilidade que seja capaz de usar ultra disks, você poderá usar ultra discos sem precisar criar uma nova VM.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Habilitar a compatibilidade de ultra disco em uma VM-CLI existente

Se sua VM atender aos requisitos descritos no [escopo e nas limitações do GA](#ga-scope-and-limitations) e estiver na [zona apropriada para sua conta](#determine-vm-size-and-region-availability), você poderá habilitar a compatibilidade de ultra disco em sua VM.

Para habilitar a compatibilidade de ultra Disk, você deve parar a VM. Depois de parar a VM, você pode habilitar a compatibilidade e reiniciar a VM. Quando a compatibilidade estiver habilitada, você poderá anexar um disco ultra:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Criar um ultra Disk-CLI

Agora que você tem uma VM capaz de conectar ultra disks, você pode criar e anexar um ultra Disk a ele.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>Anexar o disco-CLI

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Como alternativa, se sua VM existente estiver em uma zona de região/disponibilidade que seja capaz de usar ultra disks, você poderá usar ultra discos sem precisar criar uma nova VM.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Habilitar a compatibilidade de ultra disco em uma VM existente-PowerShell

Se sua VM atender aos requisitos descritos no [escopo e nas limitações do GA](#ga-scope-and-limitations) e estiver na [zona apropriada para sua conta](#determine-vm-size-and-region-availability), você poderá habilitar a compatibilidade de ultra disco em sua VM.

Para habilitar a compatibilidade de ultra Disk, você deve parar a VM. Depois de parar a VM, você pode habilitar a compatibilidade e reiniciar a VM. Quando a compatibilidade estiver habilitada, você poderá anexar um disco ultra:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Criar e anexar um ultra Disk-PowerShell

Agora que você tem uma VM capaz de usar ultra disks, você pode criar e anexar um ultra Disk a ele:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Ajustar o desempenho de um ultra Disk

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ultra disks oferece um recurso exclusivo que permite que você ajuste seu desempenho. Você pode fazer esses ajustes da portal do Azure, nos próprios discos.

- Navegue até sua VM e selecione **discos**.
- Selecione o ultra Disk do qual você gostaria de modificar o desempenho.

![Captura de tela da folha discos em sua VM, o ultra Disk está realçado.](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selecione **configuração** e faça suas modificações.
- Clique em **Salvar**.

![Captura de tela da folha de configuração em seu ultra disco, tamanho do disco, IOPS e taxa de transferência são realçados, salvar é realçado.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Ultra disks oferece um recurso exclusivo que permite que você ajuste seu desempenho, o comando a seguir descreve como usar esse recurso:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ajustar o desempenho de um ultra Disk usando o PowerShell

Ultra discos têm um recurso exclusivo que permite que você ajuste o desempenho, o comando a seguir é um exemplo que ajusta o desempenho sem precisar desanexar o disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>Próximas etapas

- [Use os ultra discos do Azure no serviço kubernetes do Azure (versão prévia)](../aks/use-ultra-disks.md).
- [Migre o disco de log para um ultra Disk](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md).
