---
title: Criar uma imagem personalizada do Azure DevTest Labs de uma VM | Microsoft Docs
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs de uma VM provisionada usando o Portal do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868404"
---
# <a name="create-a-custom-image-from-a-vm"></a>Criar uma imagem personalizada de uma VM

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Você pode criar uma imagem personalizada de uma VM provisionada e depois usá-la para criar VMs idênticas. As etapas a seguir ilustram como criar uma imagem personalizada com base em uma VM:

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. No painel do laboratório, selecione **Minhas máquinas virtuais**.
 
1. No painel **Minhas máquinas virtuais**, selecione a VM com base na qual você deseja criar a imagem personalizada.

1. No painel da VM, selecione **Criar imagem personalizada (VHD)**.

    ![Criar item de menu de imagem personalizada](./media/devtest-lab-create-template/create-custom-image.png)

1. No painel **Imagem personalizada**, digite um nome e uma descrição para a sua imagem personalizada. Essas informações são exibidas na lista de bases quando você cria uma VM. A imagem personalizada incluirá o disco do sistema operacional e todos os discos de dados anexados à máquina virtual.

    ![Painel Criar imagem personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selecione se o sysprep foi executado na VM. Se o sysprep não foi executado na VM, especifique se você quer que o sysprep seja executado na VM quando a imagem personalizada for criada.

1. Selecione **OK** quando tiver terminado de criar a imagem personalizada.

Depois de alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um usuário de laboratório quiser criar uma nova VM, a imagem estará disponível na lista de imagens básicas.

![Imagem personalizada disponível na lista de imagens básicas](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)
