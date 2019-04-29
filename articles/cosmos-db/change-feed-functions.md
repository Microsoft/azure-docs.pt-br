---
title: Como usar o feed de alterações do Azure Cosmos DB com o Azure Functions
description: Usar o feed de alterações do Azure Cosmos DB com o Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112014"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquiteturas sem servidor baseado em evento com o Azure Cosmos DB e o Azure Functions

O Azure Functions fornece a maneira mais simples para conectar-se para o [o feed de alterações](change-feed.md). Você pode criar pequenas funções reativo do Azure que será disparado automaticamente em cada novo evento no feed de alterações do contêiner do Azure Cosmos.

![Funções sem servidor baseado em evento, trabalhando com o gatilho do Azure Cosmos DB](./media/change-feed-functions/functions.png)

Com o [gatilho do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), você pode aproveitar o [processador do Feed de alteração](./change-feed-processor.md)de dimensionamento do e a funcionalidade de detecção de eventos confiável sem a necessidade de manter qualquer [trabalhador infraestrutura](./change-feed-processor.md#implementing-the-change-feed-processor-library). Basta se concentrar na lógica da Azure Function sem se preocupar com o resto do pipeline de fornecimento de eventos. Você pode até mesmo combinar o gatilho com qualquer outro [associações do Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Atualmente, o gatilho do Azure Cosmos DB tem suporte para uso com o Core (API do SQL) apenas.

## <a name="requirements"></a>Requisitos

Para implementar um fluxo de baseado em evento sem servidor, você precisa:

* **O contêiner monitorado**: Contêiner monitorado é o contêiner do Azure Cosmos que está sendo monitorado e armazena os dados do qual o feed de alterações é gerado. Todas as inserções e alterações (por exemplo, CRUD) ao contêiner monitorado são refletidas no feed de alterações do contêiner.
* **O contêiner de concessão**: O contêiner de concessão mantém o estado entre vários e instâncias de Azure Function sem servidor dinâmica e permite o dimensionamento dinâmico. Esse contêiner de concessão pode ser manual ou automaticamente criada pelo Trigger.To do Azure Cosmos DB automaticamente criar o contêiner de concessão, defina a *CreateLeaseCollectionIfNotExists* sinalizador no [deconfiguração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Contêineres de concessão particionadas são necessários para ter um `/id` definição de chave de partição.

## <a name="create-your-azure-cosmos-db-trigger"></a>Criar o gatilho do Azure Cosmos DB

Agora há suporte para a criação de sua função do Azure com um gatilho do Azure Cosmos DB em todos os Azure Functions IDE e integrações de CLI:

* [Extensão do Visual Studio](../azure-functions/functions-develop-vs.md) para usuários do Visual Studio.
* [Extensão do Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) para usuários do Visual Studio Code.
* E finalmente [ferramentas da CLI Core](../azure-functions/functions-run-local.md#create-func) para uma experiência independente do IDE de plataforma cruzada.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Executar o gatilho do Azure Cosmos DB localmente

Você pode executar sua [Azure Function localmente](../azure-functions/functions-develop-local.md) com o [emulador do Azure Cosmos DB](./local-emulator.md) para criar e desenvolver seus fluxos com base em evento sem servidor sem uma assinatura do Azure ou incorrer em custos.

Se você quiser testar cenários ao vivo na nuvem, você poderá [Experimente gratuitamente o Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sem nenhum cartão de crédito ou assinatura do Azure necessários.

## <a name="next-steps"></a>Próximas etapas

Agora, você pode continuar saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Maneiras de ler o feed de alterações](read-change-feed.md)
* [Usando a biblioteca do processador do feed de alterações](change-feed-processor.md)
* [Como trabalhar com a biblioteca do processador de feed de alterações](change-feed-processor.md)
* [Computação de banco de dados sem servidor usando o Azure Cosmos DB e o Azure Functions](serverless-computing-database.md)
