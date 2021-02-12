---
title: PowerShell-criar uma imagem de um instantâneo ou de um disco gerenciado em uma galeria de imagens compartilhada
description: Saiba como criar uma imagem de um instantâneo ou de um disco gerenciado em uma galeria de imagens compartilhada usando o PowerShell.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f4ca28efce28933eed9be5cca7bd412f2d9505aa
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679527"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Criar uma imagem de um disco gerenciado ou instantâneo em uma galeria de imagens compartilhada usando o PowerShell

Se você tiver um instantâneo ou disco gerenciado existente que deseja migrar para uma galeria de imagens compartilhada, poderá criar uma imagem da Galeria de imagens compartilhada diretamente do disco gerenciado ou do instantâneo. Depois de testar a nova imagem, você pode excluir o disco gerenciado de origem ou o instantâneo. Você também pode criar uma imagem de um disco gerenciado ou instantâneo em uma galeria de imagens compartilhada usando o [CLI do Azure](image-version-snapshot-cli.md).

As imagens em uma galeria de imagens têm dois componentes, que serão criados neste exemplo:
- Uma **definição de imagem** contém informações sobre a imagem e os requisitos para usá-la. Isso inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é usada para criar uma VM ao usar uma galeria de imagens compartilhada. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você cria uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter um instantâneo ou um disco gerenciado. 

Se você quiser incluir um disco de dados, o tamanho do disco de dados não poderá ser superior a 1 TB.

Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.


## <a name="get-the-snapshot-or-managed-disk"></a>Obter o instantâneo ou o disco gerenciado

Você pode ver uma lista de instantâneos que estão disponíveis em um grupo de recursos usando [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot). 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Depois de saber o nome do instantâneo e em qual grupo de recursos ele está, você pode usar `Get-AzSnapshot` novamente para obter o objeto de instantâneo e armazená-lo em uma variável para uso posterior. Este exemplo obtém um instantâneo chamado *mysnapshot* do grupo de recursos "MyResource Group" e o atribui à variável *$Source*. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

Você também pode usar um disco gerenciado em vez de um instantâneo. Para obter um disco gerenciado, use [Get-AzDisk](/powershell/module/az.compute/get-azdisk). 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Em seguida, obtenha o disco gerenciado e atribua-o à `$source` variável.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Você pode usar os mesmos cmdlets para obter os discos de dados que deseja incluir na imagem. Atribua-os às variáveis e, em seguida, use essas variáveis posteriormente ao criar a versão da imagem.


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

Ao fazer a definição de imagem, verifique se o tem todas as informações corretas. Neste exemplo, supomos que o instantâneo ou o disco gerenciado sejam de uma VM em uso e não tenha sido generalizado. Se o disco gerenciado ou o instantâneo foi tirado de um sistema operacional generalizado (depois de executar o Sysprep para Windows ou [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou `-deprovision+user` Linux), altere o `-OsState` para `generalized` . 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](./shared-image-galleries.md#image-definitions).

Crie a definição de imagem usando [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a definição de imagem é chamada *myImageDefinition* e é para um sistema operacional Windows especializado. Para criar uma definição para imagens usando um sistema operacional Linux, use `-OsType Linux` . 

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

### <a name="purchase-plan-information"></a>Informações do plano de compra

Em alguns casos, você precisa passar informações do plano de compra no ao criar uma VM de uma imagem baseada em uma imagem do Azure Marketplace. Nesses casos, recomendamos que você inclua as informações do plano de compra na definição da imagem. Nesse caso, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir do instantâneo usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Se você quiser que sua imagem contenha um disco de dados, além do disco do sistema operacional, adicione o `-DataDiskImage` parâmetro e defina-o como a ID do instantâneo do disco de dados ou do disco gerenciado.

Neste exemplo, a versão da imagem é *1.0.0* e ela é replicado para os datacenters *Centro-Oeste dos EUA* e *Centro-Sul dos EUA*. Ao escolher regiões de destino para replicação, lembre-se de que você também precisa incluir a região de *origem* como um destino para replicação.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Pode levar um tempo para replicar a imagem para todas as regiões de destino, portanto, criamos um trabalho para podermos acompanhar o progresso. Para ver o andamento do trabalho, digite `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Você precisa aguardar que a versão da imagem termine completamente de ser compilada e replicada antes de poder usar o mesmo instantâneo para criar outra versão de imagem. 
>
> Você também pode armazenar a versão da imagem no [armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `-StorageAccountType Standard_ZRS` ao criar a versão da imagem.
>

## <a name="delete-the-source"></a>Excluir a origem

Depois de verificar se a nova versão da imagem está funcionando corretamente, você pode excluir a origem da imagem com [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) ou [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk).


## <a name="next-steps"></a>Próximas etapas

Depois de verificar que a replicação foi concluída, você pode criar uma VM com base na [imagem especializada](vm-specialized-image-version-powershell.md).

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).