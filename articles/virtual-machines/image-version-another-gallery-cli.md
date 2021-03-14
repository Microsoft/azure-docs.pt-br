---
title: Copiar uma versão de imagem de outra galeria usando a CLI
description: Copie uma versão de imagem de outra galeria com a CLI do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 0bea4fbac062b498dabe04e6e58d530d09b16d6d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553095"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Copiar uma imagem de outra galeria usando o CLI do Azure

Se você tiver várias galerias em sua organização, também poderá criar versões de imagem de versões de imagem existentes armazenadas em outras galerias. Por exemplo, você pode ter uma galeria de desenvolvimento e teste para criar e testar novas imagens. Quando eles estiverem prontos para serem usados na produção, você poderá copiá-los para uma galeria de produção usando este exemplo. Você também pode criar uma imagem de uma imagem em outra galeria usando [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter uma galeria de origem, uma definição de imagem e uma versão de imagem existentes. Você também deve ter uma galeria de destino. 

A versão da imagem de origem deve ser replicada para a região onde a Galeria de destino está localizada. 

Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.



## <a name="get-information-from-the-source-gallery"></a>Obter informações da Galeria de origem

Você precisará de informações da definição da imagem de origem para poder criar uma cópia dela em sua nova galeria.

Liste as informações sobre as galerias de imagens disponíveis usando [AZ SIG List](/cli/azure/sig#az-sig-list) para encontrar informações sobre a Galeria de origem.

```azurecli-interactive 
az sig list -o table
```

Liste as definições de imagem em uma galeria usando [AZ SIG Image-Definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list). Neste exemplo, estamos procurando definições de imagem na galeria chamada *myGallery* no grupo de recursos *myGalleryRG* .

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Liste as versões de uma imagem em uma galeria, usando [AZ SIG Image-Version List](/cli/azure/sig/image-version#az-sig-image-version-list) para localizar a versão da imagem que você deseja copiar em sua nova galeria. Neste exemplo, estamos procurando todas as versões de imagem que fazem parte da definição de imagem *myImageDefinition* .

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Depois de ter todas as informações necessárias, você pode obter a ID da versão da imagem de origem usando [AZ SIG Image-Version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Criar a definição de imagem 

Você precisa criar uma definição de imagem que corresponda à definição de imagem da sua versão de imagem de origem. Você pode ver todas as informações necessárias para recriar a definição de imagem em sua nova galeria usando [AZ SIG Image-Definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
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
  "osType": "Linux",
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

Crie uma nova definição de imagem, em sua nova galeria, usando as informações da saída acima.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Criar a versão da imagem

Crie versões usando [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). Você precisará passar a ID da imagem gerenciada para usar como uma linha de base para criar a versão da imagem. Você pode usar [az image list](/cli/azure/image?view#az-image-list) para obter informações sobre as imagens que estão em um grupo de recursos. 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a versão da nossa imagem é a *1.0.0* e vamos criar uma réplica na região *do Sul EUA Central* e uma réplica na região *leste dos EUA* usando o armazenamento com redundância de zona.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Você precisa esperar que a versão da imagem seja compilada e replicada completamente antes de poder usar a mesma imagem gerenciada para criar outra versão da imagem.
>
> Você também pode armazenar sua imagem no armazenamento Premium adicionando `--storage-account-type  premium_lrs` ou [armazenando o armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `--storage-account-type  standard_zrs` ao criar a versão da imagem.
>

## <a name="next-steps"></a>Próximas etapas

Crie uma VM com base em uma versão de imagem [generalizada](vm-generalized-image-version-cli.md) ou [especializada](vm-specialized-image-version-cli.md) .

Além disso, experimente o [Construtor de imagens do Azure (versão prévia)](./image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](./linux/image-builder-gallery-update-image-version.md). 

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).