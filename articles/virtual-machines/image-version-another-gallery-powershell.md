---
title: Copiar uma imagem de outra galeria usando o PowerShell
description: Copie uma imagem de outra galeria usando Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 85bc6f4468e0365d247b0adc204bd53ce9fa3006
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676709"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Copiar uma imagem de outra galeria usando o PowerShell

Se você tiver várias galerias em sua organização, poderá criar imagens de imagens armazenadas em outras galerias. Por exemplo, você pode ter uma galeria de desenvolvimento e teste para criar e testar novas imagens. Quando eles estiverem prontos para serem usados na produção, você poderá copiá-los para uma galeria de produção usando este exemplo. Você também pode criar uma imagem de uma imagem em outra galeria usando o [CLI do Azure](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter uma galeria de origem, uma definição de imagem e uma versão de imagem existentes. Você também deve ter uma galeria de destino. 

A versão da imagem de origem deve ser replicada para a região onde a Galeria de destino está localizada. 

Criaremos uma nova definição de imagem e a versão da imagem na Galeria de destino.


Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.


## <a name="get-the-source-image"></a>Obter a imagem de origem 

Você precisará de informações da definição da imagem de origem para poder criar uma cópia dela na Galeria de destino.

Liste informações sobre as galerias existentes, as definições de imagem e as versões de imagem usando o cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) .

Os resultados estão no formato `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Depois de ter todas as informações necessárias, você pode obter a ID da versão da imagem de origem usando [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). Neste exemplo, estamos obtendo a versão da `1.0.0` imagem, da `myImageDefinition` definição, na `myGallery` Galeria de origem, no `myResourceGroup` grupo de recursos.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Criar a definição de imagem 

Você precisa criar uma nova definição de imagem que corresponda à definição de imagem de sua origem. Você pode ver todas as informações necessárias para recriar a definição de imagem usando [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


A saída será parecida com esta:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Crie uma nova definição de imagem, na Galeria de destino, usando o cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) e as informações da saída acima.


Neste exemplo, a definição de imagem é chamada *myDestinationImgDef* na galeria chamada *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>Criar a versão da imagem

Crie uma versão de imagem usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Será necessário passar a ID da imagem de origem no `--managed-image` parâmetro para criar a versão da imagem na Galeria de destino. 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a Galeria de destino é denominada *myDestinationGallery*, no grupo de recursos *myDestinationRG* , na localização *oeste dos EUA* . A versão da nossa imagem é a *1.0.0* e vamos criar uma réplica na região *do Sul EUA Central* e 2 réplicas na região *oeste dos EUA* . 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

Pode levar um tempo para replicar a imagem para todas as regiões de destino, portanto, criamos um trabalho para podermos acompanhar o progresso. Para ver o andamento do trabalho, digite `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Você precisa esperar que a versão da imagem seja compilada e replicada completamente antes de poder usar a mesma imagem gerenciada para criar outra versão da imagem.
>
> Você também pode armazenar a imagem no armazenamento Premium adicionando `-StorageAccountType Premium_LRS`, ou no [Armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `-StorageAccountType Standard_ZRS` ao criar a versão da imagem.
>


## <a name="next-steps"></a>Próximas etapas

Crie uma VM com base em uma versão de imagem [generalizada](vm-generalized-image-version-powershell.md) ou [especializada](vm-specialized-image-version-powershell.md) .

O [Construtor de imagens do Azure (visualização)](./image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](./linux/image-builder-gallery-update-image-version.md). 

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).