---
title: Tutorial – Criar imagens de VM personalizadas com a CLI do Azure
description: Neste tutorial, você aprende a usar a CLI do Azure para criar uma imagem de máquina virtual personalizada no Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 81bbf0b49919db68407a85b9ea2f731c5f8e1d91
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769869"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com a CLI do Azure

Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. Neste tutorial, você criará sua própria imagem personalizada de uma máquina virtual do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar uma VM de uma imagem 
> * Compartilhar uma galeria de imagens


Este tutorial usa a CLI dentro do [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizada para a versão mais recente. Para abrir o Cloud Shell, selecione **Experimentar** na parte superior de um bloco de código qualquer.

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que execute a CLI do Azure versão 2.4.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Visão geral

Uma [Galeria de Imagens Compartilhadas](../shared-image-galleries.md) simplifica o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. 

A Galeria de Imagens Compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. 

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como pegar uma VM existente e transformá-la em uma imagem personalizada reutilizável que você pode usar para criar novas instâncias de VM.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, confira o [Início rápido da CLI](quick-create-cli.md) para criar uma VM a ser usada neste tutorial. Ao trabalhar no tutorial, substitua os nomes dos recursos se necessário.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. 

Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Criar uma galeria de imagens usando [az sig create](/cli/azure/sig#az_sig_create). O exemplo a seguir cria um grupo de recursos da galeria chamado *myGalleryRG* no *Leste dos EUA*, bem como uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

Você pode ver uma lista das VMs disponíveis usando [az vm list](/cli/azure/vm#az_vm_list). 

```azurecli-interactive
az vm list --output table
```

Quando souber o nome da VM e em qual grupo de recursos ela está, obtenha a ID da VM usando [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Copie a ID da VM para usar mais tarde.

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para as imagens. Elas são usadas para gerenciar informações sobre as versões da imagem que são criadas dentro delas. 

Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](../shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria usando [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create). 

Neste exemplo, a definição da imagem se chama *myImageDefinition* e é referente a uma imagem [especializada](../shared-image-galleries.md#generalized-and-specialized-images) do SO Linux. 

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

Copie a ID da definição da imagem da saída para usar mais tarde.

## <a name="create-the-image-version"></a>Criar a versão da imagem

Crie uma versão da imagem com base na VM usando [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create).  

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
> Você também pode armazenar a imagem no armazenamento Premium adicionando `--storage-account-type  premium_lrs`, ou no [Armazenamento com redundância de zona](../../storage/common/storage-redundancy.md) adicionando `--storage-account-type  standard_zrs` ao criar a versão da imagem.
>

 
## <a name="create-the-vm"></a>Criar a VM

Crie a VM usando [az vm create](/cli/azure/vm#az_vm_create) e o parâmetro --specialized para indicar que se trata de uma imagem especializada. 

Use a ID de definição de imagem de `--image` para criar a VM com base na versão mais recente da imagem que está disponível. Você também pode criar a VM com base em uma versão específica fornecendo a ID de versão da imagem de `--image`. 

Neste exemplo, estamos criando uma VM com base na versão mais recente da imagem *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Compartilhar a galeria

Você pode compartilhar imagens entre assinaturas usando o Azure RBAC (controle de acesso baseado em função do Azure). Você pode compartilhar imagens no nível da galeria, da definição da imagem e da versão da imagem. Qualquer usuário que tenha permissões de leitura para uma versão de imagem, mesmo entre assinaturas, poderá implantar uma VM usando a versão da imagem.

Recomendamos que você compartilhe com outros usuários no nível da galeria. Para obter a ID do objeto da galeria, use [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use a ID do objeto como um escopo, juntamente com um endereço de email e [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para conceder a um usuário acesso à galeria de imagens compartilhadas. Substitua `<email-address>` e `<gallery iD>` pelas suas informações.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações sobre como compartilhar recursos usando o Azure RBAC, confira [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Construtor de Imagens do Azure

O Azure também oferece um serviço integrado ao Packer, o [Construtor de Imagens de VM do Azure](../image-builder-overview.md). Basta descrever suas personalizações em um modelo e ele cuidará da criação da imagem. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM personalizada. Você aprendeu a:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar uma VM de uma imagem 
> * Compartilhar uma galeria de imagens

Avance para o próximo tutorial para saber mais sobre máquinas virtuais de alta disponibilidade.

> [!div class="nextstepaction"]
> [Criar VMs altamente disponíveis](tutorial-availability-sets.md)