---
title: "Adicionar o conector do Banco de Dados SQL em seus Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral do conector do Banco de Dados SQL do Azure com parâmetros da API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: a3d5cb909dbfcb00f3fbfa0165bb6cd58eb18688
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Introdução ao conector do Banco de Dados SQL
Usando o conector do Banco de Dados SQL, crie fluxos de trabalho para sua organização que gerenciam dados nas tabelas. 

Com o Banco de Dados SQL, você:

* Compile o fluxo de trabalho adicionando um novo cliente a um banco de dados de clientes ou atualizando um pedido em um banco de dados de pedidos.
* Use as ações para obter uma linha de dados, inserir uma nova linha e até mesmo excluir. Por exemplo, quando um registro é criado no Dynamics CRM Online (um gatilho), insira uma linha em um Banco de Dados SQL do Azure (uma ação). 

Este tópico mostra como usar o conector do Banco de Dados SQL em um aplicativo lógico, além de listar as ações.

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) e [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Conectar-se ao Banco de Dados SQL do Azure
Antes do aplicativo lógico poder acessar qualquer serviço, primeiro crie uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao Banco de Dados SQL, você cria uma *conexão* do Banco de Dados SQL. Para criar uma conexão, insira as credenciais que normalmente usa para acessar o serviço ao qual você está se conectando. Desse modo, no Banco de Dados SQL, insira suas credenciais do Banco de Dados SQL para criar a conexão. 

#### <a name="create-the-connection"></a>Criar a conexão
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Usar um gatilho
Esse conector não tem gatilhos. Use outros gatilhos para iniciar o aplicativo lógico, como um gatilho de Recorrência, um gatilho de Webhook HTTP, gatilhos disponíveis com outros conectores e muito mais. [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) fornece um exemplo.

## <a name="use-an-action"></a>Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de mais. Você tem várias opções: **adicionar uma ação**, **adicionar uma condição** ou uma das opções **Mais**.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "sql" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. Em nosso exemplo, escolha **SQL Server – obter linha**. Se uma conexão já existir, escolha o **Nome da tabela** na lista suspensa e insira a **ID da Linha** que deseja retornar.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-sqlazure.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Nesse exemplo, retornamos uma linha de uma tabela. Para ver os dados nessa linha, adicione outra ação que cria um arquivo usando os campos da tabela. Por exemplo, adicione uma ação do OneDrive que usa os campos FirstName e LastName para criar um novo arquivo na conta de armazenamento de nuvem. 
   > 
   > 
5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/sql/). 

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

