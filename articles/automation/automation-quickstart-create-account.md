---
title: Guia de Início Rápido do Azure – Criar uma conta de Automação do Azure | Microsoft Docs
description: Este artigo ajuda você a começar a criar uma conta da Automação do Azure e executar um runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83836695"
---
# <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

Você pode criar uma conta da Automação do Azure por meio do próprio Azure. Para isso, use o portal do Azure, uma interface do usuário baseada em navegador que permite o acesso a vários recursos. Uma conta de Automação pode gerenciar recursos em todas as regiões e assinaturas para determinado locatário. 

Este guia de início rápido orienta você para a criação de uma conta de Automação e para a execução de um runbook na conta. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

[Entrar no Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Criar uma conta de Automação

1. Escolha um nome para a sua conta do Azure. Os nomes da conta de Automação são exclusivos por região e grupo de recursos. Os nomes das contas de Automação que foram excluídas podem não estar disponíveis imediatamente.

    > [!NOTE]
    > Você não poderá alterar o nome da conta depois que ela tiver sido inserida na interface do usuário. 

2. Clique no botão **Criar um recurso**, localizado no canto superior esquerdo do portal do Azure.

3. Selecione **Ferramentas de Gerenciamento e TI** e selecione **Automação**.

4. Insira as informações da conta, incluindo o nome da conta selecionado. Para **Criar conta Executar como do Azure**, escolha **Sim** para que os artefatos que simplificam a autenticação do Azure sejam habilitados automaticamente. Quando as informações estiverem preenchidas, clique em **Criar** para iniciar a implantação da conta de Automação.

    ![Inserir informações sobre a conta de Automação na página](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Para obter uma lista atualizada dos locais em que você pode implantar uma conta de Automação, confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. Quando a implantação for concluída, clique em **Todos os Serviços**.

6. Selecione **Contas de Automação** e escolha a conta de Automação que você criou.

    ![Visão geral da conta de Automação](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Executar um runbook

Execute um dos runbooks tutoriais.

1. Clique em **Runbooks** em **Automação de Processo**. A lista de runbooks é exibida. Por padrão, vários runbooks tutoriais são habilitados na conta.

    ![Lista de runbooks da conta de Automação](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selecione o runbook **AzureAutomationTutorialScript**. Essa ação abre a página de visão geral do runbook.

    ![Visão geral do runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Clique em **Iniciar** e, na página Iniciar Runbook, clique em **OK** para iniciar o runbook.

    ![Página do trabalho de runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Após o status do trabalho se tornar `Running`, clique em **Saída** ou **Todos os Logs** para exibir a saída do trabalho de runbook. Para este runbook tutorial, a saída é uma lista dos seus recursos do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido você implantou uma conta de Automação, iniciou um trabalho de runbook e exibiu os resultados do trabalho. Para saber mais sobre a Automação do Azure, continue para o início rápido para criar seu primeiro runbook.

> [!div class="nextstepaction"]
> [Início Rápido da Automação – Criar um runbook da Automação do Azure](./automation-quickstart-create-runbook.md)

