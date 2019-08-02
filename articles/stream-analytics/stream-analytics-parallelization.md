---
title: Use a paralelização de consultas e dimensionamento no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics configurando partições de entrada, ajustando a definição da consulta e definindo unidades de streaming de trabalho.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 5eba5601a50640261fa1b488d959f606d4514737
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612212"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Aproveitar a paralelização de consultas no Azure Stream Analytics
Este artigo mostra como tirar proveito da paralelização no Azure Stream Analytics. Aprenda a dimensionar trabalhos do Stream Analytics configurando partições de entrada e ajustando a definição da consulta de análise.
Como pré-requisito, convém estar familiarizado com a noção de Unidade de Streaming descrita em [Compreender e ajustar as Unidades de Streaming](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de um trabalho do Stream Analytics?
Uma definição de trabalho de Stream Analytics inclui entradas, consulta e saída. As entradas são de onde o trabalho lê o fluxo de dados. A consulta é usada para transformar o fluxo de entrada de dados e a saída é para onde são enviados os resultados do trabalho.

Um trabalho requer pelo menos uma fonte de entrada para streaming de dados. A fonte de entrada do fluxo de dados pode ser armazenada em um Hub de eventos do Barramento de Serviço do Azure ou um armazenamento de Blobs do Azure. Para saber mais, veja [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md) e [Começar a usar o Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partições em origens e coletores
Dimensionamento de um trabalho do Stream Analytics tira proveito de partições na entrada ou saída. Particionamento permite que você divida dados em subconjuntos com base em uma chave de partição. Um processo que consome os dados (como um trabalho de Streaming Analytics) pode consumir e gravar diferentes partições em paralelo, o que aumenta a taxa de transferência. 

### <a name="inputs"></a>Entradas
Todas as entradas do Azure Stream Analytics podem tirar proveito do particionamento:
-   EventHub (é preciso definir a chave de partição explicitamente com a palavra-chave PARTITION BY)
-   Hub IoT (é preciso definir a chave de partição explicitamente com a palavra-chave PARTITION BY)
-   Armazenamento de blob

### <a name="outputs"></a>outputs

Quando você trabalha com o Stream Analytics, você pode tirar proveito do particionamento nas saídas:
-   Armazenamento do Azure Data Lake
-   Verificação de
-   tabela do Azure
-   Armazenamento de Blob (é possível definir a chave de partição explicitamente)
-   Azure Cosmos DB (é preciso definir a chave de partição explicitamente)
-   Hubs de Eventos (é preciso definir a chave de partição explicitamente)
-   Hub IoT (é preciso definir a chave de partição explicitamente)
-   Barramento de Serviço
- SQL e SQL Data Warehouse com o particionamento opcional: obter mais informações sobre a [Saída para a página do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

O Power BI não dá suporte ao particionamento. No entanto, você ainda pode particionar a entrada, conforme descrito [nesta seção](#multi-step-query-with-different-partition-by-values) 

Para obter mais informações sobre partições, consulte os seguintes artigos:

* [Visão geral dos recursos de Hubs de Eventos](../event-hubs/event-hubs-features.md#partitions)
* [Particionamento de dados](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Trabalhos embaraçosamente paralelos
Um trabalho *embaraçosamente paralelo* é o cenário mais escalonável que temos no Stream Analytics do Azure. Ele conecta uma partição da entrada para uma instância da consulta a uma partição da saída. Este paralelismo tem os seguintes requisitos:

1. Se sua lógica de consulta for dependente da mesma chave que está sendo processada pela mesma instância de consulta, você deverá garantir que os eventos sejam encaminhados para a mesma partição da entrada. Para Hubs de Eventos ou o Hub IoT, isso significa que os dados do evento devem ter o conjunto de valores **PartitionKey**. Como alternativa, você pode usar os remetentes particionados. Para armazenamento de Blobs, isso significa que os eventos são enviados à mesma pasta de partição. Se sua lógica de consulta não exigir que a mesma chave seja processada pela mesma instância de consulta, você pode ignorar esse requisito. Um exemplo disso seria uma consulta simples de seleção/projeto/filtro.  

2. Depois que os dados são dispostos como precisam ser no lado da saída, você precisa garantir que a consulta seja particionada. Isso exige que você use **PARTITION BY** em todas as etapas. Várias etapas são permitidas, mas todas elas devem ser particionadas pela mesma chave. No nível de compatibilidade 1.0 e 1.1, a chave de particionamento deve ser definida como **PartitionId** para que o trabalho seja totalmente paralelo. Para trabalhos com o nível de compatibilidade 1.2 e posterior, coluna personalizada pode ser especificada como chave de partição nas configurações de entrada e o trabalho será automoatically paralellized mesmo sem a cláusula PARTITION BY.

3. A maioria da saída pode tirar proveito do particionamento, no entanto, se você usar um tipo de saída que não dá suporte ao particionamento, seu trabalho não será totalmente paralelo. Consulte a [seção de saída](#outputs) para obter mais detalhes.

4. O número de partições de entrada deve ser igual ao número de partições de saída. A saída do Armazenamento de Blob pode dar suporte a partições, e herda o esquema de particionamento da consulta de upstream. Quando uma chave de partição do Armazenamento de Blob for especificada, os dados serão particionados por partição de entrada, portanto, o resultado ainda será totalmente paralelo. Exemplos de valores de partição que permitem um trabalho totalmente paralelo:

   * 8 partições de entrada de Hubs de Eventos e 8 partições de saída de Hubs de Eventos
   * 8 partições de entrada de Hubs de Eventos e Saída de armazenamento de Blobs
   * Oito partições de entrada do hub de eventos e a saída do armazenamento de blob particionadas por um campo personalizado com cardinalidade aleatória
   * 8 partições de entrada de armazenamento de Blobs e Saída de armazenamento de Blobs
   * 8 partições de entrada de armazenamento de Blobs e 8 partições de saída de Hubs de Eventos

As seções a seguir discutem alguns cenários de exemplo que são embaraçosamente paralelos.

### <a name="simple-query"></a>Consulta simples

* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

Consulta:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Essa consulta é um filtro simples. Portanto, nós não precisamos se preocupar sobre particionamento da entrada que está sendo enviada para o hub de eventos. Observe que com nível de compatibilidade de trabalhos antes de 1.2 deve incluir **PARTITION BY PartitionId** cláusula, portanto, ela preenche o requisito #2 anterior. Para a saída, é necessário configurar a saída de Hubs de Eventos no trabalho para ter a chave de partição definida como **PartitionId**. Uma última verificação é certificar-se de que o número de partições de entrada é igual ao número de partições de saída.

### <a name="query-with-a-grouping-key"></a>Consulta com chave de agrupamento

* Entrada: Hub de eventos com 8 partições
* Saída: Armazenamento de blob

Consulta:

```SQL
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

Nesse caso, não importa qual é a consulta. Se a contagem de partição de entrada não corresponder à contagem de partição de saída, a topologia não é embaraçosamente paralela. + No entanto, ainda podemos obter algum nível de paralelização.

### <a name="query-using-non-partitioned-output"></a>Consultar usando a saída não particionada
* Entrada: Hub de eventos com 8 partições
* Saída: Power BI

Atualmente, a saída do Power BI não suporta particionamento. Portanto, esse cenário não é embaraçosamente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de várias etapas com diferentes valores de PARTITION BY
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

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

Os exemplos anteriores mostram alguns trabalhos do Stream Analytics que está de acordo com (ou não) em uma topologia em paralela. Se eles estão em conformidade, eles têm o potencial para expansão máxima. Para trabalhos que não se encaixam em nenhum desses perfis, as diretrizes de expansão estarão disponíveis em atualizações futuras. Por enquanto, use as diretrizes gerais nas seções a seguir.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Nível de compatibilidade 1.2 - consulta de várias etapas com diferentes valores por partição 
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

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

Nível de compatibilidade 1.2 habilita a execução paralela da consulta por padrão. Por exemplo, a consulta da seção anterior será parttioned desde que a coluna "TollBoothId" é definida como chave de partição de entrada. Cláusula de partição a ParttionId não é necessária.

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

| Consultar                                               | Máxima quantidade de SUs para o trabalho |
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

## <a name="achieving-higher-throughputs-at-scale"></a>Alcançar taxas de transferência mais alta em escala

Uma [embaraçosamente paralelos](#embarrassingly-parallel-jobs) trabalho é necessária, mas não é suficiente para manter uma taxa de transferência maior em grande escala. Cada sistema de armazenamento e sua saída correspondente do Stream Analytics tem variações sobre como alcançar a taxa de transferência melhor gravação possíveis. Como com qualquer cenário em escala, há alguns desafios que podem ser resolvidos usando as configurações corretas. Esta seção discute as configurações para algumas saídas comuns e fornece exemplos para sustentar as taxas de ingestão de 1K, K 5 e 10 mil eventos por segundo.

As observações a seguir usam um trabalho do Stream Analytics com consulta sem monitoração de estado (passagem), um UDF do JavaScript que grava no Cosmos DB, BD SQL do Azure ou Hub de eventos basic.

#### <a name="event-hub"></a>Hub de evento

|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de saída  |
|--------|---------|---------|
| 1K     |    1    |  2 TU   |
| 5 MIL     |    6    |  6 TU   |
| 10.000    |    12   |  10 TU  |

O [Hub de eventos](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) solução é dimensionado linearmente em termos de streaming as SU (unidades) e taxa de transferência, tornando-o mais eficiente e modo de alto desempenho para analisar e transmitir dados para fora do Stream Analytics. Trabalhos podem ser dimensionados para até 192 SU, que basicamente resulta em processamento até 200 MB/s ou 19 trilhões de eventos por dia.

#### <a name="azure-sql"></a>SQL do Azure
|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de saída  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5 MIL   |   18 |  P4   |
|    10.000  |   36 |  P6   |

[SQL Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) oferece suporte à gravação em paralelo, chamado herdam particionamento, mas ele não está habilitado por padrão. No entanto, a habilitação herdam o particionamento, junto com uma consulta totalmente paralela, pode não ser suficiente para alcançar taxas de transferência mais alta. Taxas de transferência de gravação SQL dependerá muito do seu esquema de configuração e a tabela de banco de dados do SQL Azure. O [desempenho de saída SQL](./stream-analytics-sql-output-perf.md) artigo traz mais detalhes sobre os parâmetros que podem maximizar a taxa de transferência de gravação. Conforme observado na [saída do Azure Stream Analytics para o banco de dados SQL](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artigo, essa solução não são dimensionadas linearmente, como um pipeline totalmente paralelo além dos 8 partições e talvez seja necessário reparticionar antes da saída do SQL (consulte [ EM](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). SKUs premium são necessários para sustentar altas taxas de e/s, juntamente com a sobrecarga de backups de log acontecendo cada alguns minutos.

#### <a name="cosmos-db"></a>Cosmos DB
|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de saída  |
|-------|-------|---------|
|  1K   |  3    | 20K RU  |
|  5 MIL   |  24   | 60K RU  |
|  10.000  |  48   | 120K RU |

[O cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) saída do Stream Analytics foi atualizada para usar a integração nativa sob [nível de compatibilidade 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Nível de compatibilidade 1.2 permite significativamente maior taxa de transferência e reduz o consumo de RU em relação a 1.1, que é o nível de compatibilidade padrão para novos trabalhos. A solução usa contêineres do cosmos DB particionados na /deviceId e o restante da solução está configurado de forma idêntica.

Todos os [Streaming em exemplos de escala do azure](https://github.com/Azure-Samples/streaming-at-scale) usar um Hub de eventos alimentado por carga para simular clientes de teste como entrada. Cada evento de entrada é um documento JSON de 1KB, que converte as taxas de ingestão configurado para as taxas de transferência (1MB/s, 5MB/s e 10MB/s) com facilidade. Eventos de simular um dispositivo de IoT, enviar os seguintes dados JSON (em uma forma abreviada) para dispositivos de até 1 K:

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
> As configurações estão sujeitos a alterações devido a vários componentes usados na solução. Para obter uma estimativa mais precisa, personalize os exemplos para ajustar seu cenário.

### <a name="identifying-bottlenecks"></a>Identificando afunilamentos

Use o painel de métricas no trabalho do Azure Stream Analytics para identificar gargalos no seu pipeline. Revisão **eventos de entrada/saída** taxa de transferência e ["Atraso de marca d'água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **eventos com lista de pendências** para ver se o trabalho está acompanhando a taxa de entrada. Para métricas do Hub de eventos, procure **solicitações limitadas** e ajustar as unidades de limite adequadamente. Para métricas do Cosmos DB, examine **máximo de RU/s consumidas por intervalo de chaves de partição** na taxa de transferência para garantir que sua partição de intervalos de chaves são consumidos uniformemente. Para o Azure SQL DB, monitore **e/s de Log** e **CPU**.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

