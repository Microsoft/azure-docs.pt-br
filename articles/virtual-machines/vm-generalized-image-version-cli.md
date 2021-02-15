---
title: Criar uma VM com base em uma imagem generalizada usando o CLI do Azure
description: Crie uma VM com base em uma versão de imagem generalizada usando o CLI do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: ec589848625e1114dedd8c58b41f7ecbc991f311
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881967"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Criar uma VM com base em uma versão de imagem generalizada usando a CLI

Crie uma VM com base em uma [versão de imagem generalizada](./shared-image-galleries.md#generalized-and-specialized-images) armazenada em uma galeria de imagens compartilhada. Se você quiser criar uma VM usando uma imagem especializada, consulte [criar uma VM com base em uma imagem especializada](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Obter a ID da imagem

Liste as definições de imagem em uma galeria usando [AZ SIG Image-Definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) para ver o nome e a ID das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Criar a VM

Crie uma VM usando [az vm create](/cli/azure/vm#az-vm-create). Para usar a versão mais recente da imagem, defina `--image` como a ID da definição da imagem. 

Substitua os nomes dos recursos conforme necessário no exemplo. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Você também pode usar uma versão específica usando a ID da versão da imagem para o `--image` parâmetro. Por exemplo, para usar a versão de imagem *1.0.0* Type: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Próximas etapas

O [Construtor de imagens do Azure (visualização)](./image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](./linux/image-builder-gallery-update-image-version.md).