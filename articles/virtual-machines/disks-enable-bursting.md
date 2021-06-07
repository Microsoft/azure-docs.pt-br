---
title: Habilitar intermitência de disco sob demanda
description: Habilite a intermitência de disco sob demanda em seu disco gerenciado.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 733d441705c7c77f0667f88151e96f76975ee0b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596392"
---
# <a name="enable-on-demand-bursting"></a>Habilitar bursting sob demanda

As unidades de estado sólido (SSD) Premium têm dois modelos de intermitência disponíveis; intermitência baseada em crédito e intermitência sob demanda. Este artigo aborda como alternar para a intermitência sob demanda. Os discos que usam o modelo sob demanda podem ultrapassar seus destinos provisionados originais. A intermitência sob demanda ocorre sempre que necessário pela carga de trabalho, até o destino de intermitência máximo. A intermitência sob demanda incorre em encargos adicionais.

Para obter detalhes sobre a intermitência de disco, consulte [intermitência de disco gerenciado](disk-bursting.md).

> [!IMPORTANT]
> Você não precisa seguir as etapas neste artigo para usar a intermitência baseada em crédito. Por padrão, a intermitência baseada em crédito é habilitada em todos os discos qualificados.

Antes de habilitar a intermitência sob demanda, entenda o seguinte:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Introdução

A intermitência sob demanda pode ser habilitada com o módulo Azure PowerShell, o CLI do Azure ou os modelos de Azure Resource Manager. Os exemplos a seguir abordam como criar um novo disco com intermitência sob demanda habilitada e habilitar a intermitência sob demanda em discos existentes.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de intermitência sob demanda estão disponíveis na versão 5.5.0 e mais recentes do módulo AZ. Como alternativa, você pode usar o [Azure cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Criar um disco de dados vazio com intermitência sob demanda

Um disco gerenciado deve ter mais de 512 GiB para habilitar a intermitência sob demanda. Substitua os `<myResourceGroupDisk>` parâmetros e e `<myDataDisk>` Execute o script a seguir para criar um SSD Premium com intermitência sob demanda:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Habilitar intermitência sob demanda em um disco existente

Um disco gerenciado deve ter mais de 512 GiB para habilitar a intermitência sob demanda. Substitua os `<myResourceGroupDisk>` `<myDataDisk>` parâmetros, e execute este comando para habilitar a intermitência sob demanda em um disco existente:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os cmdlets de intermitência sob demanda estão disponíveis na versão 2.19.0 e mais recentes do [módulo CLI do Azure](/cli/azure/install-azure-cli). Como alternativa, você pode usar o [Azure cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Criar e anexar um disco de dados de intermitência sob demanda

Um disco gerenciado deve ter mais de 512 GiB para habilitar a intermitência sob demanda. Substitua os `<yourDiskName>` `<yourResourceGroup>` parâmetros,, e `<yourVMName>` , em seguida, execute os seguintes comandos para criar um SSD Premium com intermitência sob demanda:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Habilitar intermitência sob demanda em um disco-CLI existente

Um disco gerenciado deve ter mais de 512 GiB para habilitar a intermitência sob demanda. Substitua os `<myResourceGroupDisk>` `<yourDiskName>` parâmetros e e execute este comando para habilitar a intermitência sob demanda em um disco existente:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Com a `2020-09-30` API de disco, você pode habilitar a intermitência sob demanda no SSDs Premium recém-criado ou existente com mais de 512 Gib. A `2020-09-30` API introduziu uma nova propriedade, `burstingEnabled` . Por padrão, essa propriedade é definida como false. O modelo de exemplo a seguir cria um SSD 1TiB Premium no Oeste EUA Central, com a intermitência de disco habilitada:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Próximas etapas

Para saber como obter informações sobre os recursos de intermitência, confira [métricas de intermitência de disco](disks-metrics.md).