---
title: Criar uma imagem personalizada do Azure DevTest Labs de um arquivo VHD | Microsoft Docs
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o Portal do Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cac812a9c38fc1dedfd31659a626b122f9527e63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87289408"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Criar uma imagem personalizada de um arquivo VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As seguintes etapas orientam você durante a criação de uma imagem personalizada de um arquivo VHD usando o Portal do Azure:

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

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

   - **Nome do plano:** insira o nome da imagem do Marketplace (SKU) a partir da qual essa imagem personalizada será criada 
   - **Oferta do plano:** insira o produto (oferta) da imagem do Marketplace a partir da qual essa imagem personalizada é criada 
   - **Oferta do plano:** insira o produto (oferta) da imagem do Marketplace a partir da qual essa imagem personalizada é criada

   > [!NOTE]
   > Se a imagem que você estiver utilizando para criar uma imagem personalizada **não** for uma imagem licenciada, esses campos estarão vazios e poderão ser preenchidos, caso você escolha. Se a imagem **for** uma imagem licenciada, os campos serão preenchidos automaticamente com as informações do plano. Se você tentar alterá-los nesse caso, uma mensagem de aviso será exibida.
   >
   >

1. Selecione **OK** para criar a imagem personalizada.

Depois de alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um usuário de laboratório quiser criar uma nova VM, a imagem estará disponível na lista de imagens básicas.

![Imagem personalizada disponível na lista de imagens básicas](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](./devtest-lab-faq.md#blog-post)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma VM ao laboratório](./devtest-lab-add-vm.md)
