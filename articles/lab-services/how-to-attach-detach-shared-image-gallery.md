---
title: Anexar ou desanexar uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Este artigo descreve como anexar uma galeria de imagens compartilhadas a um laboratório de sala de aula no Azure Lab Services.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 99b944ad85e311cd66b0a1cec4e585de37f83500
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786394"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anexar ou desanexar uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como anexar uma galeria de imagens compartilhadas a uma conta de laboratório ou desanexá-la dessa conta. 

> [!NOTE]
> Quando você [salva uma imagem de modelo de um laboratório](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) no Azure Lab Services em uma galeria de imagens compartilhada, a imagem é carregada na galeria como uma imagem especializada. [Imagens especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) mantêm informações específicas do computador e perfis de usuário. Você ainda pode carregar diretamente uma imagem generalizada para a Galeria fora do Azure Lab Services. 
>
> Um criador de laboratório pode criar uma VM de modelo baseada em imagens generalizadas e especializadas em Azure Lab Services. 

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários aos quais esse recurso dá suporte: 

- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhadas à conta de laboratório e carrega uma imagem para a galeria de imagens compartilhadas fora do contexto de um laboratório. Em seguida, os criadores de laboratório podem usar essa imagem da galeria de imagens compartilhadas para criar laboratórios. 
- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhadas à conta de laboratório. Um criador de laboratório (instrutor) salva a imagem personalizada do laboratório na galeria de imagens compartilhadas. Em seguida, outros criadores de laboratório podem selecionar essa imagem na galeria de imagens compartilhadas para criar um modelo para os laboratórios deles. 

    Quando uma imagem é salva em uma galeria de imagens compartilhadas, o Azure Lab Services replica a imagem salva em outras regiões disponíveis na mesma [geografia](https://azure.microsoft.com/global-infrastructure/geographies/). Isso verifica se a imagem está disponível para laboratórios criados em outras regiões na mesma geografia. Salvar imagens em uma galeria de imagens compartilhadas gera um custo adicional, que inclui o custo de todas as imagens replicadas. Esse custo é separado do custo de uso do Azure Lab Services. Para obter mais informações sobre o preço da Galeria de Imagens Compartilhadas, confira [Galeria de Imagens Compartilhadas – cobrança](../virtual-machines/shared-image-galleries.md#billing).

> [!IMPORTANT]
> Ao usar uma galeria de imagens compartilhadas, o Azure Lab Services dá suporte apenas a imagens com menos de 128 GB de espaço em disco do sistema operacional. Imagens com mais de 128 GB de espaço em disco ou vários discos não serão mostradas na lista de imagens de máquina virtual durante a criação do laboratório.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta de laboratório
Ao criar uma conta de laboratório, você pode anexar uma galeria de imagens compartilhadas à conta de laboratório. Você pode selecionar uma galeria de imagens compartilhadas existente na lista suspensa ou criar uma. Para criar e anexar uma galeria de imagens compartilhadas à conta de laboratório, selecione **Criar**, insira um nome para a galeria e digite **OK**. 

![Configurar a galeria de imagens compartilhadas no momento da criação da conta de laboratório](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurar após a criação da conta de laboratório
Após a criação da conta de laboratório, você poderá executar as seguintes tarefas:

- Criar e anexar uma galeria de imagens compartilhadas
- Anexar uma galeria de imagens compartilhadas à conta de laboratório
- Desanexar uma galeria de imagens compartilhadas da conta de laboratório

## <a name="create-and-attach-a-shared-image-gallery"></a>Criar e anexar uma galeria de imagens compartilhadas
1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Serviços de Laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione sua conta de laboratório para ver a página da **Conta de Laboratório**. 
4. Selecione **Galeria de imagens compartilhadas** no menu à esquerda e selecione **+ Criar** na barra de ferramentas.  

    ![Botão Criar galeria de imagens compartilhadas](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Na janela **Criar galeria de imagens compartilhadas**, insira um **nome** para a galeria e digite **OK**. 

    ![Janela Criar galeria de imagens compartilhadas](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    O Azure Lab Services cria a galeria de imagens compartilhadas e a anexa à conta de laboratório. Todos os laboratórios criados nessa conta de laboratório têm acesso à galeria de imagens compartilhadas anexada. 

    ![Galeria de imagens anexadas](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    No painel inferior, você vê imagens na galeria de imagens compartilhadas. Nessa nova galeria, não há imagens. Quando você carrega imagens na galeria, elas são exibidas nesta página.     

    Todas as imagens na galeria de imagens compartilhadas anexada são habilitadas por padrão. Você pode habilitar ou desabilitar as imagens selecionadas selecionando-as na lista e usando o botão **Habilitar imagens selecionadas** ou **Desabilitar imagens selecionadas**.

## <a name="attach-an-existing-shared-image-gallery"></a>Anexar uma galeria de imagens compartilhadas existente
O procedimento a seguir mostra como anexar uma galeria de imagens compartilhadas existente a uma conta de laboratório. 

1. Na página **Conta de Laboratório**, selecione **Galeria de imagens compartilhadas** no menu à esquerda e selecione **Anexar** na barra de ferramentas. 

    ![Galeria de imagens compartilhadas – botão Adicionar](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na página **Anexar uma Galeria de Imagens Compartilhadas existente**, selecione sua galeria de imagens compartilhadas e selecione **OK**.

    ![Selecionar uma galeria existente](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Você verá a seguinte tela: 

    ![Minha galeria na lista](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Nesse exemplo, ainda não há imagens na galeria de imagens compartilhadas.

    A identidade do Azure Lab Services é adicionada como um colaborador à galeria de imagens compartilhadas anexada ao laboratório. Ele permite que os educadores/administradores de TI salvem imagens de máquinas virtuais na galeria de imagens compartilhadas. Todos os laboratórios criados nessa conta de laboratório têm acesso à galeria de imagens compartilhadas anexada. 

    Todas as imagens na galeria de imagens compartilhadas anexada são habilitadas por padrão. Você pode habilitar ou desabilitar as imagens selecionadas selecionando-as na lista e usando o botão **Habilitar imagens selecionadas** ou **Desabilitar imagens selecionadas**. 

## <a name="detach-a-shared-image-gallery"></a>Desanexar uma galeria de imagens compartilhadas
Apenas uma galeria de imagens compartilhadas pode ser anexada a um laboratório. Se você quiser anexar outra galeria de imagens compartilhadas, desanexe a atual antes de anexar a nova. Para desanexar uma galeria de imagens compartilhadas do seu laboratório, selecione **Desanexar** na barra de ferramentas e confirme a operação de desanexação. 

![Desanexar a galeria de imagens compartilhadas da conta de laboratório](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como salvar uma imagem de laboratório na galeria de imagens compartilhadas ou usar uma imagem da galeria de imagens compartilhadas para criar uma VM, confira [Como usar a galeria de imagens compartilhadas](how-to-use-shared-image-gallery.md).

Para obter mais informações sobre galerias de imagens compartilhadas em geral, confira [galeria de imagens compartilhadas](../virtual-machines/shared-image-galleries.md).