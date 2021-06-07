---
title: incluir arquivo
description: incluir arquivo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/27/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 736ab630b035ae1a668332eb5ab5aa9530ef0e88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95972395"
---
1. Em uma nova janela do navegador, entre no [Portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Criar um recurso**.
   
   ![Crie um recurso no portal do Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na página **Novo**, selecione **Bancos de dados** > **Azure Cosmos DB**.
   
   ![O painel Bancos de Dados do portal do Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
   > [!TIP]
   > Se você não vir o Azure Cosmos DB na lista, basta digitar na caixa de pesquisa na parte superior da página e pressionar a tecla _Enter_. 

3. Na página **Criar Conta do Azure Cosmos DB**, insira as configurações da nova conta do Azure Cosmos DB. 
 
    Configuração|Valor|Descrição
    ---|---|---
    Subscription|Sua assinatura|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos DB. 
    Grupo de recursos|Criar Novo<br><br>Em seguida, digite o mesmo nome que o Nome da Conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a conta. Para simplificar, use o mesmo nome que o Nome da Conta do Azure Cosmos DB. 
    Nome da Conta|Insira um nome exclusivo|Insira um nome exclusivo para identificar a conta do Azure Cosmos DB. O URI da sua conta será *gremlin.azure.com* acrescentado ao nome da conta exclusivo.<br><br>O nome da conta pode usar apenas letras minúsculas, números e hifens (-) e deve ter de 3 a 31 caracteres.
    API|Gremlin (grafo)|A API determina o tipo de conta a ser criada. O Azure Cosmos DB fornece cinco APIs: Core (SQL) para bancos de dados de documentos, Gremlin para bancos de dados de grafos, MongoDB para bancos de dados de documentos, Tabela do Azure e Cassandra. É necessário criar uma conta separada para cada API. <br><br>Selecione **Gremlin (grafo)**, porque neste início rápido você está criando uma tabela que funciona com a API do Gremlin. <br><br>[Saiba mais sobre a API do Gremlin](../articles/cosmos-db/graph-introduction.md).|
    Location|Selecione a região mais próxima de seus usuários|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use o local mais próximo dos usuários para fornecer a eles acesso mais rápido aos dados.
    Modo de capacidade|Taxa de transferência provisionada ou sem servidor|Selecione **Taxa de transferência provisionada** para criar uma conta no modo [taxa de transferência provisionada](../articles/cosmos-db/set-throughput.md). Selecione **Sem servidor** para criar uma conta no modo [sem servidor](../articles/cosmos-db/serverless.md).

    Selecione **Revisar + Criar**. Você pode ignorar as seções **Rede** e **Marcas**. 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. A criação da conta leva alguns minutos. Aguarde até que o portal exiba a página **Parabéns! Sua conta do Azure Cosmos DB foi criada**.
   
   ![Página Conta do Azure Cosmos DB criada](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

