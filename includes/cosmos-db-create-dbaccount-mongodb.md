---
title: incluir arquivo
description: incluir arquivo
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ad4445cbea6553a7a96299e1276dbe8f3816e166
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994529"
---
1. Em uma nova janela do navegador, entre no [Portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Criar um recurso**.
   
   ![Crie um recurso no portal do Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na página **Novo**, selecione **Bancos de dados** > **Azure Cosmos DB**.
   
   ![O painel Bancos de Dados do portal do Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na página **Criar Conta do Azure Cosmos DB**, insira as configurações da nova conta do Azure Cosmos DB. 
 
    Configuração|Valor|Descrição
    ---|---|---
    Subscription|Sua assinatura|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos DB. 
    Grupo de recursos|Criar Novo<br><br>Em seguida, digite o mesmo nome que o Nome da Conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a conta. Para simplificar, use o mesmo nome que o Nome da Conta do Azure Cosmos DB. 
    Nome da Conta|Insira um nome exclusivo|Insira um nome exclusivo para identificar a conta do Azure Cosmos DB. O URI da sua conta será *mongo.cosmos.azure.com* acrescentado ao nome da conta exclusivo.<br><br>O nome da conta pode usar apenas letras minúsculas, números e hifens (-) e deve ter de 3 a 31 caracteres.
    API|API do Azure Cosmos DB para MongoDB|A API determina o tipo de conta a ser criada. O Azure Cosmos DB fornece cinco APIs: Core (SQL) para bancos de dados de documentos, Gremlin para o bancos de dados de grafos, API do Azure Cosmos DB para MongoDB para bancos de dados de documentos, Tabela do Azure e Cassandra. No momento, você deve criar uma conta separada para cada API. <br><br>Selecione **API do Azure Cosmos DB para MongoDB** porque, neste guia de início rápido, você está criando uma coleção que funciona com o MongoDB.<br><br>[Saiba mais sobre a API do Azure Cosmos DB para MongoDB](../articles/cosmos-db/mongodb-introduction.md).|
    Location|Selecione a região mais próxima de seus usuários|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use o local mais próximo dos usuários para fornecer a eles acesso mais rápido aos dados.|
    Modo de capacidade|Taxa de transferência provisionada ou sem servidor|Selecione **Taxa de transferência provisionada** para criar uma conta no modo [taxa de transferência provisionada](../articles/cosmos-db/set-throughput.md). Selecione **Sem servidor** para criar uma conta no modo [sem servidor](../articles/cosmos-db/serverless.md).<br><br>**Observação**: somente a API do MongoDB versão 3.6 é compatível com contas sem servidor. Escolher 3.2 como a versão forçará a conta para o modo de taxa de transferência provisionada.

    Selecione **Revisar + Criar**. Você pode ignorar as seções **Rede** e **Marcas**. 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A criação da conta leva alguns minutos. Aguarde até que o portal exiba a página **Parabéns! Sua conta da API do Azure Cosmos DB para MongoDB está pronta**.

    ![O painel Notificações do portal do Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
