---
title: Criar uma imagem de uma VM
description: Saiba como usar Azure PowerShell para criar uma imagem em uma galeria de imagens compartilhada de uma VM existente no Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f7afc671dbb3d4ef8aa30222c4f235c9f7869d02
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556835"
---
# <a name="create-an-image-from-a-vm"></a>Criar uma imagem de uma VM

Se você tiver uma VM existente que deseja usar para criar várias VMs idênticas, poderá usar essa VM para criar uma imagem em uma galeria de imagens compartilhadas usando Azure PowerShell. Você também pode criar uma imagem de uma VM usando o [CLI do Azure](image-version-vm-cli.md).

Você pode capturar uma imagem de VMs [especializadas e generalizadas](./shared-image-galleries.md#generalized-and-specialized-images) usando Azure PowerShell. 

As imagens em uma galeria de imagens têm dois componentes, que serão criados neste exemplo:
- Uma **definição de imagem** contém informações sobre a imagem e os requisitos para usá-la. Isso inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é usada para criar uma VM ao usar uma galeria de imagens compartilhada. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você cria uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter uma galeria de imagens compartilhada existente e uma VM existente no Azure para usar como a origem. 

Se a VM tiver um disco de dados anexado, o tamanho do disco de dados não poderá ser maior que 1 TB.

Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.


## <a name="get-the-gallery"></a>Obter a Galeria

Você pode listar todas as galerias e definições de imagem por nome. Os resultados estão no formato `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Depois de encontrar a Galeria correta e as definições de imagem, crie variáveis para que elas sejam usadas mais tarde. Este exemplo obtém a galeria chamada *myGallery* no grupo de recursos *MyResource* Group.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Obter a VM

Você pode ver uma lista das VMss que estão disponíveis em um grupo de recursos usando [Get-AzVM](/powershell/module/az.compute/get-azvm). Depois de saber o nome da VM e em qual grupo de recursos ele está, você pode usar `Get-AzVM` novamente para obter o objeto da VM e armazená-lo em uma variável para uso posterior. Este exemplo obtém uma VM chamada *sourceVM* do grupo de recursos "MyResource Group" e a atribui à variável *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

É uma prática recomendada stop\deallocate a VM antes de criar uma imagem usando [Stop-AzVM](/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para as imagens. Eles são usados para gerenciar informações sobre a imagem. Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. 

Ao fazer a definição de imagem, verifique se o tem todas as informações corretas. Se você generaliza a VM (usando Sysprep para Windows ou waagent-deprovision para Linux), você deve criar uma definição de imagem usando `-OsState generalized` . Se você não tiver generalizado a VM, crie uma definição de imagem usando `-OsState specialized` .

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](./shared-image-galleries.md#image-definitions).

Crie a definição de imagem usando [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

Neste exemplo, a definição de imagem é chamada *myImageDefinition* e é para uma VM especializada que executa o Windows. Para criar uma definição para imagens usando o Linux, use `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a versão da imagem é *1.0.0* e ela é replicado para os datacenters *Centro-Oeste dos EUA* e *Centro-Sul dos EUA*. Ao escolher regiões de destino para replicação, lembre-se de que você também precisa incluir a região de *origem* como um destino para replicação.

Para criar uma versão da imagem da VM, use `$vm.Id.ToString()` para o `-SourceImageId`.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
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
> Você também pode armazenar sua imagem no armazenamento Premium adicionando `-StorageAccountType Premium_LRS` ou [armazenando o armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `-StorageAccountType Standard_ZRS` ao criar a versão da imagem.
>

## <a name="next-steps"></a>Próximas etapas

Depois de verificar se a nova versão da imagem está funcionando corretamente, você pode criar uma VM. Crie uma VM com base em uma [versão de imagem especializada](vm-specialized-image-version-powershell.md) ou em uma [versão de imagem generalizada](vm-generalized-image-version-powershell.md).

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).