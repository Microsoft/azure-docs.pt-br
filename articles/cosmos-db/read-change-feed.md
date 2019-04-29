---
title: Acessando a alteração de feed no Azure Cosmos DB do Azure Cosmos DB
description: Este artigo descreve diferentes opções disponíveis para ler e acessar o feed de alterações no Azure Cosmos DB no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 459ed4b6f16d3cfe5bb792be7f063a1253a3006e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60927793"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lendo o Azure Cosmos DB alterar feed

Você pode trabalhar com o feed de alteração do Azure Cosmos DB usando qualquer uma das seguintes opções:

* Usando o Azure Functions
* Usando a biblioteca do processador de feed de alterações
* Usando a API do SQL do Azure Cosmos DB

## <a name="using-azure-functions"></a>Usando o Azure Functions

Funções do Azure é a opção mais simples e recomendada. Quando você cria um disparador do Azure Cosmos DB em um aplicativo do Azure Functions, pode selecionar o contêiner para se conectar e a Função do Azure é acionada sempre que houver uma alteração no contêiner. Os gatilhos podem ser criados usando o portal Funções do Azure, o portal do Azure Cosmos DB ou programaticamente com SDKs. O Visual Studio e o VS Code fornecem suporte para escrever as Funções do Azure e você pode até mesmo usar a CLI do Azure Functions para o desenvolvimento de plataforma cruzada. Você pode gravar e depurar o código em sua área de trabalho e, em seguida, implantar a função com um clique. Consulte [Computação de banco de dados sem servidor usando os artigos](serverless-computing-database.md) e [Usando os feeds do Azure](change-feed-functions.md) do Azure Functions para saber mais.

## <a name="using-the-change-feed-processor-library"></a>Usando a biblioteca do processador de feed de alterações

A biblioteca do processador de feed de alterações oculta a complexidade e ainda fornece um controle completo do feed de alterações. A biblioteca segue o padrão do observador, onde sua função de processamento é chamada pela biblioteca. Se você tiver feed de alterações de alta taxa de transferência, poderá instanciar vários clientes para ler o feed de alterações. Como você está usando a biblioteca do processador de feeds de mudança, ele irá dividir automaticamente a carga entre os diferentes clientes sem que você tenha que implementar essa lógica. Toda a complexidade é tratada pela biblioteca. Se você quiser ter seu próprio balanceador de carga, poderá implementar `IPartitionLoadBalancingStrategy` para uma estratégia de partição personalizada para processar o feed de alterações. Para saber mais, consulte [usando a biblioteca do processador de feeds de alterações](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Usando a API do SQL do Azure Cosmos DB

Com o SDK, você obtém um controle de baixo nível do feed de alterações. Você pode gerenciar o ponto de verificação, acessar uma determinada chave de partição lógica, etc. Se você tiver vários leitores, poderá usar `ChangeFeedOptions` para distribuir o carregamento de leitura para segmentos diferentes ou clientes diferentes. 

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Biblioteca do processador do feed usando a alteração](change-feed-processor.md)
