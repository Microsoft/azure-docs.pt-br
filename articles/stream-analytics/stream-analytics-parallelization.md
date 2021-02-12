---
title: Use a paralelização de consultas e dimensionamento no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics configurando partições de entrada, ajustando a definição da consulta e definindo unidades de streaming de trabalho.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9149413d070bbb5eb8d0f8d0c99fe5ff705bcefb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012318"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Aproveitar a paralelização de consultas no Azure Stream Analytics
Este artigo mostra como tirar proveito da paralelização no Azure Stream Analytics. Aprenda a dimensionar trabalhos do Stream Analytics configurando partições de entrada e ajustando a definição da consulta de análise.
Como pré-requisito, convém estar familiarizado com a noção de Unidade de Streaming descrita em [Compreender e ajustar as Unidades de Streaming](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de um trabalho do Stream Analytics?
Uma definição de trabalho do Stream Analytics inclui pelo menos uma entrada, uma consulta e uma saída de streaming. As entradas são de onde o trabalho lê o fluxo de dados. A consulta é usada para transformar o fluxo de entrada de dados e a saída é para onde são enviados os resultados do trabalho.

## <a name="partitions-in-inputs-and-outputs"></a>Partições em entradas e saídas
Particionamento permite que você divida dados em subconjuntos com base em uma [chave de partição](../event-hubs/event-hubs-scalability.md#partitions). Se a entrada (por exemplo, Hubs de Eventos) for particionada por uma chave, recomendamos especificar essa chave de partição ao adicionar entrada ao seu trabalho do Stream Analytics. O dimensionamento de um trabalho do Stream Analytics utiliza partições na entrada e saída. Um trabalho do Stream Analytics pode consumir e gravar diferentes partições em paralelo, o que aumenta a taxa de transferência. 

### <a name="inputs"></a>Entradas
Todas as entradas do Azure Stream Analytics podem tirar proveito do particionamento:
-   EventHub (será preciso definir a chave de partição explicitamente com a palavra-chave PARTITION BY se estiver usando o nível de compatibilidade 1.1 ou inferior)
-   Hub IoT (será preciso definir a chave de partição explicitamente com a palavra-chave PARTITION BY se estiver usando o nível de compatibilidade 1.1 ou inferior)
-   Armazenamento de blob

### <a name="outputs"></a>outputs

Quando você trabalha com o Stream Analytics, você pode tirar proveito do particionamento nas saídas:
-   Armazenamento do Azure Data Lake
-   Funções do Azure
-   tabela do Azure
-   Armazenamento de Blob (é possível definir a chave de partição explicitamente)
-   Azure Cosmos DB (é preciso definir a chave de partição explicitamente)
-   Hubs de Eventos (é preciso definir a chave de partição explicitamente)
-   Hub IoT (é preciso definir a chave de partição explicitamente)
-   Barramento de Serviço
- SQL e Azure Synapse Analytics com particionamento opcional: consulte mais informações na [página saída para o banco de dados SQL do Azure](./stream-analytics-sql-output-perf.md).

O Power BI não dá suporte ao particionamento. No entanto, você ainda pode particionar a entrada, conforme descrito [nesta seção](#multi-step-query-with-different-partition-by-values) 

Para obter mais informações sobre partições, consulte os seguintes artigos:

* [Visão geral dos recursos de Hubs de Eventos](../event-hubs/event-hubs-features.md#partitions)
* [Particionamento de dados](/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Trabalhos embaraçosamente paralelos
Um trabalho *embaraçosamente paralelo* é o cenário mais escalonável no Azure Stream Analytics. Ele conecta uma partição da entrada para uma instância da consulta a uma partição da saída. Este paralelismo tem os seguintes requisitos:

1. Se sua lógica de consulta for dependente da mesma chave que está sendo processada pela mesma instância de consulta, você deverá garantir que os eventos sejam encaminhados para a mesma partição da entrada. Para Hubs de Eventos ou o Hub IoT, isso significa que os dados do evento devem ter o conjunto de valores **PartitionKey**. Como alternativa, você pode usar os remetentes particionados. Para armazenamento de Blobs, isso significa que os eventos são enviados à mesma pasta de partição. Um exemplo seria uma instância de consulta que agrega dados por userID em que o hub de eventos de entrada é particionado usando userID como chave de partição. Contudo, se a lógica de consulta não exigir que a mesma chave seja processada pela mesma instância de consulta, você poderá ignorar esse requisito. Um exemplo disso seria uma consulta simples de seleção/projeto/filtro.  

2. A próxima etapa é fazer com que sua consulta seja particionada. Para trabalhos com nível de compatibilidade 1.2 ou mais recente (recomendado), a coluna personalizada pode ser especificada como Chave de Partição nas configurações de entrada e o trabalho será paralelizado automaticamente. Trabalhos com nível de compatibilidade 1.0 ou 1.1 exigem que você use **PARTITION BY PartitionId** em todas as etapas da sua consulta. Várias etapas são permitidas, mas todas elas devem ser particionadas pela mesma chave. 

3. A maioria das saídas com suporte no Stream Analytics pode utilizar o particionamento. Se você usar um tipo de saída que não dá suporte ao particionamento, seu trabalho não será *embaraçosamente paralelo*. Para saídas do Hub de Eventos, verifique se a **Coluna de chave de partição** está definida como a mesma chave de partição usada na consulta. Consulte a [seção de saída](#outputs) para obter mais detalhes.

4. O número de partições de entrada deve ser igual ao número de partições de saída. A saída do Armazenamento de Blob pode dar suporte a partições, e herda o esquema de particionamento da consulta de upstream. Quando uma chave de partição do Armazenamento de Blob for especificada, os dados serão particionados por partição de entrada, portanto, o resultado ainda será totalmente paralelo. Exemplos de valores de partição que permitem um trabalho totalmente paralelo:

   * 8 partições de entrada de Hubs de Eventos e 8 partições de saída de Hubs de Eventos
   * 8 partições de entrada de Hubs de Eventos e Saída de armazenamento de Blobs
   * Oito partições de entrada do hub de eventos e a saída do armazenamento de blob particionadas por um campo personalizado com cardinalidade aleatória
   * 8 partições de entrada de armazenamento de Blobs e Saída de armazenamento de Blobs
   * 8 partições de entrada de armazenamento de Blobs e 8 partições de saída de Hubs de Eventos

As seções a seguir discutem alguns cenários de exemplo que são embaraçosamente paralelos.

### <a name="simple-query"></a>Consulta simples

* Entrada: Hub de eventos com 8 partições
* Saída: O hub de eventos com 8 partições ("Coluna de chave de partição" deve ser definida para usar "PartitionId")

Consulta:

```SQL
    --Using compatibility level 1.2 or above
    SELECT TollBoothId
    FROM Input1
    WHERE TollBoothId > 100
    
    --Using compatibility level 1.0 or 1.1
    SELECT TollBoothId
    FROM Input1 PARTITION BY PartitionId
    WHERE TollBoothId > 100
```

Essa consulta é um filtro simples. Portanto, nós não precisamos se preocupar sobre particionamento da entrada que está sendo enviada para o hub de eventos. Observe que os trabalhos com nível de compatibilidade anterior a 1.2 devem incluir a cláusula **PARTITION BY PartitionId**, portanto ele atende ao requisito nº 2 de anteriormente. Para a saída, é necessário configurar a saída de Hubs de Eventos no trabalho para ter a chave de partição definida como **PartitionId**. Uma última verificação é certificar-se de que o número de partições de entrada é igual ao número de partições de saída.

### <a name="query-with-a-grouping-key"></a>Consulta com chave de agrupamento

* Entrada: Hub de eventos com 8 partições
* Saída: Armazenamento de blob

Consulta:

```SQL
    --Using compatibility level 1.2 or above
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
    --Using compatibility level 1.0 or 1.1
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Esta consulta tem uma chave de agrupamento. Portanto, os eventos agrupados devem ser enviados para a mesma partição do Hub de Eventos. Como, neste exemplo, agrupamos por TollBoothID, devemos ter certeza de que TollBoothID é usado como a chave de partição quando os eventos são enviados ao Hub de Eventos. Em seguida, no ASA, podemos usar **PARTITION BY PartitionId** para herdar deste esquema de partição e habilitar a paralelização completa. Como a saída é o armazenamento de Blobs, não precisamos nos preocupar sobre como configurar um valor de chave de partição, de acordo com o requisito #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Cenários de exemplo que *não* são embaraçosamente paralelos

Na seção anterior, mostramos alguns cenários embaraçosamente paralelos. Nesta seção, vamos discutir os cenários que não atendem a todos os requisitos para serem embaraçosamente paralelos. 

### <a name="mismatched-partition-count"></a>Contagem de partições incompatível
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 32 partições

Se a contagem de partição de entrada não corresponder à contagem de partição de saída, a topologia não será embaraçosamente paralela, independentemente da consulta. No entanto, ainda podemos obter algum nível ou paralelização.

### <a name="query-using-non-partitioned-output"></a>Consultar usando a saída não particionada
* Entrada: Hub de eventos com 8 partições
* Saída: Power BI

Atualmente, a saída do Power BI não suporta particionamento. Portanto, esse cenário não é embaraçosamente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de várias etapas com diferentes valores de PARTITION BY
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições
* Nível de compatibilidade: 1.0 ou 1.1

Consulta:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Como você pode ver, a segunda etapa usa **TollBoothId** como a chave de particionamento. Esta etapa não é igual à primeira etapa e, portanto, exige que façamos um embaralhamento. 

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de várias etapas com diferentes valores de PARTITION BY
* Entrada: Hub de eventos com 8 partições
* Saída: O hub de eventos com 8 partições ("Coluna de chave de partição" deve ser definida para usar "TollBoothId")
* Nível de compatibilidade – 1.2 ou superior

Consulta:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

o nível de compatibilidade 1.2 ou superior permite a execução de consulta paralela por padrão. Por exemplo, a consulta da seção anterior será particionada, contanto que a coluna “TollBoothId” seja definida como a Chave de Partição de entrada. A cláusula PARTITION BY PartitionId não é necessária.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcule o máximo de unidades de um trabalho de streaming
O número total de unidades de streaming que pode ser usado por um trabalho de Análise de fluxo depende do número de etapas da consulta definida para o trabalho e o número de partições para cada etapa.

### <a name="steps-in-a-query"></a>Etapas de uma consulta
Uma consulta pode ter uma ou mais etapas. Cada etapa é uma subconsulta definida usando a palavra-chave **WITH**. A única consulta que está fora da palavra-chave **WITH** também é contada como uma etapa. Por exemplo, a instrução **SELECT** na consulta a seguir:

Consulta:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Esta consulta tem duas etapas.

> [!NOTE]
> Esta consulta é discutida em mais detalhes mais adiante neste artigo.
>  

### <a name="partition-a-step"></a>Uma etapa de partição
Particionamento de uma etapa exige as seguintes condições:

* A fonte de entrada deve ser particionada. 
* A instrução **SELECT** da consulta deve ser lida de uma origem de entrada particionada.
* A consulta dentro da etapa deve ter a palavra-chave **PARTITION BY**.

Quando uma consulta for particionada, os eventos de entrada serão processados e agregados em diferentes grupos de partição e saídas de eventos são geradas para cada um dos grupos. Se você quiser uma agregação combinada, crie uma segunda etapa não particionada para agregação.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcule o máximo de unidades de um trabalho de streaming
Todas as etapas não particionadas juntas podem escalar verticalmente até seis unidades de Streaming (SUs) para um trabalho de Stream Analytics. Além disso, você pode adicionar 6 SUs para cada partição em uma etapa particionada.
Veja alguns **exemplos** na tabela a seguir.

| Consulta                                               | Máxima quantidade de SUs para o trabalho |
| --------------------------------------------------- | ------------------- |
| <ul><li>A consulta contém uma única etapa.</li><li>A etapa não está particionada.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é particionado por 16.</li><li>A consulta contém uma única etapa.</li><li>A etapa é particionada.</li></ul> | 96 partições (6 * 16 partições) |
| <ul><li>A consulta contém duas etapas.</li><li>Nenhuma das etapas é particionada.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é particionado por 3.</li><li>A consulta contém duas etapas. A etapa de entrada é particionada e a segunda etapa não é.</li><li>A instrução <strong>SELECT</strong> lê da entrada particionada.</li></ul> | 24 (18 para as etapas particionadas + 6 para as etapas não particionadas) |

### <a name="examples-of-scaling"></a>Exemplos de escala

A consulta a seguir calcula o número de carros passando por um pedágio que tem três pedágios dentro de uma janela de três minutos. Essa consulta pode ser escalada verticalmente para até seis unidades de streaming.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Para usar mais unidades de streaming para a consulta, tanto o fluxo de dados de entrada quanto a consulta devem ser particionados. Dada a partição do fluxo de dados definida como 3, a seguinte consulta modificada pode ser escalada verticalmente para até 18 unidades de streaming:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Quando uma consulta é particionada, os eventos de entrada são processados e agregados em grupos de partições separados. Eventos de saída também são gerados para cada um dos grupos. O particionamento pode causar alguns resultados inesperados quando o campo **GROUP BY** não for a chave da partição no fluxo de dados de entrada. Por exemplo, o campo **TollBoothId** na consulta de exemplo anterior não é a chave de partição de **Input1**. O resultado é que os dados do Pedágio #1 podem ser distribuídos em várias partições.

Cada uma das partições **Entrada1** serão processadas separadamente pelo Stream Analytics. Como resultado, vários registros da contagem de carros para o mesmo pedágio na mesma janela em cascata serão criados. Se a chave de partição de entrada não puder ser alterada, esse problema poderá ser corrigido pela adição de uma etapa sem partição a fim de agregar valores entre partições, como no exemplo a seguir:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Esta consulta pode ser escalada verticalmente para até 24 SUs.

> [!NOTE]
> Se você estiver unindo dois fluxos, certifique-se de eles sejam particionados por chave de partição da coluna que você usa para criar as junções. Além disso, certifique-se de que você tem o mesmo número de partições em ambos os fluxos.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Alcançar maior taxa de transferência em escala

Um trabalho [embaraçosamente paralelo](#embarrassingly-parallel-jobs) é necessário, mas não é suficiente para sustentar uma maior taxa de transferência em escala. Cada sistema de armazenamento e a saída do Stream Analytics correspondente têm variações sobre como obter a melhor taxa de transferência de gravação possível. Assim como acontece com qualquer cenário em escala, há alguns desafios que podem ser resolvidos usando as configurações corretas. Esta seção aborda as configurações para algumas saídas comuns e fornece exemplos para manter as taxas de ingestão de 1 mil, 5 mil e 10 mil eventos por segundo.

As observações a seguir usam um trabalho do Stream Analytics com consulta sem estado (passagem), um UDF JavaScript básico que grava no Hub de Eventos, no BD SQL do Azure ou no Cosmos DB.

#### <a name="event-hub"></a>Hub de evento

|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de Saída  |
|--------|---------|---------|
| 1 mil     |    1    |  2 TU   |
| 5 mil     |    6    |  6 TU   |
| 10 mil    |    12   |  10 TU  |

A solução do [Hub de Eventos](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-eventhubs) é dimensionada linearmente em termos de SU (unidades de streaming) e taxa de transferência, fazendo dela a maneira mais eficiente e de alto desempenho de analisar e transmitir dados do Stream Analytics. Os trabalhos podem ser escalados verticalmente para até 192 SU, o que significa, aproximadamente, o processamento de até 200 MB/s ou 19 trilhões de eventos por dia.

#### <a name="azure-sql"></a>SQL do Azure
|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de Saída  |
|---------|------|-------|
|    1 mil   |   3  |  S3   |
|    5 mil   |   18 |  P4   |
|    10 mil  |   36 |  P6   |

O [Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-azuresql) dá suporte à gravação em paralelo, chamada Herdar Particionamento, mas não é habilitado por padrão. No entanto, habilitar Herdar Particionamento, juntamente com uma consulta totalmente paralela, pode não ser suficiente para obter taxas de transferência maiores. As taxas de transferência de gravação do SQL dependem significativamente da configuração do banco de dados e do esquema da tabela. O artigo [Desempenho de Saída do SQL](./stream-analytics-sql-output-perf.md) tem mais detalhes sobre os parâmetros que podem maximizar a taxa de transferência de gravação. Conforme observado no artigo [Saída do Azure Stream Analytics para o Banco de Dados SQL do Azure](./stream-analytics-sql-output-perf.md#azure-stream-analytics), essa solução não é dimensionada linearmente como um pipeline totalmente paralelo além de 8 partições e pode precisar ser reparticionada antes da saída SQL (confira [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Os SKUs Premium são necessários para sustentar altas taxas de E/S juntamente com a sobrecarga dos backups de log de transações ocorrendo a cada poucos minutos.

#### <a name="cosmos-db"></a>Cosmos DB
|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de Saída  |
|-------|-------|---------|
|  1 mil   |  3    | 20 mil RU  |
|  5 mil   |  24   | 60 mil RU  |
|  10 mil  |  48   | 120 mil RU |

A saída do [Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb) do Stream Analytics foi atualizada para usar a integração nativa no [nível de compatibilidade 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). O nível de compatibilidade 1.2 permite uma taxa de transferência significativamente maior e reduz o consumo de RU em comparação com o 1.1, que é o nível de compatibilidade padrão para novos trabalhos. A solução usa contêineres do CosmosDB particionados em /deviceId e o restante da solução é configurado de maneira idêntica.

Todos os [exemplos de streaming em escala do Azure](https://github.com/Azure-Samples/streaming-at-scale) usam um hub de eventos como entrada que é alimentada pelos clientes de teste de simulação de carga. Cada evento de entrada é um documento JSON de 1 KB, que converte as taxas de ingestão configuradas em taxas de transferência (1 MB/s, 5 MB/s e 10 MB/s) facilmente. Os eventos simulam um dispositivo IoT que envia os seguintes dados JSON (em uma forma reduzida) para dispositivos de até 1 mil:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> As configurações estão sujeitas a alteração devido aos vários componentes usados na solução. Para obter uma estimativa mais precisa, personalize os exemplos para se ajustarem ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificar gargalos

Use o painel Métricas em seu trabalho do Azure Stream Analytics para identificar gargalos em seu pipeline. Examine **Eventos de Entrada/Saída** para taxa de transferência e ["Atraso de Marca-d'água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **Eventos Acumulados** para ver se o trabalho está acompanhando a taxa de entrada. Para as métricas do Hub de Eventos, procure **Solicitações Limitadas** e ajuste as Unidades de Limite adequadamente. Para métricas do Cosmos DB, examine **Máximo de RU/s consumidas por intervalo de chaves de partição** em Taxa de Transferência para verificar se os intervalos de chave de partição foram consumidos uniformemente. Para o BD SQL do Azure, monitore a **E/S de Log** e **CPU**.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente a nossa [página de Perguntas e respostas da Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/