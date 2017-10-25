---
title: 'Azure Stream Analytics: Compreender e ajustar as Unidades de Streaming | Microsoft Docs'
description: Entenda quais fatores afetam o desempenho no Azure Stream Analytics.
keywords: unidade de streaming, desempenho de consulta
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 14f73322701dffea283432bf2a25b5e6cd3e9de4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-adjust-streaming-units"></a>Compreender e ajustar as Unidades de Streaming

O Stream Analytics do Azure agrega o "peso" do desempenho de executar um trabalho em SUs (Unidades de Streaming). As SUs representam os recursos de computação que são consumidos para execução de um trabalho. As SUs fornecem uma maneira de descrever a capacidade de processamento de evento relativa, com base em uma medida combinada de CPU, memória e taxas de leitura e gravação. Essa capacidade permite a você focar na lógica da consulta e elimina a necessidade de conhecer as considerações do desempenho da camada do armazenamento, de alocar manualmente memória para o trabalho e de aproximar a contagem de núcleo de CPU necessária para executar seu trabalho pontualmente.

Para obter o processamento de streaming de baixa latência, os trabalhos do Azure Stream Analytics executam todo o processamento na memória. Quando a memória está acabando, o trabalho de streaming falha. Como resultado, para um trabalho de produção, é importante monitorar o uso de recursos de um trabalho de streaming e verificar se há recursos suficientes alocados para manter os trabalhos em execução 24/7.

A métrica é uma porcentagem, variando de 0% a 100%. Para um trabalho de streaming com volume mínimo, a métrica % de Utilização de SU costuma ficar entre 10 a 20%. É melhor manter a métrica abaixo de 80% para levar em conta os picos ocasionais.  Você pode definir um alerta na métrica (clique [aqui para configurar alertas de métrica](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/insights-alerts-portal)).



## <a name="configure-stream-analytics-streaming-units-sus"></a>Configurar as Unidades de Streaming (SU) do Stream Analytics
1. Entre no [Portal do Azure](http://portal.azure.com/)
2. Na lista de recursos, localize o trabalho do Stream Analytics que você deseja escalar e abra-o. 
3. Na folha do trabalho, em Configurar, clique em **Escala**. 

    ![Configuração de trabalho do Stream Analytics no Portal do Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Use o controle deslizante para definir o SUs para o trabalho. Observe que você está limitado a configurações específicas de SU. 


## <a name="monitor-job-performance"></a>Monitorar o desempenho do trabalho
Usando o portal do Azure, você pode acompanhar a taxa de transferência de um trabalho:

![Análise de fluxo do Azure, monitorar trabalhos][img.stream.analytics.monitor.job]

Calcule a taxa de transferência esperada da carga de trabalho. Caso a taxa de transferência seja menor do que o esperado, ajuste a partição de entrada e a consulta e adicione unidades de streaming ao seu trabalho.


## <a name="how-many-sus-are-required-for-a-job"></a>Quantas SUs são necessárias para um trabalho?

A escolha do número de SUs necessárias para um trabalho específico depende da configuração da partição para as entradas e da consulta que é definida dentro do trabalho. A página **Escala** permite que você defina o número correto de SUs. Trata-se de uma prática recomendada para alocar mais SUs do que o necessário. O mecanismo de processamento do Stream Analytics foi otimizado para latência e taxa de transferência ao custo da alocação de memória adicional.

Em geral, a prática recomendada é iniciar com 6 SUs para consultas que não usam **PARTITION BY**. Em seguida, determine o ponto ideal usando um método de tentativa e erro no qual você modifica o número de SUs depois de passar volumes representativos de dados, e examine a métrica % de Utilização de SU.

Para saber mais sobre como escolher o número correto de SUs, consulte esta página: [Escalar trabalhos do Azure Stream Analytics para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)

> [!Note]
> A escolha de quantas SUs são necessárias para um trabalho específico depende da configuração de partição das entradas e da consulta definida para o trabalho. Você pode selecionar até sua cota de SUs para um trabalho. Por padrão, cada assinatura do Azure tem uma cota de até 200 SUs para todos os trabalhos analíticos em uma região específica. Para aumentar as SUs para suas assinaturas, entre em contato com o [Suporte da Microsoft](http://support.microsoft.com). Os valores válidos para o SUs por trabalho são 1, 3, 6 e em incrementos de 6.
> Observe que usar 1 SU não é recomendado para trabalhos de produção. Normalmente, orientamos no sentido de usar os trabalhos com 1 SU apenas para criação de protótipos e testes.



## <a name="factors-increasing-su-utilization"></a>Fatores que aumenta a % de utilização de SU 
### <a name="stateful-query-logic"></a>Lógica de consulta com estado 
Um dos recursos exclusivos do trabalho do Azure Stream Analytics é a execução do processamento com estado, como funções de análise temporal, de junções temporais e de agregações em janela. Cada um desses operadores mantém alguns estados. 
#### <a name="windowed-aggregates"></a>Agregações em janelas
O tamanho do estado de uma agregação em janela é proporcional ao número de grupos (cardinalidade) no grupo por operador. Por exemplo, na consulta a seguir, o número associado a clusterid é a cardinalidade da consulta. 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


Para amenizar os problemas causados pela alta cardinalidade na consulta anterior, você pode enviar eventos ao Hub de Eventos particionados por ''clusterid'', e escalar horizontalmente a consulta permitindo que o sistema processe cada partição de entrada separadamente usando **PARTITION BY**, conforme mostra o exemplo a seguir:


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

Depois que a consulta é particionada horizontalmente, ela é espalhada em vários nós. Como resultado, o número de clusterid em cada nó é reduzido, diminuindo assim a cardinalidade do grupo por operador. 

As partições do Hub de Evento devem ser particionadas pela chave de agrupamento para evitar a necessidade de uma etapa de redução. Confira detalhes adicionais [aqui](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-overview). 
#### <a name="temporal-join"></a>Junção temporal
O tamanho do estado de uma junção temporal é proporcional ao número de eventos no espaço de manobra temporal da junção, que é a taxa de eventos de entrada multiplicada pelo tamanho do espaço de manobra. 

O número de eventos sem correspondência na junção afeta a utilização da memória para a consulta. A consulta a seguir está tentando localizar as impressões de anúncio que geram cliques:

    SELECT id
    FROM clicks 
    INNER JOIN, impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

Neste exemplo, é possível que vários anúncios sejam exibidos e algumas pessoas cliquem neles, e é necessário manter todos os eventos em uma janela do tempo. A memória consumida é proporcional ao tamanho da janela e à taxa de eventos. 

Para corrigir isso, envie eventos ao Hub de Eventos particionados por chaves de junção (id neste caso), e escale horizontalmente a consulta permitindo que o sistema processe cada partição de entrada separadamente usando **PARTITION BY**, conforme mostrado:

    SELECT id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

Depois que a consulta é particionada horizontalmente, ela é espalhada em vários nós. Como resultado, o número de eventos em cada nó é reduzido, diminuindo o tamanho do estado mantido na janela de junção. 
#### <a name="temporal-analytic-function"></a>Função de análise temporal
O tamanho do estado de uma função de análise temporal é proporcional à taxa de eventos multiplicada pela duração. A correção é semelhante à junção temporal. Você pode escalar horizontalmente a consulta usando **PARTITION BY**. 

### <a name="out-of-order-buffer"></a>Buffer fora de ordem 
Usuário pode configurar o tamanho do buffer fora de ordem no painel de configuração de Ordenação de Eventos. O buffer é usado para armazenar as entradas durante o período e, depois, reordená-las. O tamanho do buffer é proporcional à taxa de entrada de evento multiplicada pelo tamanho da janela fora de ordem. O tamanho da janela padrão é 0. 

Para corrigir isso, escale horizontalmente a consulta usando **PARTITION BY**. Depois que a consulta é particionada horizontalmente, ela é espalhada em vários nós. Como resultado, o número de eventos em cada nó é reduzido, diminuindo o número de eventos em cada buffer de reordenação. 

### <a name="input-partition-count"></a>Contagem de partição de entrada 
Cada partição de entrada de um trabalho de entrada tem um buffer. Quanto maior o número de partições de entrada, mais recursos o trabalho consumirá. Para cada SU, o Azure Stream Analytics pode processar aproximadamente 1 MB/s de entrada. Portanto, convém corresponder o número ASA SU com o número de partições de seu Hub de Eventos. Normalmente, um trabalho de 1 SU é suficiente para um Hub de Eventos com duas partições (que é o mínimo para o Hub de Eventos). Se o Hub de Eventos tem mais partições, seu trabalho de ASA consumirá mais recursos, mas não necessariamente usará a taxa de transferência adicional fornecida pelo Hub de Eventos. Para um trabalho de 6 SU, talvez seja necessário quatro ou oito partições do Hub de Eventos. Um Hub de Eventos com 16 partições ou mais em um trabalho de 1 SU geralmente contribui para o uso excessivo de recursos, e deve ser evitado. 

### <a name="reference-data"></a>Dados de referência 
Os dados de referência no ASA são carregados na memória para uma pesquisa rápida. Com a implementação atual, cada operação de junção com dados de referência mantém uma cópia dos dados de referência na memória, mesmo se você ingressar com os mesmos dados de referência várias vezes. Para consultas com **PARTITION BY**, cada partição tem uma cópia dos dados de referência, para que as partições fiquem completamente separadas. Com o efeito multiplicador, o uso da memória pode ficar muito alto rapidamente se você ingressar com dados de referência várias vezes e com várias partições.  

#### <a name="use-of-udf-functions"></a>Uso de funções UDF
Quando você adiciona uma função UDF, o Azure Stream Analytics carrega o tempo de execução do JavaScript na memória. Isso afetará a % de SU.


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Criar consultas paralelizáveis no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Dimensionar trabalhos do Azure Stream Analytics para aumentar a produtividade](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
