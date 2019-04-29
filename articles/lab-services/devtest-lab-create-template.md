---
title: Criar uma imagem personalizada do Azure DevTest Labs de um arquivo VHD | Microsoft Docs
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o Portal do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61295916"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Criar uma imagem personalizada de um arquivo VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As seguintes etapas orientam você durante a criação de uma imagem personalizada de um arquivo VHD usando o Portal do Azure:

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. No painel principal do laboratório, selecione **Configuração e políticas**. 

1. No painel **Configuração e políticas**, selecione **Imagens personalizadas**.

1. No painel **Imagens personalizadas**, selecione **+Adicionar**.

    ![Imagem de Adicionar Personalizado](./media/devtest-lab-create-template/add-custom-image.png)

1. Insira o nome da imagem personalizada. Esse nome é exibido na lista de imagens base durante a criação de uma VM.

1. Insira a descrição da imagem personalizada. Essa descrição é exibida na lista de imagens base durante a criação de uma VM.

1. Para **Tipo de sistema operacional**, selecione **Windows** ou **Linux**.

    - Se selecionar **Windows**, especifique por meio da caixa de seleção se o *sysprep* foi executado no computador. 
    - Se selecionar **Linux**, especifique por meio da caixa de seleção se o *deprovision* foi executado no computador. 

1. Selecione um **VHD** no menu suspenso. Este é o VHD que será usado para criar a nova imagem personalizada. Se necessário, selecione para **Carregar um VHD usando PowerShell**.

1. Você também poderá inserir um nome do plano, oferta do plano e um editor do plano, se a imagem utilizada para criar a imagem personalizada não for uma imagem licenciada (publicada pela Microsoft).

   - **Nome do plano:** Digite o nome da imagem do Marketplace (SKU) do qual essa imagem personalizada é criada 
   - **Plano de oferta:** Insira o produto (oferta) da imagem do Marketplace da qual essa imagem personalizada é criada. 
   - **Editor do plano:** Insira o editor da imagem do Marketplace da qual essa imagem personalizada é criada.

   > [!NOTE]
   > Se a imagem que você estiver utilizando para criar uma imagem personalizada **não** for uma imagem licenciada, esses campos estarão vazios e poderão ser preenchidos, caso você escolha. Se a imagem **for** uma imagem licenciada, os campos serão preenchidos automaticamente com as informações do plano. Se você tentar alterá-los nesse caso, uma mensagem de aviso será exibida.
   >
   >

1. Selecione **OK** para criar a imagem personalizada.

Depois de alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um usuário de laboratório quiser criar uma nova VM, a imagem estará disponível na lista de imagens básicas.

![Imagem personalizada disponível na lista de imagens básicas](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma VM ao laboratório](./devtest-lab-add-vm.md)
