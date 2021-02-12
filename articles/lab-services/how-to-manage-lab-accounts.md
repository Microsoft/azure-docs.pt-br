---
title: Gerenciar contas de laboratório no Azure Lab Services | Microsoft Docs
description: Saiba como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório em uma assinatura do Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434083"
---
# <a name="create-and-manage-lab-accounts"></a>Criar e gerenciar contas de laboratório
No Azure Lab Services, uma conta de laboratório é um contêiner para tipos de laboratório gerenciados, como laboratórios. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
As etapas a seguir ilustram como usar o portal do Azure para criar uma conta de laboratório no Azure Lab Services. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Contas de laboratório** na seção **DevOps**. Se você selecionar a estrela (`*`) ao lado de **Contas de laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Contas de laboratório** em **FAVORITOS**.

    ![Todos os serviços -> Contas de laboratório](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **Contas de laboratório**, selecione **Adicionar** na barra de ferramentas ou **Criar conta de laboratório** na página. 

    ![Selecione Adicionar na página Contas de laboratório](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na guia **Básico** da página **Criar uma conta de laboratório**, execute as seguintes ações: 
    1. Para **Nome da conta de laboratório**, insira um nome. 
    2. Selecione a **Assinatura do Azure** na qual você quer criar a conta de laboratório.
    3. Para **Grupo de recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    4. Para **Local**, selecione um local/região em que você deseja que a conta de laboratório seja criada.
    5. Para o campo **Permitir que o criador de laboratório escolha a localização do laboratório**, especifique se deseja que os criadores de laboratório possam selecionar uma localização para o laboratório. Por padrão, a opção está desabilitada. Quando ela estiver desabilitada, os criadores de laboratório não poderão especificar uma localização para o laboratório que estão criando. Os laboratórios são criados na localização geográfica mais próxima da conta de laboratório. Quando ela estiver habilitada, um criador de laboratório poderá selecionar uma localização no momento da criação de um laboratório. Para obter mais informações, consulte [Permitir que o criador do laboratório escolha o local do laboratório](allow-lab-creator-pick-lab-location.md). 

        ![Criar conta de laboratório -> Noções Básicas](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Selecione **Avançar: Avançado** na parte inferior da página para navegar até a guia **Avançado**. Em seguida, execute as seguintes etapas: 
    1. Selecione uma **galeria de imagens compartilhadas** existente ou crie uma. Você pode salvar a VM do modelo na galeria de imagens compartilhadas para que ela seja reutilizada por outros. Para obter informações detalhadas sobre galerias de imagens compartilhadas, confira [Usar uma galeria de imagens compartilhadas no Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Especifique se deseja **desligar automaticamente as máquinas virtuais do Windows** quando os usuários se desconectarem delas. Especifique quanto tempo as máquinas virtuais deverão esperar até o usuário se reconectar antes do desligamento automático. 
    3. Para **Emparelhar rede virtual**, selecione uma VNet (rede virtual) de emparelhamento para a rede de laboratório. Os laboratórios criados nesta conta são conectados à VNet selecionada e têm acesso aos recursos na rede virtual selecionada. Para obter mais informações, consulte [Conectar a rede virtual do seu laboratório a uma rede virtual par](how-to-connect-peer-virtual-network.md).    
    8. Especifique um **intervalo de endereços** para VMs do laboratório. O intervalo de endereços deve estar na notação CIDR (Roteamento entre Domínios sem Classificação) (exemplo: 10.20.0.0/23). As máquinas virtuais do laboratório serão criadas nesse intervalo de endereços. Para obter mais informações, confira [Especificar um intervalo de endereços para as VMs do laboratório](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > A propriedade **intervalo de endereços** só será aplicável se uma **rede virtual par** estiver habilitada para o laboratório.

        ![Criar conta de laboratório -> Avançado](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Selecione **Avançar: Marcas** na parte inferior da página para alternar para a guia **Marcas**. Adicione as marcas que você deseja associar à conta do laboratório. Marcas são pares nome/valor que permitem categorizar recursos e exibir a cobrança consolidada por meio da aplicação da mesma marca a vários recursos e grupos de recursos. Para obter mais informações, veja [Usar marcas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).

    ![Captura de tela que mostra a página "criar conta de laboratório" com a guia marcas realçada.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Selecione **Revisar + Criar** na parte inferior desta página para alternar para a guia **Revisar + Criar**. 
4. Examine as informações de resumo nesta página e selecione **Criar**. 

    ![Criar conta de laboratório -> Marcas](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Aguarde até que a implantação seja concluída, expanda **Próximas etapas** e selecione **Ir para o recurso**, conforme mostrado na imagem a seguir: 

    Você também pode selecionar o **ícone de sino** na barra de ferramentas (**Notificações**), confirme se a implantação foi bem-sucedida e, em seguida, selecione **Ir para o recurso**. 

    Como alternativa, selecione **Atualizar** na página **Contas de laboratório** e, em seguida, selecione a conta de laboratório que você criou. 

    ![Janela Criar uma conta de laboratório](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Você verá a seguinte página de **conta de laboratório**:

    ![Página da conta de laboratório](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Exibir contas de laboratório
1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu. 
3. Selecione **Contas de Laboratório** para o **tipo**. 
    Você também pode filtrar por assinatura, grupo de recursos, locais e marcas. 

    ![Todos os recursos -> Contas de laboratório](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Excluir uma conta de laboratório
Siga as instruções da seção anterior que exibe as contas de laboratório em uma lista. Use as instruções a seguir para excluir uma conta de laboratório: 

1. Selecione a **conta de laboratório** que você deseja excluir. 
2. Selecione **Excluir** na barra de ferramentas. 

    ![Contas de laboratório -> botão Excluir](./media/how-to-manage-lab-accounts/delete-button.png)
1. Digite **Sim** para confirmar.
1. Selecione **Excluir**. 

    ![Excluir conta de laboratório – confirmação](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Você também pode usar o módulo do PowerShell Az.LabServices (visualização) para gerenciar contas de laboratório. Para obter mais informações, consulte a [página inicial do Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Próximas etapas
Consulte outros artigos na seção **Guias de instruções** -> **Criar e configurar contas de laboratório (proprietário da conta do laboratório)** do TOC (sumário). 