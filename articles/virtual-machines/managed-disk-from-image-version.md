---
title: Criar um disco gerenciado com base em uma versão de imagem
description: Crie um disco gerenciado com base em uma versão de imagem em uma galeria de imagens compartilhada.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 84ec5ecbfdc2aab2445d050cc51d2b56acb7f3fd
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202548"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Criar um disco gerenciado com base em uma versão de imagem

Se necessário, você pode exportar o sistema operacional ou um único disco de dados de uma versão de imagem como um disco gerenciado de uma versão de imagem armazenada em uma galeria de imagens compartilhada.


## <a name="cli"></a>CLI

Liste as versões de imagem em uma galeria usando [AZ SIG Image-Version List](/cli/azure/sig/image-version#az_sig_image_version_list). Neste exemplo, estamos procurando todas as versões de imagem que fazem parte da definição de imagem *myImageDefinition* na Galeria de imagens *myGallery* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Defina a `source` variável como a ID da versão da imagem e, em seguida, use [AZ Disk Create](/cli/azure/disk#az_disk_create) para criar o disco gerenciado. 

Neste exemplo, exportamos o disco do sistema operacional da versão da imagem para criar um disco gerenciado chamado *myManagedOSDisk*, na região *eastus* , em um grupo de recursos chamado *MyResource* Group. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Se você quiser exportar um disco de dados da versão da imagem, adicione `--gallery-image-reference-lun` para especificar o local do LUN do disco de dados a ser exportado. 

Neste exemplo, exportamos o disco de dados localizado no LUN 0 da versão da imagem para criar um disco gerenciado chamado *myManagedDataDisk*, na região *eastus* , em um grupo de recursos chamado *MyResource* Group. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Liste as versões de imagem em uma galeria usando [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Depois de ter todas as informações necessárias, você pode usar [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) para obter a versão da imagem de origem que deseja usar e atribuí-la a uma variável. Neste exemplo, estamos obtendo a versão da `1.0.0` imagem, da `myImageDefinition` definição, na `myGallery` Galeria de origem, no `myResourceGroup` grupo de recursos.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Depois de definir a `source` variável como a ID da versão da imagem, use [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) para criar uma configuração de disco e [New-AzDisk](/powershell/module/az.compute/new-azdisk) para criar o disco. 

Neste exemplo, exportamos o disco do sistema operacional da versão da imagem para criar um disco gerenciado chamado *myManagedOSDisk*, na região *eastus* , em um grupo de recursos chamado *MyResource* Group. 

Crie uma configuração de disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Crie o disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Se você quiser exportar um disco de dados na versão da imagem, adicione uma ID de LUN à configuração de disco para especificar o local do LUN de dados a ser exportado. 

Neste exemplo, exportamos o disco de dados localizado no LUN 0 da versão da imagem para criar um disco gerenciado chamado *myManagedDataDisk*, na região *eastus* , em um grupo de recursos chamado *MyResource* Group. 

Crie uma configuração de disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Crie o disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Próximas etapas

Você também pode criar uma versão de imagem de um disco gerenciado usando o [CLI do Azure](image-version-managed-image-cli.md) ou o [PowerShell](image-version-managed-image-powershell.md).


