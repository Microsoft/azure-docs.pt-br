---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 742e0028b1f92beb8300cc97f09d8292259fbc0a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712444"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Habilitar e implantar o SSDs ultra do Azure (visualização)

Unidades de estado sólido de ultra do Azure alta taxa de transferência de oferta da (SSD) (versão prévia), IOPS alta e armazenamento de disco de baixa latência consistente para máquinas virtuais de IaaS do Azure (VMs). Essa nova oferta fornece o melhor em desempenho de linha, nos mesmos níveis de disponibilidade que nossas ofertas de discos atuais. Um dos maiores benefícios do SSDs ultra é a capacidade de alterar dinamicamente o desempenho da SSD, juntamente com suas cargas de trabalho sem a necessidade de reiniciar as VMs. Ultra SSDs é adequado para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho pesadas de transação.

Atualmente, o SSDs ultra estão em visualização e você deve [registrar](https://aka.ms/UltraSSDPreviewSignUp) na visualização para acessá-los.

## <a name="determine-your-availability-zone"></a>Determinar sua zona de disponibilidade

Após a aprovação, você precisa determinar qual zona de disponibilidade que você está, para usar o ultra SSDs. Execute um dos comandos a seguir para determinar qual zona no Leste dos EUA 2 para implantar seu disco ultra para:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

A resposta será semelhante ao formulário abaixo, onde X é a zona a ser usado para implantar no Leste dos EUA 2. X pode ser 1, 2 ou 3.

Preservar os **zonas** valor, ele representa a zona de disponibilidade e você precisará dele para implantar um SSD ultra.

|ResourceType  |Nome  |Location  |Zonas  |Restrição  |Recurso  |Valor  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se não houve resposta do comando e, em seguida, seu registro para o recurso é ainda pendente ou aprovado ainda.

Agora que você sabe em qual zona implantar, siga as etapas neste artigo para fazer sua primeira implantação de VMs com o ultra SSD.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Implantar um SSD ultra usando o Azure Resource Manager

Primeiro, determine o tamanho da VM para implantar. Como parte dessa versão prévia, há suporte apenas para as famílias de VM DsV3 e EsV3. Confira a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre esses tamanhos de VM.

Se você quiser criar uma VM com vários SSDs ultra, consulte o exemplo [criar uma VM com vários SSD ultra](https://aka.ms/UltraSSDTemplate).

Se você pretende usar seu próprio modelo, verifique se **apiVersion** para `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` está definido como `2018-06-01` (ou posterior).

Defina a sku de disco para **UltraSSD_LRS**, em seguida, defina a capacidade do disco, IOPS, zona de disponibilidade e taxa de transferência em MBps para criar um disco ultra.

Depois que a VM for provisionada, será possível particionar e formatar os discos de dados e configurá-los para suas cargas de trabalho.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Implantar um SSD ultra usando a CLI

Primeiro, determine o tamanho da VM para implantar. Como parte dessa versão prévia, há suporte apenas para as famílias de VM DsV3 e EsV3. Confira a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre esses tamanhos de VM.

Para usar o ultra SSDs, você deve criar uma VM que é capaz de usar o ultra SSDs.

Substituir ou definir as **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** variáveis com seus próprios valores. Definir **$zone** para o valor de sua zona de disponibilidade que você obteve o [início deste artigo](#determine-your-availability-zone). Em seguida, execute o seguinte comando CLI para criar uma VM habilitada ultra:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Criar um SSD ultra usando a CLI

Agora que você tem uma VM que é capaz de usar o ultra SSDs, você pode criar e anexar um SSD ultra a ele.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Ajustar o desempenho de um SSD ultra usando a CLI

SSDs Ultra oferecem um recurso exclusivo que permite que você ajuste seu desempenho, o comando a seguir ilustra como usar esse recurso:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Implantar um SSD ultra usando o PowerShell

Primeiro, determine o tamanho da VM para implantar. Como parte dessa versão prévia, há suporte apenas para as famílias de VM DsV3 e EsV3. Confira a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre esses tamanhos de VM.

Para usar o ultra SSDs, você deve criar uma VM que é capaz de usar o ultra SSDs. Substituir ou definir as **$resourcegroup** e **$vmName** variáveis com seus próprios valores. Definir **$zone** para o valor de sua zona de disponibilidade que você obteve o [início deste artigo](#determine-your-availability-zone). Em seguida, execute o seguinte [New-AzVm](/powershell/module/az.compute/new-azvm) comando para criar um ultra habilitado VM:

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

### <a name="create-an-ultra-ssd-using-powershell"></a>Criar um SSD ultra usando o PowerShell

Agora que você tem uma VM que é capaz de usar o ultra SSDs, você pode criar e anexar um SSD ultra a ele:

```powershell
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
-DiskName 'Disk02' `
-Disk $diskconfig;
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Ajustar o desempenho de um SSD ultra usando o PowerShell

Ultra SSDs têm uma funcionalidade exclusiva que permite que você ajuste seu desempenho, o comando a seguir está um exemplo que ajusta o desempenho sem a necessidade de desanexar o disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Próximas etapas

Se você quiser experimentar o novo tipo de disco [solicitar acesso à visualização com essa pesquisa](https://aka.ms/UltraSSDPreviewSignUp).