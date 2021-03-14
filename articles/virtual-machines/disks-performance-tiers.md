---
title: Alterar o desempenho dos Azure Managed disks-CLI/PowerShell
description: Saiba como alterar as camadas de desempenho para discos gerenciados existentes usando o módulo Azure PowerShell ou o CLI do Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 429845aa22b6d069b8d7233132de8eb3b24b2985
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043670"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>Altere o nível de desempenho usando o módulo Azure PowerShell ou o CLI do Azure

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar um disco de dados vazio com uma camada superior à camada de linha de base

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Criar um disco do sistema operacional com uma camada superior à camada de linha de base de uma imagem do Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>Atualizar a camada de um disco

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>Mostrar a camada de um disco

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>Alterar o nível de desempenho de um disco sem tempo de inatividade (versão prévia)

Você também pode alterar seu nível de desempenho sem tempo de inatividade, portanto, não é necessário desalocar sua VM ou desanexar o disco para alterar a camada. Para obter mais informações e o link de inscrição para a versão prévia, consulte a seção [alterar o tipo de desempenho sem tempo de inatividade (versão prévia)](#change-performance-tier-without-downtime-preview) .


O script a seguir atualizará a camada de um disco maior do que a camada de linha de base usando o modelo de exemplo [CreateUpdateDataDiskWithTier.jsem](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json). Substitua `<yourSubScriptionID>` , `<yourResourceGroupName>` , `<yourDiskName>` , `<yourDiskSize>` e `<yourDesiredPerformanceTier>` Execute o script:

 ```cli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
diskName=<yourDiskName>
diskSize=<yourDiskSize>
performanceTier=<yourDesiredPerformanceTier>
region=EastUS2EUAP

 az login

 az account set --subscription $subscriptionId

 az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
--parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
```

Uma alteração no nível de desempenho pode levar até 15 minutos para ser concluída. Para confirmar se o disco alterou as camadas, use o seguinte comando:

```cli
az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-12-01 --query [properties.tier] -o tsv
```

## <a name="next-steps"></a>Próximas etapas

Se você precisar redimensionar um disco para tirar proveito dos níveis de desempenho mais alto, consulte estes artigos:

- [Expandir discos rígidos virtuais em uma VM do Linux com a CLI do Azure](linux/expand-disks.md)
- [Expandir um disco gerenciado conectado a uma máquina virtual do Windows](windows/expand-os-disk.md)
