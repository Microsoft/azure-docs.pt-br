---
title: Criar um conjunto de dimensionamento de uma versão de imagem especializada usando o CLI do Azure
description: Crie um conjunto de dimensionamento usando uma versão de imagem especializada em uma galeria de imagens compartilhada usando o CLI do Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ecc2bfe6bdc06ede61e6c4d1e6eccfc9ef6323a
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98877995"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Criar um conjunto de dimensionamento usando uma versão de imagem especializada com o CLI do Azure

Crie um conjunto de dimensionamento de uma [versão de imagem especializada](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) armazenada em uma galeria de imagens compartilhada. Se você quiser criar um conjunto de dimensionamento usando uma versão de imagem generalizada, consulte [criar um conjunto de dimensionamento de uma imagem generalizada](instance-generalized-image-version-cli.md).

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que execute a CLI do Azure versão 2.4.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Substitua os nomes dos recursos conforme necessário no exemplo. 

Liste as definições de imagem em uma galeria usando [AZ SIG Image-Definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) para ver o nome e a ID das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Crie um conjunto de dimensionamento usando [`az vmss create`](/cli/azure/vmss#az-vmss-create) o `--specialized` parâmetro para indicar que a imagem é uma imagem especializada.

Use a ID de definição de imagem de `--image` para criar as instâncias do conjunto de dimensionamento com base na versão mais recente da imagem que está disponível. Você também pode criar as instâncias do conjunto de dimensionamento com base em uma versão específica fornecendo a ID de versão da imagem de `--image`. Lembre-se de que o uso de uma versão de imagem específica significa que a automação poderá falhar se essa versão de imagem específica não estiver disponível porque foi excluída ou removida da região. É recomendável usar a ID de definição de imagem para criar sua nova VM, a menos que uma versão de imagem específica seja necessária.

Neste exemplo, estamos criando instâncias da versão mais recente da imagem *myImageDefinition* .

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>Próximas etapas
O [Construtor de imagens do Azure (visualização)](../virtual-machines/image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Você também pode criar um recurso de Galeria de imagens compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)