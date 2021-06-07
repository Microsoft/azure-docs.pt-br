---
title: Configurar uma conta de laboratório com o Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório com o Azure Lab Services, adicionar um criador de laboratório e especificar imagens do Marketplace a serem usadas pelos laboratórios na conta do laboratório.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445059"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutorial: Configurar uma conta de laboratório com o Azure Lab Services
No Azure Lab Services, uma conta de laboratório serve como a conta central na qual os laboratórios da sua organização são gerenciados. Em sua conta de laboratório, conceda permissão para outras pessoas criarem laboratórios e definir políticas que se aplicam a todos os laboratórios na conta do laboratório. Neste tutorial, saiba como criar uma conta de laboratório. 

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar uma conta de laboratório
> * Adicionar um usuário à função de criador de laboratório

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
As etapas a seguir ilustram como usar o portal do Azure para criar uma conta de laboratório no Azure Lab Services. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **DevOps** em **Categorias**. Em seguida, selecione **Serviços de Laboratório**. Se você selecionar a estrela (`*`) ao lado de **Serviços de Laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **Serviços de Laboratório**, selecione **Adicionar** na barra de ferramentas ou selecione **Criar conta de laboratório** na página. 

    ![Selecione Adicionar na página Contas de laboratório](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na guia **Básico** da página **Criar uma conta de laboratório**, execute as seguintes ações: 
    1. Para **Nome da conta de laboratório**, insira um nome. 
    2. Selecione a **Assinatura do Azure** na qual você quer criar a conta de laboratório.
    3. Para **Grupo de recursos**, selecione um grupo de recursos existente ou selecione **Criar novo** e insira um nome para o grupo de recursos.
    4. Para **Local**, selecione um local/região em que você deseja criar a conta de laboratório. 

        ![Conta do laboratório – Página de Noções Básicas](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Selecione **Examinar + criar**.
    6. Revise o resumo e selecione **Criar**. 

        ![Revisar + criar -> Criar](./media/tutorial-setup-lab-account/create-button.png)    
5. Depois que a implantação estiver concluída, expanda **Próximas etapas** e selecione **Ir para o recurso**. 

    ![Página da conta de laboratório](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Confirme se você vê a página **Conta do Laboratório**. 

    ![Página da conta de laboratório](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um usuário à função de criador de laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

> [!NOTE]
> A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso neste tutorial, ignore esta etapa. 

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** , selecione **+ Adicionar** na barra de ferramentas e, em seguida, selecione **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou uma conta de laboratório. Para saber mais sobre como criar um laboratório de sala de aula como educador, vá para o próximo tutorial:

> [!div class="nextstepaction"]
> [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)

