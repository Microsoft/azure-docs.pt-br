---
title: CLI – criar uma imagem de um instantâneo ou de um disco gerenciado em uma galeria de imagens compartilhada
description: Saiba como criar uma imagem de um instantâneo ou de um disco gerenciado em uma galeria de imagens compartilhada usando o CLI do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c809edd3699d0b9827fe15da53d5d18b12cbe6e6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556954"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Criar uma imagem de um disco gerenciado ou instantâneo em uma galeria de imagens compartilhada usando o CLI do Azure

Se você tiver um instantâneo ou disco gerenciado existente que deseja migrar para uma galeria de imagens compartilhada, poderá criar uma imagem da Galeria de imagens compartilhada diretamente do disco gerenciado ou do instantâneo. Depois de testar a nova imagem, você pode excluir o disco gerenciado de origem ou o instantâneo. Você também pode criar uma imagem de um disco gerenciado ou instantâneo em uma galeria de imagens compartilhada usando o [Azure PowerShell](image-version-snapshot-powershell.md).

As imagens em uma galeria de imagens têm dois componentes, que serão criados neste exemplo:
- Uma **definição de imagem** contém informações sobre a imagem e os requisitos para usá-la. Isso inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é usada para criar uma VM ao usar uma galeria de imagens compartilhada. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você cria uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter um instantâneo ou um disco gerenciado. 

Se você quiser incluir um disco de dados, o tamanho do disco de dados não poderá ser superior a 1 TB.

Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.

## <a name="find-the-snapshot-or-managed-disk"></a>Localizar o instantâneo ou o disco gerenciado 

Você pode ver uma lista de instantâneos que estão disponíveis em um grupo de recursos usando [AZ snapshot List](/cli/azure/snapshot#az-snapshot-list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Você também pode usar um disco gerenciado em vez de um instantâneo. Para obter um disco gerenciado, use [AZ Disk List](/cli/azure/disk#az-disk-list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Depois de ter a ID do instantâneo ou do disco gerenciado e atribuí-lo a uma variável chamada `$source` a ser usada posteriormente.

Você pode usar o mesmo processo para obter os discos de dados que deseja incluir na imagem. Atribua-os às variáveis e, em seguida, use essas variáveis posteriormente ao criar a versão da imagem.


## <a name="find-the-gallery"></a>Localizar a Galeria

Você precisará de informações sobre a Galeria de imagens para criar a definição de imagem.

Lista informações sobre as galerias de imagem disponíveis usando [AZ SIG List](/cli/azure/sig#az-sig-list). Observe o nome da galeria a qual grupo de recursos a galeria está para usar mais tarde.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para as imagens. Eles são usados para gerenciar informações sobre a imagem. Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. 

Ao fazer a definição de imagem, verifique se o tem todas as informações corretas. Neste exemplo, supomos que o instantâneo ou o disco gerenciado sejam de uma VM em uso e não tenha sido generalizado. Se o disco gerenciado ou o instantâneo foi tirado de um sistema operacional generalizado (depois de executar o Sysprep para Windows ou [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou `-deprovision+user` Linux), altere o `-OsState` para `generalized` . 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](./shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição da imagem se chama *myImageDefinition* e é referente a uma imagem [especializada](./shared-image-galleries.md#generalized-and-specialized-images) do SO Linux. Para criar uma definição para imagens usando um SO Windows, use `--os-type Windows`. 

Neste exemplo, a galeria é denominada *myGallery*, está no grupo de recursos *myGalleryRG* e o nome da definição de imagem será *mImageDefinition*.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Criar a versão da imagem

Crie uma versão de imagem usando [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a versão da nossa imagem é a *1.0.0* e vamos criar uma réplica na região *do Sul EUA Central* e uma réplica na região *leste dos EUA 2* usando o armazenamento com redundância de zona. Ao escolher regiões de destino para replicação, lembre-se de que você também precisa incluir a região de *origem* do disco gerenciado ou instantâneo como um destino para replicação.

Passe a ID do instantâneo ou do disco gerenciado no `--os-snapshot` parâmetro.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Se desejar incluir discos de dados na imagem, você precisará incluir o `--data-snapshot-luns` parâmetro definido como o número de LUN e o `--data-snapshots` conjunto como a ID do disco de dados ou instantâneo.

> [!NOTE]
> Você precisa esperar que a versão da imagem seja compilada e replicada completamente antes de poder usar a mesma imagem gerenciada para criar outra versão da imagem.
>
> Você também pode armazenar todas as réplicas de versão da imagem no [armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `--storage-account-type standard_zrs` ao criar a versão da imagem.
>

## <a name="next-steps"></a>Próximas etapas

Crie uma VM com base em uma [versão de imagem especializada](vm-specialized-image-version-cli.md).

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).