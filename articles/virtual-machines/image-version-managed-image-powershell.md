---
title: Clonar uma imagem gerenciada para uma galeria de imagens compartilhadas
description: Saiba como usar Azure PowerShell para clonar uma imagem gerenciada para uma versão de imagem em uma galeria de imagens compartilhada.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: cec4f258cfaa0584c24f2cfc92ec1a536f6277cc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556973"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>Clonar uma imagem gerenciada para uma imagem da Galeria de imagens compartilhadas

Se você tiver uma imagem gerenciada existente que deseja clonar e mover para uma galeria de imagens compartilhada, poderá criar uma imagem da Galeria de imagens compartilhada diretamente da imagem gerenciada. Depois de testar a nova imagem, você pode excluir a imagem gerenciada de origem. Você também pode migrar de uma imagem gerenciada para uma galeria de imagens compartilhada usando o [CLI do Azure](image-version-managed-image-cli.md).

As imagens em uma galeria de imagens têm dois componentes, que serão criados neste exemplo:
- Uma **definição de imagem** contém informações sobre a imagem e os requisitos para usá-la. Isso inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é usada para criar uma VM ao usar uma galeria de imagens compartilhada. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você cria uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter uma imagem gerenciada existente. Se a imagem gerenciada contiver um disco de dados, o tamanho do disco de dados não poderá ser superior a 1 TB.

Ao trabalhar com este artigo, substitua o grupo de recursos e os nomes de VM quando for necessário.

## <a name="get-the-gallery"></a>Obter a Galeria

Você pode listar todas as galerias e definições de imagem por nome. Os resultados estão no formato `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Depois de encontrar a Galeria correta, crie uma variável para usar mais tarde. Este exemplo obtém a galeria chamada *myGallery* no grupo de recursos *MyResource* Group.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para as imagens. Eles são usados para gerenciar informações sobre a imagem. Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. 

Ao fazer a definição de imagem, verifique se o tem todas as informações corretas. Como as imagens gerenciadas são sempre generalizadas, você deve definir `-OsState generalized` . 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](./shared-image-galleries.md#image-definitions).

Crie a definição de imagem usando [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a definição da imagem é chamada *myImageDefinition* e é para um sistema operacional generalizado do Windows. Para criar uma definição para imagens usando um sistema operacional Linux, use `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Obter a imagem gerenciada

Você pode ver uma lista de imagens que estão disponíveis em um grupo de recursos usando [Get-AzImage](/powershell/module/az.compute/get-azimage). Depois que você souber o nome da imagem e em qual grupo de recursos ela está, poderá usar `Get-AzImage` novamente para obter o objeto de imagem e armazená-lo em uma variável para uso posterior. Este exemplo obtém uma imagem chamada *myImage* do grupo de recursos "myResourceGroup" e o atribui à variável *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem da imagem gerenciada usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a versão da imagem é *1.0.0* e ela é replicado para os datacenters *Centro-Oeste dos EUA* e *Centro-Sul dos EUA*. Ao escolher regiões de destino para replicação, lembre-se de que você também precisa incluir a região de *origem* como um destino para replicação. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Pode levar um tempo para replicar a imagem para todas as regiões de destino, portanto, criamos um trabalho para podermos acompanhar o progresso. Para ver o progresso, digite `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> Você precisa esperar que a versão da imagem seja compilada e replicada completamente antes de poder usar a mesma imagem gerenciada para criar outra versão da imagem. 
>
> Você também pode armazenar sua imagem no armazenamento Premium adicionando `-StorageAccountType Premium_LRS` ou [armazenando o armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `-StorageAccountType Standard_ZRS` ao criar a versão da imagem.
>

## <a name="delete-the-managed-image"></a>Excluir a imagem gerenciada

Depois de verificar se a nova versão da imagem está funcionando corretamente, você pode excluir a imagem gerenciada.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Depois de verificar que a replicação foi concluída, você pode criar uma VM a partir da [imagem generalizada](vm-generalized-image-version-powershell.md).

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).