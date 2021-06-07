---
title: Habilitar uma imagem licenciada em seu laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como habilitar uma imagem licenciada no Azure DevTest Labs usando o Portal do Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e2b6e495be2257548782143517f5a970b424860c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91276349"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Habilitar uma imagem licenciada em seu laboratório no Azure DevTest Labs

No Azure DevTest Labs, uma imagem licenciada inclui os termos e condições – normalmente de terceiros – que devem ser aceitos antes que a imagem possa ser acessada pelos usuários no laboratório. As seções a seguir descrevem como trabalhar com imagens licenciadas a fim de disponibilizá-las para uso na criação de máquinas virtuais.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Determinar se uma imagem licenciada está disponível para os usuários
A primeira etapa para permitir que os usuários criem VMs a partir de uma imagem licenciada é certificar-se de que os termos e condições da imagem licenciada foram aceitos. As etapas a seguir mostram como você pode exibir o status de oferta de uma imagem licenciada e, se necessário, aceitar os termos e condições.

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços** e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. No painel esquerdo, em **CONFIGURAÇÃO**, selecione **Configurações e políticas**.

1. No painel esquerdo, em **BASES DA MÁQUINA VIRTUAL**, selecione **Imagens do Marketplace**. 

    ![Item de menu das imagens do Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Uma lista com todas as imagens do marketplace disponíveis é exibida, incluindo o **STATUS DA OFERTA** de cada imagem.

    ![Lista de imagens do marketplace mostrando o status de oferta de cada imagem](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Uma imagem licenciada mostra um status de oferta de 
    
    - **Termos aceitos:** a imagem licenciada está disponível aos usuários para criação de VMs. 
    - **Revisão necessária dos termos:** a imagem licenciada não está disponível atualmente para os usuários. Os termos e condições da licença devem ser aceitos antes que os usuários do laboratório possam usá-la para criar VMs. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Disponibilizar uma imagem licenciada a usuários de laboratório
Para certificar-se de que uma imagem licenciada esteja disponível para usuários de laboratório, um proprietário de laboratório com permissões de administrador deve aceitar primeiro os termos e condições dessa imagem licenciada. A habilitação da implantação programática para a assinatura associada a uma imagem licenciada aceita automaticamente os termos legais e declarações de privacidade dessa imagem. [Trabalhar com imagens do Marketplace no Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) fornece informações adicionais sobre a implantação programática de imagens do marketplace.

Habilite a implantação programática para uma imagem licenciada executando estas etapas:

1. No [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), vá para a lista de **imagens Marketplace**.

1. Identifique uma imagem licenciada à qual você deseja que os usuários tenham acesso, mas cujos termos não foram aceitos. Por exemplo, você pode ver uma máquina virtual de ciência de dados que mostra um status de **Termos aceitos** ou **Revisão necessária dos termos**.

    ![Captura de tela que mostra Máquina Virtual de Ciência de Dados imagens com o status de oferta "de" termos aceitos "e" análise de termos necessário "](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > As VMs de ciência de dados são imagens de Máquinas Virtuais do Azure pré-instaladas, configuradas e testadas com várias ferramentas populares normalmente usadas para análise de dados, aprendizado de máquina e treinamento de inteligência artificial. [Introdução à Máquina Virtual de Ciência de Dados do Azure para Linux e Windows](../machine-learning/data-science-virtual-machine/overview.md) fornece uma grande quantidade de informações sobre DSVMs.
   >
   >

1. Na coluna **STATUS DA OFERTA** da imagem, selecione **Revisão dos termos necessária**.

1. Na janela Configurar Implantação Programática, selecione **Habilitar**.

    ![Janela Configurar Implantação Programática](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Você poderá ver várias assinaturas listadas na janela Configurar Implantação Programática. Verifique se que você está habilitando a implantação programática apenas para a assinatura pretendida.
   >
   >


1. Selecione **Salvar**. 

    Na lista de imagens do marketplace, essa imagem agora mostra **Termos aceitos** e está disponível para os usuários criarem máquinas virtuais.

> [!NOTE]
> Os usuários podem criar uma imagem personalizada de uma imagem licenciada. Consulte [Criar uma imagem personalizada de um arquivo VHD](devtest-lab-create-template.md) para obter mais informações.
>
>


## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](./devtest-lab-faq.md#blog-post)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Criar uma imagem personalizada de uma VM](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Criar uma imagem personalizada de um arquivo VHD](devtest-lab-create-template.md)
- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)
