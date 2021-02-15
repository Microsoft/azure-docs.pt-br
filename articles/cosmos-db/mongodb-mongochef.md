---
title: Use o Studio 3T para se conectar à API do Azure Cosmos DB para MongoDB
description: Saiba como se conectar à API de um Azure Cosmos DB para MongoDB usando o Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096505"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Conectar-se a uma conta do Azure Cosmos usando o Studio 3T
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Para se conectar à API de um Azure Cosmos DB para MongoDB usando o Studio 3T, você deve:

* Baixe e instale o [Studio 3T](https://studio3t.com/).
* Tenha as informações da cadeia de [conexão](connect-mongodb-account.md) da sua conta do Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Criar a conexão no Studio 3T

Para adicionar sua conta do Azure Cosmos ao Gerenciador de conexões do 3T Studio, use as seguintes etapas:

1. Recupere as informações de conexão para a conta da API para MongoDB do Azure Cosmos DB usando as instruções no artigo [conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Captura de tela da página cadeia de conexão":::

2. Clique em **Conectar** para abrir o Gerenciador de Conexões e clique em **Nova Conexão**

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Captura de tela da página cadeia de conexão":::
3. Na janela **nova conexão** , na guia **servidor** , insira o host (FQDN) da conta do Azure Cosmos e da porta.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Captura de tela da página cadeia de conexão":::
4. Na janela **Nova Conexão** , na guia **Autenticação** , escolha o Modo de Autenticação **(MONGODB-CR ou SCARM-SHA-1) Básico** e insira NOME DE USUÁRIO e SENHA.  Aceite o banco de dados de autenticação padrão (admin) ou forneça seu próprio valor.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Captura de tela da página cadeia de conexão":::
5. Na janela **Nova Conexão** , na guia **SSL** , marque a caixa de seleção **Usar protocolo SSL para se conectar** e o botão de opção **Aceitar certificados SSL de servidor autoassinados** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Captura de tela da página cadeia de conexão":::
6. Clique no botão **Testar Conectividade** para validar as informações de conexão, clique em **OK** para retornar à janela Nova Conexão e clique em **Salvar** .

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Captura de tela da página cadeia de conexão":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usar o Studio 3T para criar um banco de dados, coleção e documentos
Para criar um banco de dados, coleta e documentos usando o Studio 3T, execute as seguintes etapas:

1. No **Gerenciador de Conexões** , realce a conexão e clique em **Conectar** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Captura de tela da página cadeia de conexão":::
2. Clique com o botão direito do mouse no host e escolha **Adicionar Banco de Dados** .  Forneça um nome de banco de dados e clique em **OK** .

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Captura de tela da página cadeia de conexão":::
3. Clique com o botão direito do mouse no banco de dados e escolha **Adicionar Coleção** .  Forneça um nome de coleção e clique em **Criar** .

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Captura de tela da página cadeia de conexão":::
4. Clique no item de menu **Coleção** e clique em **Adicionar Documento** .

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Captura de tela da página cadeia de conexão" },
        "isRegistered": false
    }
    ```

7. Execute uma consulta de exemplo. Por exemplo, procure famílias com o sobrenome 'Andersen' e retorne os campos de estado e pais.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Captura de tela da página cadeia de conexão":::

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
