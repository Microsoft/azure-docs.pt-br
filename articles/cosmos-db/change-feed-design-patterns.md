---
title: Padrões de design do feed de alterações no Azure Cosmos DB
description: Visão geral dos padrões de design comuns do feed de alterações
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334558"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Padrões de design do feed de alterações no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O feed de alterações do Azure Cosmos DB permite o processamento eficiente de grandes conjuntos de dados com um alto volume de gravações. O feed de alterações também oferece uma alternativa para consultar todo um conjunto de dados para identificar o que foi alterado. Este documentação se concentra em padrões de design comuns do feed de alterações, compensações de design e limitações do feed de alterações.

O Azure Cosmos DB é adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é usar as alterações nos dados para disparar ações adicionais. Os exemplos de ações adicionais incluem:

* Disparar uma notificação ou uma chamada a uma API, quando um item é inserido ou atualizado.
* Processamento de fluxo em tempo real para IoT ou processamento de análise em tempo real em dados operacionais.
* Movimentação de dados, como a sincronização com um cache, um mecanismo de pesquisa, um data warehouse ou armazenamento frio.

O feed de alterações no Azure Cosmos DB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões, conforme mostra a imagem a seguir:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Usando o feed de alterações do Azure Cosmos DB para capacitar a análise em tempo real e cenários de computação orientada a eventos" border="false":::

## <a name="event-computing-and-notifications"></a>Notificações e computação de eventos

O feed de alterações do Azure Cosmos DB pode simplificar os cenários que precisam disparar uma notificação ou enviar uma chamada a uma API com base em um determinado evento. Você pode usar a [Biblioteca de Processos do Feed de Alterações](change-feed-processor.md) para sondar automaticamente o contêiner em busca de alterações e chamar uma API externa sempre que houver uma gravação ou atualização.

Você também pode disparar seletivamente uma notificação ou enviar uma chamada a uma API com base em critérios específicos. Por exemplo, se você estiver lendo o feed de alterações usando o [Azure Functions](change-feed-functions.md), poderá colocar a lógica na função para enviar apenas uma notificação se um critério específico tiver sido atendido. Embora o código do Azure Functions fosse executado durante cada gravação e atualização, a notificação só seria enviada se critérios específicos tivessem sido atendidos.

## <a name="real-time-stream-processing"></a>Processamento de fluxo em tempo real

O feed de alterações do Azure Cosmos DB pode ser usado para processamento de fluxo em tempo real para IoT ou processamento de análise em tempo real em dados operacionais.
Por exemplo, você pode receber e armazenar dados de evento de dispositivos, sensores, infraestrutura e aplicativos, além de processar esses eventos em tempo real, usando o [Spark](../hdinsight/spark/apache-spark-overview.md). A seguinte imagem mostra como você pode implementar uma arquitetura lambda usando o Azure Cosmos DB por meio do feed de alterações:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Pipeline lambda baseado no Azure Cosmos DB para ingestão e consulta" border="false":::

Em muitos casos, as implementações do processamento de fluxo recebem primeiro um alto volume de dados de entrada em uma fila de mensagens temporária como o Hub de Eventos do Azure ou o Apache Kafka. O feed de alterações é uma ótima alternativa devido à capacidade do Azure Cosmos DB de dar suporte a uma alta taxa sustentada de ingestão de dados com baixa latência de leitura e gravação garantida. As vantagens do feed de alterações do Azure Cosmos DB em uma fila de mensagens incluem:

### <a name="data-persistence"></a>Persistência de dados

Os dados gravados no Azure Cosmos DB serão exibidos no feed de alterações e serão mantidos até serem excluídos. As filas de mensagens normalmente têm um período de retenção máximo. Por exemplo, o [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) oferece uma retenção de dados máxima de 90 dias.

### <a name="querying-ability"></a>Capacidade de consulta

Além de ler do feed de alterações de um contêiner do Cosmos, você também pode executar consultas SQL nos dados armazenados no Azure Cosmos DB. O feed de alterações não é uma duplicação de dados que já estão no contêiner, mas apenas um mecanismo diferente de leitura dos dados. Portanto, se você ler os dados do feed de alterações, eles sempre serão consistentes com consultas do mesmo contêiner do Azure Cosmos DB.

### <a name="high-availability"></a>Alta disponibilidade

O Azure Cosmos DB oferece até 99,999% de disponibilidade de leitura e gravação. Ao contrário de muitas filas de mensagens, os dados do Azure Cosmos DB podem ser distribuídos e configurados globalmente com facilidade com um [RTO (Objetivo de Ponto de Recuperação)](./consistency-levels.md#rto) de zero.

Depois de processar itens no feed de alterações, você pode criar uma exibição materializada e persistir valores agregados de volta no Azure Cosmos DB. Se você estiver usando o Azure Cosmos DB para criar um jogo, poderá, por exemplo, usar o feed de alterações para implementar placares em tempo real de acordo com as pontuações dos jogos concluídos.

## <a name="data-movement"></a>Movimentação de dados

Você também pode ler no feed de alterações para uma movimentação de dados em tempo real.

Por exemplo, o feed de alterações ajuda você a executar as seguintes tarefas com eficiência:

* Atualizar um cache, índice de pesquisa ou data warehouse com os dados armazenados no Azure Cosmos DB.

* Realizar migrações com tempo de inatividade zero para outra conta ou para outro contêiner do Azure Cosmos com uma chave de partição lógica diferente.

* Implementar um arquivamento e uma camada de dados no nível do aplicativo. Por exemplo, você pode armazenar "dados de acesso frequente" no Azure Cosmos DB e envelhecer "dados frios" em outros sistemas de armazenamento, como o [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md).

Quando você precisa [desnormalizar dados entre partições e contêineres](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), você pode ler o feed de alterações do seu contêiner como uma fonte para essa replicação de dados. A replicação de dados em tempo real com o feed de alterações só pode garantir a consistência eventual. Você pode [monitorar a distância com a qual o Processador do Feed de Alterações atrasa](how-to-use-change-feed-estimator.md) no processamento de alterações no contêiner do Cosmos.

## <a name="event-sourcing"></a>Fornecimento de eventos

O [padrão de fornecimento de evento](/azure/architecture/patterns/event-sourcing) envolve o uso de um armazenamento somente de acréscimo para registrar a série completa de ações nesses dados. O feed de alterações do Azure Cosmos DB é uma ótima opção como um armazenamento de dados central em arquiteturas de fornecimento de eventos em que toda a ingestão de dados é modelada como gravações (sem atualizações ou exclusões). Nesse caso, cada gravação no Azure Cosmos DB é um “evento” e você terá um registro completo de eventos passados no feed de alterações. Usos típicos dos eventos publicados pelo repositório de eventos central se destinam à manutenção de exibições materializadas ou para integração com sistemas externos. Como não há limite de tempo para a retenção no feed de alterações, você pode reproduzir todos os eventos passados lendo desde o início do feed de alterações do seu contêiner do Cosmos.

Você pode fazer [vários consumidores do feed de alterações assinarem o feed de alterações do mesmo contêiner](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Além da taxa de transferência provisionada do [contêiner de concessão](change-feed-processor.md#components-of-the-change-feed-processor), não há custo para utilizar o feed de alterações. O feed de alterações está disponível em todos os contêineres, independentemente de ser utilizado.

O Azure Cosmos DB é um ótimo armazenamento de dados central persistente somente de acréscimo no padrão de fornecimento de eventos devido aos seus pontos fortes em escalabilidade horizontal e alta disponibilidade. Além disso, a biblioteca do Processador do Feed de alterações oferece uma garantia ["pelo menos uma vez"](change-feed-processor.md#error-handling) para que você não perca o processamento de nenhum evento.

## <a name="current-limitations"></a>Limitações atuais

O feed de alterações tem limitações importantes que você deve entender. Embora os itens em um contêiner do Cosmos sempre permaneçam no feed de alterações, o feed de alterações não é um log de operação completo. Há importantes áreas a serem consideradas ao criar um aplicativo que utiliza o feed de alterações.

### <a name="intermediate-updates"></a>Atualizações intermediárias

Somente a alteração mais recente para um determinado item está incluída no feed de alterações. Ao processar alterações, você lerá a versão mais recente do item disponível. Se houver várias atualizações para o mesmo item em um curto período de tempo, será possível perder o processamento de atualizações intermediárias. Se você deseja controlar as atualizações e poder reproduzir as atualizações passadas para um item, recomendamos modelar essas atualizações como uma série de gravações.

### <a name="deletes"></a>Deletes

O feed de alterações não captura exclusões. Se você excluir um item do contêiner, ele também será removido do feed de alterações. O método mais comum de lidar com isso é adicionar um marcador flexível nos itens que estão sendo excluídos. Você pode adicionar uma propriedade chamada “deleted” e defini-la como “true” no momento da exclusão. Essa atualização de documento será exibida no feed de alterações. Você pode definir um TTL neste item para que ele possa ser excluído automaticamente mais tarde.

### <a name="guaranteed-order"></a>Ordem garantida

Há uma ordem garantida no feed de alterações dentro de um valor de chave de partição, mas não entre valores de chave de partição. Você deve selecionar uma chave de partição que ofereça uma garantia de ordem significativa.

Por exemplo, considere um aplicativo de varejo usando o padrão de design de fornecimento de eventos. Neste aplicativo, diferentes ações de usuário são "eventos" que são modelados como gravações no Azure Cosmos DB. Imagine se alguns eventos de exemplo ocorreram na seguinte sequência:

1. O cliente adiciona o item A ao carrinho de compras
2. O cliente adiciona o item B ao carrinho de compras
3. O cliente remove o item A do carrinho de compras
4. O cliente faz check-out e o conteúdo do carrinho de compras é enviado

Uma exibição materializada do conteúdo atual do carrinho de compras é mantida para cada cliente. Esse aplicativo deve verificar se esses eventos são processados na ordem em que ocorrem. Se, por exemplo, o check-out do carrinho fosse processado antes da remoção do item A, provavelmente o item A teria sido enviado, ao contrário do item B desejado. Para garantir que esses quatro eventos sejam processados na ordem da ocorrência, eles devem estar dentro do mesmo valor de chave de partição. Se você selecionar **nome de usuário** (cada cliente tem um nome de usuário exclusivo) como a chave de partição, você poderá garantir que esses eventos apareçam no feed de alterações na mesma ordem em que são gravados no Azure Cosmos DB.

## <a name="examples"></a>Exemplos

Aqui estão alguns exemplos de código do feed de alterações do mundo real que se estão fora do escopo dos exemplos fornecidos no Microsoft Docs:

- [Introdução ao feed de alterações](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Caso de uso de IoT centrado no feed de alterações](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso de uso de varejo centrado no feed de alterações](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do feed de alterações](change-feed.md)
* [Opções de ler o feed de alterações](read-change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)