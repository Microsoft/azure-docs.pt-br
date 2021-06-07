---
title: incluir arquivo
description: incluir arquivo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 941e2ee7b8eba957d970998324156c50a86439af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94503999"
---
1. Em uma nova janela do navegador, entre no [Portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Criar um recurso**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="Crie um recurso no portal do Azure":::
   
3. Na página **Novo**, selecione **Bancos de dados** > **Azure Cosmos DB**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="O painel Bancos de Dados do portal do Azure":::
   
3. Na página **Criar Conta do Azure Cosmos DB**, insira as configurações da nova conta do Azure Cosmos DB. 
 
    Configuração|Valor|Descrição
    ---|---|---
    Subscription|Sua assinatura|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos DB. 
    Grupo de recursos|**Criar** e, em seguida, Nome da Conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a conta. Para simplificar, use o mesmo nome que o Nome da Conta do Azure Cosmos DB. 
    Nome da Conta|Um nome exclusivo|Insira um nome exclusivo para identificar a conta do Azure Cosmos DB.<br><br>O nome da conta pode usar apenas letras minúsculas, números e hifens (-) e deve ter de 3 a 31 caracteres.
    API|Tabela|A API determina o tipo de conta a ser criada. O Azure Cosmos DB fornece cinco APIs: Core (SQL) para bancos de dados de documentos, Gremlin para bancos de dados de grafos, MongoDB para bancos de dados de documentos, Tabela do Azure e Cassandra. É necessário criar uma conta separada para cada API. <br><br>Selecione **Tabela do Azure**, porque neste início rápido você criará uma tabela que funciona com a API de Tabela. <br><br>[Saiba mais sobre a API de Tabela](../articles/cosmos-db/table-introduction.md).|
    Location|A região mais próxima dos usuários|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use o local mais próximo dos usuários para fornecer a eles acesso mais rápido aos dados.
    Modo de capacidade|Taxa de transferência provisionada ou sem servidor|Selecione **Taxa de transferência provisionada** para criar uma conta no modo [taxa de transferência provisionada](../articles/cosmos-db/set-throughput.md). Selecione **Sem servidor** para criar uma conta no modo [sem servidor](../articles/cosmos-db/serverless.md).

    Mantenha as opções **Redundância Geográfica** e **Gravações em Várias Regiões** como **Desabilitar** para evitar preços adicionais e ignore as seções **Rede** e **Marcas**.

5. Selecione **Revisar + Criar**. Após a concluir a validação, selecione **Criar** para criar a conta. 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="A página da nova conta do Azure Cosmos DB":::

6. São necessários alguns minutos para criar a conta. Um mensagem informando **A implantação está em andamento** será exibida. Aguarde a conclusão da implantação e, em seguida, selecione **Ir para o recurso**.

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="O painel de notificações do portal do Azure":::

