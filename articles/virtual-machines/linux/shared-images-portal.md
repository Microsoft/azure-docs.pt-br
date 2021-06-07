---
title: Criar imagens de VM Linux do Azure compartilhadas usando o portal
description: Saiba como usar portal do Azure para criar e compartilhar imagens de máquinas virtuais do Linux.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 90ed91caa1c4b71ae90b86f5b0783a6d5c1c669e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552789"
---
# <a name="create-a-shared-image-gallery-using-the-portal"></a>Criar uma galeria de imagens compartilhada usando o portal

Uma [Galeria de Imagens Compartilhadas](../shared-image-galleries.md) simplifica o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para inicializar tarefas de implantação, como pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. 

A Galeria de imagens compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas em sua organização, dentro ou entre regiões, dentro de um locatário do Azure AD. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. Você pode criar várias galerias, de modo que pode agrupar logicamente as imagens compartilhadas. 

A galeria é um recurso de nível superior que fornece controle de acesso baseado em função do Azure (RBAC do Azure) completo. As imagens podem ser atualizadas, e você pode optar por replicar cada versão da imagem para um conjunto diferente de regiões do Azure. A galeria funciona apenas com imagens gerenciadas.

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos. Usaremos ou criaremos estes itens neste artigo:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>





## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, você deve ter uma imagem gerenciada existente de uma VM generalizada ou um instantâneo de uma VM especializada. Você pode seguir [o tutorial: criar uma imagem personalizada de uma VM do Azure com Azure PowerShell](tutorial-custom-images.md) para criar uma imagem gerenciada ou [criar um instantâneo](../windows/snapshot-copy-managed-disk.md) para uma VM especializada. Para imagens gerenciadas e instantâneos, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar com este artigo, substitua o grupo de recursos e os nomes de VM quando for necessário.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Criar VMs 

Agora você pode criar uma ou mais novas VMs. Este exemplo cria uma VM chamada *myVMfromImage* no *myResourceGroup* no datacenter do *Leste dos EUA*.

1. Vá para a definição de imagem. Você pode usar o filtro de recursos para mostrar todas as definições de imagem disponíveis.
1. Na página da definição de imagem, selecione **criar VM** no menu na parte superior da página.
1. Para **grupo de recursos**, selecione **criar novo** e digite grupo de *recursos* para o nome.
1. Em **nome da máquina virtual**, digite *myVM*.
1. Em **Região**, selecione *Leste dos EUA*.
1. Para **as opções de disponibilidade**, deixe o padrão de *nenhuma redundância de infraestrutura necessária*.
1. O valor da **imagem** será preenchido automaticamente com a `latest` versão da imagem se você tiver iniciado na página para a definição da imagem.
1. Para **tamanho**, escolha um tamanho de VM na lista de tamanhos disponíveis e escolha **selecionar**.
1. Em **conta de administrador**, se a VM de origem tiver sido generalizada, insira seu **nome de usuário** e a **chave pública SSH**. Se a VM de origem era especializada, essas opções serão esmaecidas porque as informações da VM de origem são usadas.
1. Se você quiser permitir o acesso remoto à VM, em **portas de entrada públicas**, escolha **permitir portas selecionadas** e, em seguida, selecione **SSH (22)** na lista suspensa. Se você não quiser permitir o acesso remoto à VM, deixe **nenhuma** selecionada para **portas de entrada públicas**.
1. Quando tiver terminado, selecione o botão **revisar + criar** na parte inferior da página.
1. Depois que a VM passar na validação, selecione **criar** na parte inferior da página para iniciar a implantação.


## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione **Excluir**, em seguida, confirme o nome do grupo de recursos para excluir.

Se você quiser excluir recursos individuais, será necessário excluí-los na ordem inversa. Por exemplo, para excluir uma definição de imagem, você precisa excluir todas as versões de imagem criadas por meio dessa imagem.

## <a name="next-steps"></a>Próximas etapas

Você também pode criar um recurso de Galeria de imagens compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma Versão da Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para obter mais informações sobre galerias de imagens compartilhadas, confira a [Visão geral](../shared-image-galleries.md). Se você enfrentar problemas, confira [Solução de problemas de galerias de imagens compartilhadas](../troubleshooting-shared-images.md).