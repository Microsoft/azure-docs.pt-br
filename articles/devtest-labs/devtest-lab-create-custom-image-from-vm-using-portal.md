---
title: Criar uma imagem personalizada do Azure DevTest Labs de uma VM | Microsoft Docs
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs de uma VM provisionada usando o Portal do Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad45ed6eb7f97e14ec0ca0bb89efb2967c90fc16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87277020"
---
# <a name="create-a-custom-image-from-a-vm"></a>Criar uma imagem personalizada de uma VM

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Você pode criar uma imagem personalizada de uma VM provisionada e depois usá-la para criar VMs idênticas. As etapas a seguir ilustram como criar uma imagem personalizada com base em uma VM:

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. No painel do laboratório, selecione **Minhas máquinas virtuais**.
 
1. No painel **Minhas máquinas virtuais**, selecione a VM com base na qual você deseja criar a imagem personalizada.

1. No painel Gerenciamento da VM, selecione **criar imagem personalizada** em **operações**.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Criar item de menu de imagem personalizada":::
1. No painel **Imagem personalizada**, digite um nome e uma descrição para a sua imagem personalizada. Essas informações são exibidas na lista de bases quando você cria uma VM. A imagem personalizada incluirá o disco do sistema operacional e todos os discos de dados anexados à máquina virtual.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Criar item de menu de imagem personalizada":::
1. Selecione se o sysprep foi executado na VM. Se o sysprep não foi executado na VM, especifique se você quer que o sysprep seja executado na VM quando a imagem personalizada for criada.
1. Selecione **OK** quando tiver terminado de criar a imagem personalizada.

    Depois de alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um usuário de laboratório quiser criar uma nova VM, a imagem estará disponível na lista de imagens básicas.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="Criar item de menu de imagem personalizada":::

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](./devtest-lab-faq.md#blog-post)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)
