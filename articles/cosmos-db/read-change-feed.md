---
title: Lendo o Azure Cosmos DB alterar feed
description: Este artigo descreve diferentes opções disponíveis para ler e acessar o feed de alterações no Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 7021367e1230573343ddf57ccd399d998ad5280e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339267"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lendo o Azure Cosmos DB alterar feed
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Você pode trabalhar com o feed de alterações do Azure Cosmos DB usando um modelo push ou pull. Com um modelo de push, o processador do feed de alterações envia o trabalho por push para um cliente que tem lógica de negócios para processar esse trabalho. No entanto, a complexidade na verificação do trabalho e do armazenamento do estado do último trabalho processado é tratada no processador do feed de alterações.

Com um modelo de pull, o cliente precisa efetuar pull do trabalho do servidor. O cliente, nesse caso, não só tem lógica de negócios para processar o trabalho, mas também armazenar o estado para o último trabalho processado, lidando com o balanceamento de carga entre vários clientes processando o trabalho em paralelo e manipulando erros.

Durante a leitura do feed de alterações do Azure Cosmos DB, geralmente é recomendável usar um modelo push porque você não precisará se preocupar com:

- A sondagem do feed de alterações em relação a alterações futuras.
- O armazenamento do estado da última alteração processada. Durante a leitura do feed de alterações, isso é automaticamente armazenado em um [contêiner de concessão](change-feed-processor.md#components-of-the-change-feed-processor).
- Balanceamento de carga entre vários clientes consumindo alterações. Por exemplo, se um cliente não puder acompanhar as alterações de processamento e outro tiver a capacidade disponível.
- [Manipulação de erros](change-feed-processor.md#error-handling). Por exemplo, repetir automaticamente as alterações com falha e que não foram processadas corretamente após uma exceção sem tratamento no código ou um problema de rede transitório.

A maioria dos cenários que usam o feed de alterações do Azure Cosmos DB usará uma das opções de modelo push. No entanto, há alguns cenários em que você talvez queira o controle de nível inferior adicional do modelo pull. Eles incluem:

- Ler as alterações de uma chave de partição específica
- Controlar o ritmo no qual seu cliente recebe alterações para processamento
- Fazer uma leitura única dos dados existentes no feed de alterações (por exemplo, fazer uma migração de dados)

## <a name="reading-change-feed-with-a-push-model"></a>Ler o feed de alterações com um modelo push

Usar um modelo push é a maneira mais fácil de ler no feed de alterações. Há duas maneiras de ler o feed de alterações com um modelo push: [Gatilhos do Azure Functions Cosmos DB](change-feed-functions.md) e [biblioteca do processador do feed de alterações](change-feed-processor.md). O Azure Functions usa o processador do feed de alterações nos bastidores, portanto, são maneiras muito semelhantes de ler o feed de alterações. Imagine o Azure Functions como uma plataforma de hospedagem para o processador do feed de alterações, não uma maneira totalmente diferente de ler o feed de alterações.

### <a name="azure-functions"></a>Funções do Azure

O Azure Functions é a opção mais simples, se você estiver apenas começando a usar o feed de alterações. Devido à sua simplicidade, também é a opção recomendada para a maioria dos casos de uso do feed de alterações. Quando você cria um gatilho do Azure Functions para Azure Cosmos DB, selecione o contêiner para se conectar, e o Azure Functions será acionado sempre que houver uma alteração no contêiner. Como o Azure Functions usa o processador do feed de alterações nos bastidores, ele paraleliza automaticamente o processamento de alterações nas [partições](partitioning-overview.md) do contêiner.

Desenvolver com o Azure Functions é uma experiência fácil e pode ser mais rápido do que implantar o processador do feed de alterações por conta própria. Os gatilhos podem ser criados no portal do Azure Functions ou programaticamente usando SDKs. O Visual Studio e o VS Code fornecem suporte para escrever as Funções do Azure e você pode até mesmo usar a CLI do Azure Functions para o desenvolvimento de plataforma cruzada. Você pode gravar e depurar o código em sua área de trabalho e, em seguida, implantar a função com um clique. Para saber mais, consulte os artigos [Computação de banco de dados sem servidor usando o Azure Functions](serverless-computing-database.md) e [Usar o feed de alterações com o Azure Functions](change-feed-functions.md).

### <a name="change-feed-processor-library"></a>Biblioteca do processador do feed de alterações

O processador do feed de alterações oferece a você mais controle do feed de alterações e ainda oculta mais complexidade. A biblioteca do processador do feed de alterações segue o padrão do observador, onde sua função de processamento é chamada pela biblioteca. A biblioteca do processador do feed de alterações verificará automaticamente se há alterações e, se forem encontradas alterações, efetue "push" delas para o cliente. Se você tiver feed de alterações de alta taxa de transferência, poderá instanciar vários clientes para ler o feed de alterações. A biblioteca do processador de feed de alterações dividirá automaticamente a carga entre os diferentes clientes. Você não precisará implementar nenhuma lógica para o balanceamento de carga entre vários clientes ou para manter o estado de concessão.

A biblioteca do processador do feed de alterações garante "pelo menos uma" entrega de todas as alterações. Em outras palavras, se você usar a biblioteca do processador do feed de alterações, sua função de processamento será chamada com êxito para cada item no feed de alterações. Se houver uma exceção sem tratamento na lógica de negócios em sua função de processamento, as alterações com falha serão repetidas até que elas sejam processadas com êxito. Para impedir que o processador do feed de alterações fique "preso" repetindo continuamente as mesmas alterações, adicione lógica à sua função de processamento para gravar documentos, mediante exceção, em uma fila de mensagens mortas. Saiba mais sobre o [tratamento de erro](change-feed-processor.md#error-handling).

No Azure Functions, a recomendação para tratar os erros é a mesma. Você ainda deve adicionar lógica em seu código delegado para gravar documentos, mediante exceção, em uma fila de mensagens mortas. No entanto, se houver uma exceção sem tratamento em sua função do Azure, a alteração que gerou a exceção não será repetida automaticamente. Se houver uma exceção sem tratamento na lógica de negócios, a função do Azure passará para o processamento da próxima alteração. A função do Azure não tentará novamente a mesma alteração com falha.

Como o Azure Functions, o desenvolvimento com a biblioteca do processador do feed de alterações é fácil. No entanto, você é responsável por implantar um ou mais hosts para o processador do feed de alterações. Um host é uma instância de aplicativo que usa o processador do feed de alterações para escutar alterações. Embora o Azure Functions tenha recursos para o dimensionamento automático, você é responsável por dimensionar seus hosts. Para saber mais, consulte [Usar o processador do feed de alterações](change-feed-processor.md#dynamic-scaling). A biblioteca do processador do feed de alterações faz parte do [SDK V3 do Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Ler o feed de alterações com um modelo pull

Com o [modelo pull do feed de alterações](change-feed-pull-model.md), você pode consumir o feed de alterações do Azure Cosmos DB em seu próprio ritmo. As alterações devem ser solicitadas pelo cliente e não há nenhuma sondagem automática para elas. Se você quiser "indicar" permanentemente a última alteração processada (semelhante ao contêiner de concessão do modelo push), será necessário [salvar um token de continuação](change-feed-pull-model.md#saving-continuation-tokens).

Usando o modelo pull do feed de alterações, você obtém um controle de nível mais baixo do feed de alterações. Ao ler o feed de alterações com o modelo pull, há três opções:

- Ler alterações para um contêiner inteiro
- Ler alterações para um determinado [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization)
- Ler alterações para um valor de chave de partição específico

Você pode paralelizar o processamento de alterações em vários clientes, assim como é possível com o processador do feed de alterações. No entanto, o modelo pull não trata automaticamente o balanceamento de carga entre clientes. Ao usar o modelo pull para paralelizar o processamento do feed de alterações, primeiro você obterá uma lista de FeedRanges. Um FeedRange representa um intervalo de valores de chave de partição. Você precisará ter um processo de orquestrador que obtém FeedRanges e os distribui entre seus computadores. Em seguida, você pode usar esses FeedRanges para que vários computadores leiam o feed de alterações em paralelo.

Não há nenhuma garantia de "pelo menos uma" entrega interna com o modelo pull. O modelo pull fornece controle de baixo nível para decidir como você gostaria de lidar com erros.

> [!NOTE]
> O modelo de pull do feed de alterações está atualmente somente em [versão prévia no SDK do .NET do Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview). A versão prévia ainda não está disponível para outras versões do SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed de alterações em APIs para Cassandra e MongoDB

A funcionalidade do feed de alterações é apresentada como fluxos de alteração na API do MongoDB e consulta com predicado em API do Cassandra. Para saber mais sobre os detalhes de implementação para a API do MongoDB, consulte [Fluxos de alteração na API do Azure Cosmos DB para o MongoDB](mongodb-change-streams.md).

O Apache Cassandra nativo fornece captura de dados de alterações (CDC), um mecanismo para sinalizar tabelas específicas para arquivamento, bem como rejeitar gravações para essas tabelas assim que um tamanho configurável no disco para o log CDC for atingido. O recurso do feed de alterações na API do Azure Cosmos DB para Cassandra aprimora a capacidade de consultar as alterações com o predicado por meio de CQL. Para saber mais sobre os detalhes de implementação, consulte [Feed de alterações na API do Azure Cosmos DB para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Biblioteca do processador do feed usando a alteração](change-feed-processor.md)
