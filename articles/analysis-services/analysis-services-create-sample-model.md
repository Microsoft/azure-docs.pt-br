---
title: Tutorial – adicionar um modelo de exemplo – Azure Analysis Services | Microsoft Docs
description: Neste tutorial, aprenda a adicionar um modelo de exemplo no Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f882a40940a5c7202e9cf1f5c8b8927f008f4a39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92013603"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Tutorial: Adicionar um modelo de exemplo do portal

Neste tutorial, você deve adicionar um modelo de tabela de banco de dados de exemplo do Adventure Works ao seu servidor. O modelo de exemplo é uma versão completa do modelo de dados de exemplo de Vendas pela Internet do Adventure Works (1200). Um modelo de amostra é útil para testar o gerenciamento de modelos, a conexão com ferramentas e aplicativos do cliente e consultar os dados do modelo. Este tutorial usa o [portal do Azure](https://portal.azure.com) e o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para: 

> [!div class="checklist"]
> * Adicionar um modelo de dados de tabela de exemplo completo em um servidor 
> * Conectar ao modelo com SSMS

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Um servidor do Analysis Services do Azure. Para saber mais, consulte [Criar um servidor - portal](analysis-services-create-server.md).
- Permissões de administrador do servidor
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Adicionar um modelo de exemplo

1. Na **Visão Geral** do servidor, clique em **Novo modelo**.

    ![Criar um modelo de amostra](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. Em **Novo modelo** > **Escolha uma fonte de dados**, verifique se **Dados de exemplo** está selecionado e, em seguida, clique em **Adicionar**.

    ![Selecionar Novo modelo](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Em **Visão geral**, verifique se o modelo de exemplo `adventureworks` foi adicionado.

    ![Selecionar dados de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Seu modelo de amostra está usando recursos de memória cache. Se você não estiver usando o modelo de amostra para testes, você deverá removê-lo do servidor.

Essas etapas descrevem como excluir um modelo de um servidor usando o SSMS.

1. No SSMS > **Pesquisador de Objetos**, clique em **Conectar** > **Analysis Services**.

2. Em **Conectar ao servidor**, cole no nome do servidor e, em seguida, em **Autenticação**, escolha **Active Directory - Universal com suporte MFA**, insira seu nome de usuário e, em seguida, clique em **Conectar**.

    ![Entrar](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. No **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados de exemplo `adventureworks` e, em seguida, clique em **Excluir**.

    ![Excluir banco de dados de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Próximas etapas 

Neste tutorial, você aprendeu como adicionar um modelo de exemplo básico, ao seu servidor. Agora que você tem um modelo de banco de dados, você pode se conectar a ele partir do SQL Server Management Studio e adicionar funções de usuário. Para saber mais, continue com o próximo tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Configurar funções de usuário e administrador do servidor](tutorials/analysis-services-tutorial-roles.md)