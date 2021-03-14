---
title: Criar uma imagem de uma VM usando CLI do Azure
description: Saiba como criar uma imagem em uma galeria de imagens compartilhadas de uma VM no Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c35be8821b6763531b43ec85b10325e91f8bc5f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556853"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Criar uma versão de imagem de uma VM no Azure usando o CLI do Azure

Se você tiver uma VM existente que deseja usar para criar várias VMs idênticas, poderá usar essa VM para criar uma imagem em uma galeria de imagens compartilhadas usando o CLI do Azure. Você também pode criar uma imagem de uma VM usando [Azure PowerShell](image-version-vm-powershell.md).

Uma **versão de imagem** é o que você usa para criar uma VM ao usar uma galeria de imagens compartilhada. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você usa uma versão de imagem para criar uma VM, a versão da imagem é usada para criar discos para a nova VM. Versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter uma galeria de imagens compartilhada existente. 

Você também deve ter uma VM existente no Azure, na mesma região que a galeria. 

Se a VM tiver um disco de dados anexado, o tamanho do disco de dados não poderá ser maior que 1 TB.

Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.

## <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

Você pode ver uma lista das VMs disponíveis usando [az vm list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Quando souber o nome da VM e em qual grupo de recursos ela está, obtenha a ID da VM usando [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para as imagens. Elas são usadas para gerenciar informações sobre as versões da imagem que são criadas dentro delas. 

Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. 

Verifique se a definição da imagem é do tipo correto. Se tiver generalizado a VM (usando o Sysprep para Windows ou waagent -deprovision para Linux), você precisará criar uma definição de imagem generalizada usando `--os-state generalized`. Se quiser usar a VM sem remover contas de usuário existentes, crie uma definição de imagem especializada usando `--os-state specialized`.

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](./shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição da imagem se chama *myImageDefinition* e é referente a uma imagem [especializada](./shared-image-galleries.md#generalized-and-specialized-images) do SO Linux. Para criar uma definição para imagens usando um SO Windows, use `--os-type Windows`. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Criar a versão da imagem

Crie uma versão da imagem com base na VM usando [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a versão de nossa imagem é *1.0.0* e criaremos duas réplicas na região do *Centro-Oeste dos EUA*, uma na região *Centro-Sul dos EUA* e uma na região *Leste dos EUA 2* usando o armazenamento com redundância de zona. As regiões de replicação precisam incluir a região em que a VM de origem fica localizada.

Substitua o valor de `--managed-image` neste exemplo pela ID da VM da etapa anterior.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Você precisa esperar que a versão da imagem seja compilada e replicada completamente antes de poder usar a mesma imagem gerenciada para criar outra versão da imagem.
>
> Você também pode armazenar sua imagem no armazenamento Premium adicionando `--storage-account-type  premium_lrs` ou [armazenando o armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `--storage-account-type  standard_zrs` ao criar a versão da imagem.
>

## <a name="next-steps"></a>Próximas etapas

Crie uma VM a partir da [imagem generalizada](vm-generalized-image-version-cli.md) usando o CLI do Azure.

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).