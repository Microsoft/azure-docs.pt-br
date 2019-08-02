---
title: Tutorial - Criar imagens personalizadas da VM com a CLI do Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar a CLI do Azure para criar uma imagem de máquina virtual personalizada no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 080d1d68c2f2fb757bb51471b91a807b2f00fa2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709516"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com a CLI do Azure

Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. Neste tutorial, você criará sua própria imagem personalizada de uma máquina virtual do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Desprovisionar e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como pegar uma VM existente e transformá-la em uma imagem personalizada reutilizável que você pode usar para criar novas instâncias de VM.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos e os nomes de VM onde for necessário.

## <a name="create-a-custom-image"></a>Criar uma imagem personalizada

Para criar uma imagem de uma máquina virtual, você precisará preparar a VM desprovisionando, desalocando e, em seguida, marcando a VM de origem como generalizada. Depois que a VM tiver sido preparada, você poderá criar uma imagem.

### <a name="deprovision-the-vm"></a>Desprovisionar a VM 

Desprovisionar generaliza a VM removendo informações específicas da máquina. Essa generalização permite implantar várias VMs de uma única imagem. Durante o desprovisionamento, o nome do host é redefinido como *localhost.localdomain*. As chaves de host de SSH, as configurações de nameserver, a senha raiz e as concessões de DHCP em cache também são excluídas.

Para desprovisionar a máquina virtual, use o agente de VM do Azure (waagent). O agente de VM do Azure está instalado na VM e gerencia o provisionamento e a interação com o Azure Fabric Controller. Para saber mais, confira o [Guia do usuário do agente Linux para o Azure](../extensions/agent-linux.md).

Conectar-se à sua VM usando o SSH e executar o comando para desprovisionar a VM. Com o argumento `+user`, a última conta de usuário provisionada e os dados associados também são excluídos. Substitua o endereço IP de exemplo pelo endereço IP público de sua VM.

SSH para a VM.
```bash
ssh azureuser@52.174.34.95
```
Desprovisione a VM.

```bash
sudo waagent -deprovision+user -force
```
Feche a sessão do SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desalocar e marcar a VM como generalizada

Para criar uma imagem, a VM precisará ser desalocada. Desaloque a VM usando [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Por fim, defina o estado da VM como generalizado com [az vm generalize](/cli//azure/vm), de maneira que a plataforma do Azure saiba que a VM foi generalizada. Você só pode criar uma imagem por meio de uma VM generalizada.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Criar a imagem

Agora você pode criar uma imagem da VM usando [az image create](/cli//azure/image). O exemplo a seguir cria uma imagem chamada *myImage* por meio de uma VM chamada *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Criar VMs por meio da imagem

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem usando [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM chamada *myVMfromImage* por meio da imagem chamada *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Gerenciamento de imagens 

Estes são alguns exemplos de tarefas comuns de gerenciamento de imagem e como para concluí-las usando a CLI do Azure.

Liste todas as imagens por nome em um formato de tabela.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Exclua uma imagem. Este exemplo exclui a imagem denominada *myOldImage* do *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM personalizada. Você aprendeu como:

> [!div class="checklist"]
> * Desprovisionar e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

Avance para o próximo tutorial para saber mais sobre máquinas virtuais de alta disponibilidade.

> [!div class="nextstepaction"]
> [Criar VMs altamente disponíveis](tutorial-availability-sets.md).

