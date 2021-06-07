---
title: Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB
description: Use o suporte ao feed de alterações do Azure Cosmos DB para rastrear as alterações nos documentos, executar o processamento baseado em eventos como gatilhos e manter caches e sistemas de análise atualizados
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: c6856a0cb70123f1a3570b611c81660a592fdc1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027751"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Feed de alterações no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

O feed de alterações no Azure Cosmos DB é um registro persistente de alterações em um contêiner na ordem em que ocorrem. O suporte para feed de alterações no Azure Cosmos DB funciona ouvindo um contêiner do Azure Cosmos DB para quaisquer alterações. Ele gera a lista classificada de documentos que foram alterados na ordem em que eles foram modificados. As alterações persistentes podem ser processadas de forma assíncrona e incremental, e a saída pode ser distribuída por um ou mais consumidores para processamento paralelo.

Saiba mais sobre [padrões de design do feed de alterações](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>SDKs do cliente e APIs com suporte

No momento, o recurso é compatível com os seguintes SDKs do cliente e APIs do Azure Cosmos DB.

| **Drivers do cliente** | **API do SQL** | **API do Azure Cosmos DB para Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Sim | Sim | Sim | Sim | Não |
|Java|Sim|Sim|Sim|Sim|Não|
|Python|Sim|Sim|Sim|Sim|Não|
|Nó/JS|Sim|Sim|Sim|Sim|Não|

## <a name="change-feed-and-different-operations"></a>Feed de alterações e operações diferentes

Hoje você vê todas as inserções e atualizações no feed de alterações. Não é possível filtrar o feed de alterações para um tipo específico de operação. Uma alternativa possível é adicionar um “marcador flexível” ao item para atualizações e filtrar com base nisso ao processar itens no feed de alterações.

No momento, o feed de alterações não registra exclusões. Semelhante ao exemplo anterior, você pode adicionar um marcador flexível nos itens que estão sendo excluídos. Por exemplo, você pode adicionar um atributo no item chamado “deleted” e configurá-lo como “true” e definir um TTL no item, para que ele possa ser excluído automaticamente. Você pode ler o feed de alterações para itens históricos (a alteração mais recente correspondente ao item, não inclui as alterações intermediárias); por exemplo, itens que foram adicionados cinco anos atrás. Você pode ler o feed de alterações desde a origem do seu contêiner, mas se um item for excluído, ele será removido do feed de alterações.

### <a name="sort-order-of-items-in-change-feed"></a>Ordem de classificação de itens no feed de alterações

Os itens do feed de alterações são organizados na ordem da hora de modificação. Essa ordem de classificação é garantida por chave de partição lógica.

### <a name="consistency-level"></a>Nível de consistência

Ao consumir o feed de alterações em um nível de consistência eventual, pode haver eventos duplicados entre as operações de leitura do feed de alterações subsequentes (o último evento de uma operação de leitura aparece como o primeiro da próxima).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed de alterações em contas do Azure Cosmos em várias regiões

Em uma conta do Azure Cosmos em várias regiões, se uma região de gravação fizer failover, o feed de alterações funcionará em toda a operação de failover manual e será contíguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed de alterações e TTL (vida útil)

Se uma propriedade TTL (vida útil) for definida em um item como -1, o feed de alterações será mantido para sempre. Se os dados não forem excluídos, continuarão no feed de alterações.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed de alterações e _etag, _lsn ou _ts

O formato de _etag é interno e você não deve assumir uma dependência dele, porque ele pode ser alterado a qualquer momento. _ts é um carimbo de data/hora de modificação ou de criação. Você pode usar o _ts para comparação cronológica. _lsn é uma ID de lote que é adicionada apenas para o feed de alterações; ela representa a ID da transação. Muitos itens podem ter a mesma _lsn. O ETag no FeedResponse é diferente do _etag que você vê no item. _etag é um identificador interno e é usado para controle de simultaneidade. A propriedade _etag diz sobre a versão do item, enquanto a propriedade ETag é usada para sequenciar o feed.

## <a name="working-with-change-feed"></a>Trabalhando com o feed de alterações

É possível trabalhar com o feed de alterações usando as seguintes opções:

* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Usando o feed de alterações com o processador do feed de alterações](change-feed-processor.md) 

O feed de alterações está disponível para cada chave de partição lógica dentro do contêiner e pode ser distribuído entre um ou mais clientes para processamento paralelo, conforme mostrado na imagem abaixo.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Processamento distribuído do feed de alterações do Azure Cosmos DB" border="false":::

## <a name="features-of-change-feed"></a>Recursos do feed de alterações

* O feed de alterações é habilitado por padrão para todas as contas do Azure Cosmos.

* É possível usar a [taxa de transferência provisionada](request-units.md) para ler o feed de alterações, assim como qualquer outra operação do Azure Cosmos DB, em qualquer uma das regiões associadas ao banco de dados do Azure Cosmos.

* O feed de alterações inclui inserções e operações de atualização feitas em itens dentro do contêiner. É possível capturar exclusões definindo um sinalizador de "exclusão suave" dentro dos seus itens (por exemplo, documentos) no lugar de exclusões. Como alternativa, é possível definir um período de expiração finito para seus itens com a [funcionalidade TTL](time-to-live.md). Por exemplo, 24 horas e use o valor dessa propriedade para a captura ser excluída. Com essa solução, é necessário processar alterações em um intervalo de tempo menor do que o período de expiração da TTL.

* Cada alteração em um item aparece exatamente uma vez no feed de alterações, sendo que os clientes devem gerenciar a lógica do ponto de verificação. Se desejar evitar a complexidade de gerenciar pontos de verificação, o processador do feed de alterações fornecerá um ponto de verificação automático e semântica "pelo menos uma vez". Consulte [usando o feed de alterações com o processador do feed de alterações](change-feed-processor.md).

* Somente a alteração mais recente para um determinado item está incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.

* O feed de alterações é classificado por ordem de modificação em cada valor de chave de partição lógica. Não há nenhuma garantia de ordem entre os valores de chave de partição.

* As alterações podem ser sincronizadas de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual haja alterações disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógica de um contêiner do Azure Cosmos. Essa funcionalidade permite que alterações de grandes contêineres sejam processadas em paralelo por vários consumidores.

* Os aplicativos podem solicitar vários feeds de alterações no mesmo contêiner simultaneamente. ChangeFeedOptions.StartTime pode ser usado para fornecer um ponto de partida. Por exemplo, para localizar o token de continuação correspondente a uma determinada hora. O ContinuationToken, se especificado, tem precedência sobre os valores de StartTime e StartFromBeginning. A precisão de ChangeFeedOptions.StartTime é de aproximadamente 5 segundos.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed de alterações em APIs para Cassandra e MongoDB

A funcionalidade do feed de alterações é apresentada como fluxo de alteração na API do MongoDB e consulta com predicado em API do Cassandra. Para saber mais sobre os detalhes de implementação para a API do MongoDB, consulte [Fluxos de alteração na API do Azure Cosmos DB para o MongoDB](mongodb-change-streams.md).

O Apache Cassandra nativo fornece captura de dados de alterações (CDC), um mecanismo para sinalizar tabelas específicas para arquivamento, bem como rejeitar gravações para essas tabelas assim que um tamanho configurável no disco para o log CDC for atingido. O recurso do feed de alterações na API do Azure Cosmos DB para Cassandra aprimora a capacidade de consultar as alterações com o predicado por meio de CQL. Para saber mais sobre os detalhes de implementação, consulte [Feed de alterações na API do Azure Cosmos DB para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para saber mais sobre o feed de alterações nos seguintes artigos:

* [Opções de ler o feed de alterações](read-change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Usando o processador de feed de alterações](change-feed-processor.md)
