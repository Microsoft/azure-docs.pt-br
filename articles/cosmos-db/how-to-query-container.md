---
title: Consultar contêineres no Azure Cosmos DB
description: Saiba como consultar contêineres em Azure Cosmos DB usando consultas em partição e entre partições
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 0f08ca84597b08b9a236b7bfb0fc9c849423a752
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335884"
---
# <a name="query-an-azure-cosmos-container"></a>Consultar um contêiner do Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como consultar um contêiner (coleção, grafo ou tabela) no Azure Cosmos DB. Em particular, ele aborda como as consultas em partição e entre partições funcionam em Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta na partição

Quando você consulta dados de contêineres, se a consulta tiver um filtro de chave de partição especificado, Azure Cosmos DB otimizará automaticamente a consulta. Ele roteia a consulta para as [partições físicas](partitioning-overview.md#physical-partitions) correspondentes aos valores de chave de partição especificados no filtro.

Por exemplo, considere a consulta abaixo com um filtro de igualdade ativado `DeviceId` . Se executarmos essa consulta em um contêiner particionado `DeviceId` , essa consulta será filtrada para uma única partição física.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Assim como no exemplo anterior, essa consulta também filtrará uma única partição. A adição do filtro adicional em não `Location` altera isso:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Aqui está uma consulta que tem um filtro de intervalo na chave de partição e não será delimitada para uma única partição física. Para ser uma consulta na partição, a consulta deve ter um filtro de igualdade que inclua a chave de partição:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Consulta entre partições

A consulta a seguir não tem um filtro na chave de partição ( `DeviceId` ). Portanto, ele deve fazer o Fan-out para todas as partições físicas onde ele é executado no índice de cada partição:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Cada partição física tem seu próprio índice. Portanto, quando você executa uma consulta entre partições em um contêiner, você está efetivamente executando uma consulta *por* partição física. Azure Cosmos DB agregará resultados automaticamente em diferentes partições físicas.

Os índices em diferentes partições físicas são independentes um do outro. Não há nenhum índice global no Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Consulta entre partições em paralelo

Os SDKs do Azure Cosmos DB 1.9.0 e versões superiores dão suporte a opções de execução de consultas paralelas. Consultas entre partições em paralelo permitem que você execute consultas entre partições de baixa latência.

Você pode gerenciar a execução de consulta paralela ajustando os seguintes parâmetros:

- **MaxConcurrency**: define o número máximo de conexões de rede simultâneas com as partições do contêiner. Se você definir essa propriedade como `-1` , o SDK gerenciará o grau de paralelismo. Se  `MaxConcurrency` definido como `0` , há uma única conexão de rede para as partições do contêiner.

- **MaxBufferedItemCount**: negocia a latência da consulta em comparação com a utilização de memória do lado do cliente. Se a opção for omitida ou definida como -1, o SDK gerenciará o número de itens no buffer durante a execução de consultas paralelas.

Devido à capacidade do Azure Cosmos DB de paralelizar consultas entre partições, a latência da consulta geralmente será bem dimensionada à medida que o sistema adiciona [partições físicas](partitioning-overview.md#physical-partitions). No entanto, a carga de RU aumentará significativamente, pois o número total de partições físicas aumenta.

Ao executar uma consulta entre partições, você está essencialmente fazendo uma consulta separada por partição física individual. Embora as consultas entre partições usem o índice, se disponíveis, elas ainda não são tão eficientes quanto as consultas em partição.

## <a name="useful-example"></a>Exemplo útil

Aqui está uma analogia para entender melhor as consultas entre partições:

Vamos imaginar que você é um driver de entrega que precisa fornecer pacotes para complexos diferentes de apartamento. Cada apartamento complexo tem uma lista no local que tem todos os números de unidade residentes. Podemos comparar cada apartamento complexo com uma partição física e cada lista com o índice da partição física.

Podemos comparar as consultas na partição e entre partições usando este exemplo:

### <a name="in-partition-query"></a>Consulta na partição

Se o driver de entrega souber o apartamento complexo (partição física) correto, ele poderá imediatamente conduzir para a compilação correta. O driver pode verificar a lista complexa de apartamento dos números de unidade residentes (o índice) e entregar rapidamente os pacotes apropriados. Nesse caso, o driver não desperdiça tempo nem esforço levando a um apartamento complexo para verificar e ver se algum dos destinatários do pacote residem nele.

### <a name="cross-partition-query-fan-out"></a>Consulta entre partições (Fan-out)

Se o driver de entrega não souber o apartamento complexo (partição física) correto, eles precisarão impulsionar cada prédio de apartamento único e verificar a lista com todos os números de unidade residentes (o índice). Depois que eles chegam em cada apartamento complexo, eles ainda poderão usar a lista de endereços de cada residente. No entanto, eles precisarão verificar cada lista de todos os apartamento, independentemente de os destinatários do pacote viverem ou não. É assim que as consultas entre partições funcionam. Embora eles possam usar o índice (não é necessário vazar em todas as portas), eles devem verificar separadamente o índice para cada partição física.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Consulta entre partições (com escopo para apenas algumas partições físicas)

Se o driver de entrega souber que todos os destinatários do pacote residem em alguns poucos complexos, eles não precisarão se dirigir a cada um. Embora a condução de algumas complexidades de apartamento ainda exija mais trabalho do que visitar apenas um único edifício, o driver de entrega ainda economiza tempo e esforço significativos. Se uma consulta tiver a chave de partição em seu filtro com a `IN` palavra-chave, ela só verificará os índices de dados da partição física relevante.

## <a name="avoiding-cross-partition-queries"></a>Evitando consultas entre partições

Para a maioria dos contêineres, é inevitável que você terá algumas consultas entre partições. Ter algumas consultas entre partições é ok! Quase todas as operações de consulta têm suporte em partições (chaves de partição lógica e partições físicas). Azure Cosmos DB também tem muitas otimizações no mecanismo de consulta e nos SDKs do cliente para paralelizar a execução da consulta em partições físicas.

Para a maioria dos cenários de leitura pesada, recomendamos simplesmente selecionar a propriedade mais comum em seus filtros de consulta. Você também deve verificar se a chave de partição está em conformidade com outras [práticas recomendadas de seleção de chave de partição](partitioning-overview.md#choose-partitionkey).

Evitar consultas entre partições normalmente só é importante para grandes contêineres. É cobrado no mínimo cerca de 2,5 RU sempre que você verifica o índice de uma partição física para obter resultados, mesmo que nenhum item na partição física corresponda ao filtro da consulta. Dessa forma, se você tiver apenas uma (ou apenas algumas) partições físicas, as consultas entre partições não consumirão significativamente mais RU do que as consultas em partição.

O número de partições físicas está vinculado à quantidade de RU provisionada. Cada partição física permite até 10.000 a RU provisionada e pode armazenar até 50 GB de dados. Azure Cosmos DB gerenciará automaticamente as partições físicas para você. O número de partições físicas no contêiner depende da taxa de transferência provisionada e do armazenamento consumido.

Você deve tentar evitar consultas entre partições se sua carga de trabalho atender aos critérios abaixo:
- Você planeja ter mais de 30.000 RU provisionados
- Você planeja armazenar mais de 100 GB de dados

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre o particionamento no Azure Cosmos DB:

- [Particionamento no Azure Cosmos DB](partitioning-overview.md)
- [Chaves de partição sintética no Azure Cosmos DB](synthetic-partition-keys.md)
