---
title: Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB
description: Use o suporte ao feed de alterações do Azure Cosmos DB para controlar as alterações nos documentos e executar o processamento baseado em eventos como gatilhos e manter os caches e sistemas de análise atualizados.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 40caea40637c57aedb6315ff6fc032898ff07af7
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467940"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Feed de alterações no Azure Cosmos DB – visão geral

O suporte para feed de alterações no Azure Cosmos DB funciona ouvindo um contêiner do Azure Cosmos DB para quaisquer alterações. Ele gera a lista classificada de documentos que foram alterados na ordem em que eles foram modificados. As alterações são mantidas, podem ser processadas de maneira assíncrona e incremental e a saída pode ser distribuída em um ou mais consumidores para processamento paralelo. 

O Azure Cosmos DB é adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é usar as alterações nos dados para disparar ações adicionais. Os exemplos de ações adicionais incluem:

* Disparar uma notificação ou uma chamada a uma API, quando um item é inserido ou atualizado.
* Processamento de fluxo em tempo real para IoT ou processamento de análise em tempo real em dados operacionais.
* Movimentação de dados adicional por meio da sincronização com um cache, com um mecanismo de pesquisa ou com um data warehouse ou do arquivamento de dados para armazenamento frio.

O feed de alterações no Azure Cosmos DB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões, conforme mostra a imagem a seguir:

![Usando o feed de alterações do Azure Cosmos DB para capacitar a análise em tempo real e cenários de computação orientada a eventos](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>SDKs do cliente e APIs com suporte

No momento, o recurso é compatível com os seguintes SDKs do cliente e APIs do Azure Cosmos DB.

| **Drivers do cliente** | **CLI do Azure** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | N/A | Sim | Não | Não | Sim | Não |
|Java|N/A|Sim|Não|Não|Sim|Não|
|Python|N/A|Sim|Não|Não|Sim|Não|
|Nó/JS|N/A|Sim|Não|Não|Sim|Não|

## <a name="change-feed-and-different-operations"></a>Feed de alterações e operações diferentes

Hoje você pode ver todas as operações no feed de alterações. A funcionalidade em que é possível controlar o feed de alterações, para operações específicas como apenas atualizações e não inserções ainda não está disponível. É possível adicionar um "marcador suave" no item para atualizações e filtrar com base no nisso ao processar itens no feed de alterações. No momento, o feed de alterações não registra exclusões. Semelhante ao exemplo anterior, é possível adicionar um marcador suave nos itens que estão sendo excluídos, por exemplo, é possível adicionar um atributo no item chamado "excluído" e defini-lo como "true" e definir uma TTL no item para que ele possa ser excluído automaticamente. É possível ler itens de histórico no feed de alterações, por exemplo, itens que foram adicionados há cinco anos. Se o item não for excluído, será possível ler o feed de alterações no que diz respeito à origem do seu contêiner.

### <a name="sort-order-of-items-in-change-feed"></a>Ordem de classificação de itens no feed de alterações

Os itens do feed de alterações são organizados na ordem da hora de modificação. Essa ordem de classificação é garantida por chave de partição lógica.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed de alterações em contas do Azure Cosmos em várias regiões

Em uma conta do Azure Cosmos em várias regiões, se uma região de gravação fizer failover, o feed de alterações funcionará em toda a operação de failover manual e será contíguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed de alterações e TTL (vida útil)

Se uma propriedade TTL (vida útil) for definida em um item como -1, o feed de alterações será mantido para sempre. Se os dados não forem excluídos, continuarão no feed de alterações.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>Feed de alterações e _etag, _lsn ou _ts

O formato de _etag é interno e você não deve assumir uma dependência dele, porque ele pode ser alterado a qualquer momento. _ts é um carimbo de data/hora de modificação ou de criação. Você pode usar o _ts para comparação cronológica. _lsn é uma ID de lote que é adicionada somente para o feed de alterações; representa a ID da transação. Muitos itens podem ter a mesma _lsn. O ETag no FeedResponse é diferente do _etag que você vê no item. O _etag é um identificador interno e usado para controle de simultaneidade; ele informa sobre a versão do item, enquanto o ETag é usado para o sequenciamento do feed.

## <a name="change-feed-use-cases-and-scenarios"></a>Casos de uso e cenários do feed de alterações

O feed de alterações permite o processamento eficiente de grandes conjuntos de dados com um alto volume de gravações. O feed de alterações também oferece uma alternativa para consultar todo um conjunto de dados para identificar o que foi alterado.

### <a name="use-cases"></a>Casos de uso

Por exemplo, com o feed de alterações, é possível executar as seguintes tarefas com eficiência:

* Atualizar um cache, um índice de pesquisa ou um data warehouse com os dados armazenados no Azure Cosmos DB.

* Implementar uma camada de dados e arquivamento do nível do aplicativo, por exemplo, armazenar "dados quentes" no Azure Cosmos DB e arquivar dados frios em outros sistemas de armazenamento, por exemplo, [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md).

* Realizar migrações com tempo de inatividade zero para outra conta ou para outro contêiner do Azure Cosmos com uma chave de partição lógica diferente.

* Implementar uma [arquitetura lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) usando o Azure Cosmos DB, em que o Azure Cosmos DB dá suporte a camadas de serviço de consulta e de lote em tempo real, permitindo, assim, uma arquitetura lambda com baixo TCO.

* Receber e armazenar dados de evento de dispositivos, sensores, infraestrutura e aplicativos, além de processar esses eventos em tempo real, por exemplo, usando o [Spark](../hdinsight/spark/apache-spark-overview.md).  A imagem a seguir mostra como é possível implementar a arquitetura lambda usando o Azure Cosmos DB por meio do feed de alterações:

![Pipeline lambda baseado no Azure Cosmos DB para ingestão e consulta](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Cenários

Veja a seguir alguns dos cenários que você pode implementar com o feed de alterações facilmente:

* Em aplicativos Web e móveis [sem servidor](https://azure.microsoft.com/solutions/serverless/), é possível acompanhar eventos como todas as alterações no perfil, nas preferências ou na localização do cliente e disparar determinadas ações, como enviar notificações por push para seus dispositivos usando o [Azure Functions](change-feed-functions.md).

* Se você estiver usando o Azure Cosmos DB para criar um jogo, poderá, por exemplo, usar o feed de alterações para implementar placares em tempo real de acordo com as pontuações dos jogos concluídos.


## <a name="working-with-change-feed"></a>Trabalhando com o feed de alterações

É possível trabalhar com o feed de alterações usando as seguintes opções:

* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Usando o feed de alterações com o processador do feed de alterações](change-feed-processor.md) 

O feed de alterações está disponível para cada chave de partição lógica dentro do contêiner e pode ser distribuído entre um ou mais clientes para processamento paralelo, conforme mostrado na imagem abaixo.

![Processamento distribuído do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Recursos do feed de alterações

* O feed de alterações é habilitado por padrão para todas as contas do Azure Cosmos.

* É possível usar a [taxa de transferência provisionada](request-units.md) para ler o feed de alterações, assim como qualquer outra operação do Azure Cosmos DB, em qualquer uma das regiões associadas ao banco de dados do Azure Cosmos.

* O feed de alterações inclui inserções e operações de atualização feitas em itens dentro do contêiner. É possível capturar exclusões definindo um sinalizador de "exclusão suave" dentro dos seus itens (por exemplo, documentos) no lugar de exclusões. Como alternativa, é possível definir um período de expiração finito para seus itens com a [funcionalidade TTL](time-to-live.md). Por exemplo, 24 horas e use o valor dessa propriedade para a captura ser excluída. Com essa solução, é necessário processar alterações em um intervalo de tempo menor do que o período de expiração da TTL. 

* Cada alteração em um item aparece exatamente uma vez no feed de alterações, sendo que os clientes devem gerenciar a lógica do ponto de verificação. Se você quiser evitar a complexidade do gerenciamento de pontos de verificação, o processador do feed de alterações fornecerá o ponto de verificação automático e a semântica "pelo menos uma vez". Consulte [usar o feed de alterações com o processador do feed de alterações](change-feed-processor.md).

* Somente a alteração mais recente para um determinado item está incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.

* O feed de alterações é classificado por ordem de modificação em cada valor de chave de partição lógica. Não há nenhuma garantia de ordem entre os valores de chave de partição.

* As alterações podem ser sincronizadas de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual haja alterações disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógica de um contêiner do Azure Cosmos. Essa funcionalidade permite que alterações de grandes contêineres sejam processadas em paralelo por vários consumidores.

* Os aplicativos podem solicitar vários feeds de alteração no mesmo contêiner simultaneamente. ChangeFeedOptions.StartTime pode ser usado para fornecer um ponto de partida. Por exemplo, para localizar o token de continuação correspondente a uma determinada hora. O ContinuationToken, se especificado, supera os valores de StartTime e StartFromBeginning. A precisão de ChangeFeedOptions.StartTime é de aproximadamente 5 segundos. 

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para saber mais sobre o feed de alterações nos seguintes artigos:

* [Opções de ler o feed de alterações](read-change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Usando o processador do feed de alterações](change-feed-processor.md)
