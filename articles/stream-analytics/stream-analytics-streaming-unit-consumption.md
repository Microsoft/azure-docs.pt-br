---
title: Unidades de streaming no Azure Stream Analytics
description: Este artigo descreve a configuração de Unidades de Streaming e outros fatores que afetam o desempenho no Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: a5a0e6feba966d2d10c5cd36432c3d5db172a795
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020002"
---
# <a name="understand-and-adjust-streaming-units"></a>Compreender e ajustar as Unidades de Streaming

As unidades de streaming (SUs) representam os recursos de computação alocados para executar um trabalho de Stream Analytics. Quanto maior o número de SUs, mais recursos de CPU e memória são alocados para o trabalho. Esta capacidade permite que você se concentre na lógica de consulta e abstrai a necessidade de gerenciar o hardware para executar o trabalho do Stream Analytics de maneira oportuna.

Para obter o processamento de streaming de baixa latência, os trabalhos do Azure Stream Analytics executam todo o processamento na memória. Quando a memória está acabando, o trabalho de streaming falha. Como resultado, em um trabalho de produção, é importante monitorar o uso de recursos de um trabalho de streaming e verificar se há recursos suficientes alocados para manter os trabalhos em execução 24/7.

A métrica de utilização % SU, que varia de 0% a 100%, descreve o consumo de memória da carga de trabalho. Para um trabalho de streaming com volume mínimo, a métrica costuma ficar entre 10 a 20%. Se a subutilização de SU% for alta (acima de 80%) ou se os eventos de entrada forem registrados (mesmo com uma baixa% de utilização, já que ele não mostra o uso da CPU), a carga de trabalho provavelmente exigirá mais recursos de computação, o que exige que você aumente o número de SUs. É melhor manter a métrica de SU abaixo de 80% para considerar picos ocasionais. Para reagir às cargas de trabalho aumentadas e aumentar as unidades de streaming, considere definir um alerta de 80% na métrica de utilização de SU. Além disso, você pode usar as métricas de atraso da marca d' água e eventos de pendência para ver se há um impacto.

## <a name="configure-stream-analytics-streaming-units-sus"></a>Configurar as Unidades de Streaming (SU) do Stream Analytics
1. Entre no [Portal do Azure](https://portal.azure.com/)

2. Na lista de recursos, localize o trabalho do Stream Analytics que você deseja escalar e abra-o. 

3. Na página do trabalho, no título **Configurar**, selecione **Escalar**. O número padrão de SUs é 3 ao criar um trabalho.

    ![Configuração de trabalho do Stream Analytics no Portal do Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Use o controle deslizante para definir o SUs para o trabalho. Observe que você está limitado a configurações específicas de SU. 
5. Você pode alterar o número de SUs atribuído ao seu trabalho, mesmo quando ele estiver em execução. Isso não será possível se seu trabalho usar uma [saída não particionada](./stream-analytics-parallelization.md#query-using-non-partitioned-output) ou tiver [uma consulta de várias etapas com uma partição diferente por valores](./stream-analytics-parallelization.md#multi-step-query-with-different-partition-by-values). Talvez você esteja restrito a escolher um conjunto de valores de SU quando o trabalho estiver em execução. 

## <a name="monitor-job-performance"></a>Monitorar o desempenho do trabalho
Usando o portal do Azure, você pode acompanhar a taxa de transferência de um trabalho:

![Análise de fluxo do Azure, monitorar trabalhos][img.stream.analytics.monitor.job]

Calcule a taxa de transferência esperada da carga de trabalho. Caso a taxa de transferência seja menor do que o esperado, ajuste a partição de entrada e a consulta e adicione unidades de streaming ao seu trabalho.

## <a name="how-many-sus-are-required-for-a-job"></a>Quantas SUs são necessárias para um trabalho?

A escolha do número de SUs necessárias para um trabalho específico depende da configuração da partição para as entradas e da consulta que é definida dentro do trabalho. A página **Escala** permite que você defina o número correto de SUs. Trata-se de uma prática recomendada para alocar mais SUs do que o necessário. O mecanismo de processamento do Stream Analytics foi otimizado para latência e taxa de transferência ao custo da alocação de memória adicional.

Em geral, a prática recomendada é iniciar com 6 SUs para consultas que não usam **PARTITION BY**. Em seguida, determine o ponto ideal usando um método de tentativa e erro no qual você modifica o número de SUs depois de passar volumes representativos de dados, e examine a métrica % de Utilização de SU. O número total de unidades de streaming que pode ser usado por um trabalho do Stream Analytics depende do número de etapas na consulta definida para o trabalho e do número de partições em cada etapa. Saiba mais sobre os limites [aqui](./stream-analytics-parallelization.md#calculate-the-maximum-streaming-units-of-a-job).

Para saber mais sobre como escolher o número correto de SUs, consulte esta página: [Escalar trabalhos do Azure Stream Analytics para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)

> [!Note]
> A escolha de quantas SUs são necessárias para um trabalho específico depende da configuração de partição das entradas e da consulta definida para o trabalho. Você pode selecionar até sua cota de SUs para um trabalho. Por padrão, cada assinatura do Azure tem uma cota de até 500 SUs para todos os trabalhos de análise em uma região específica. Para aumentar as SUs para suas assinaturas, entre em contato com o [Suporte da Microsoft](https://support.microsoft.com). Os valores válidos para o SUs por trabalho são 1, 3, 6 e em incrementos de 6.

## <a name="factors-that-increase-su-utilization"></a>Fatores que aumentam a utilização de % SU 

Elementos de consulta temporal (orientados ao tempo) são o conjunto principal de operadores com monitoração de estado fornecido por Stream Analytics. O Stream Analytics gerencia o estado dessas operações internamente em nome de usuário, ao gerenciar o consumo de memória, pontos de verificação para a resiliência e a recuperação de estado durante as atualizações de serviço. Embora o Stream Analytics gerencia totalmente os estados, há um número de práticas recomendadas que os usuários devem considerar.

Observe que um trabalho com lógica de consulta complexa pode ter alta utilização de % de SU mesmo quando não estiver recebendo eventos de entrada continuamente. Isso pode acontecer após um aumento repentino nos eventos de entrada e saída. Se a consulta for complexa, o trabalho poderá continuar a manter o estado na memória.

A utilização percentual da UA pode cair repentinamente para 0 por um curto período antes de voltar aos níveis esperados. Isso ocorre devido a erros transitórios ou atualizações iniciadas pelo sistema. O aumento do número de unidades de streaming para um trabalho pode não reduzir a utilização de SU% se a consulta não for [totalmente paralela](./stream-analytics-parallelization.md).

Ao comparar a utilização durante um período de tempo, use [métricas de taxa de eventos](stream-analytics-monitoring.md). As métricas InputEvents e OutputEvents mostram quantos eventos foram lidos e processados. Há métricas que indicam o número de eventos de erro também, como erros de desserialização. Quando o número de eventos por unidade de tempo aumenta, o SU% aumenta na maioria dos casos.

## <a name="stateful-query-logic-in-temporal-elements"></a>Lógica de consulta com estado em elementos temporais
Um dos recursos exclusivos do trabalho do Azure Stream Analytics é a execução do processamento com estado, como funções de análise temporal, de junções temporais e de agregações em janela. Cada um desses operadores mantém as informações de estados. O tamanho máximo da janela para esses elementos de consulta é de sete dias. 

O conceito de janela temporal é exibido em vários elementos de consulta do Stream Analytics:
1. Agregações em janela: GROUP BY Em cascata, Salto e Janelas deslizantes

2. Junções temporais: JOIN à função DATEDIFF

3. Funções analíticas temporais: ISFIRST, LAST e a LAG com LIMIT DURATION

Os seguintes fatores influenciam a memória usada (parte da métrica de unidades de streaming) por trabalhos do Stream Analytics:

## <a name="windowed-aggregates"></a>Agregações em janelas
A memória consumida (tamanho do estado) para uma agregação em janelas nem sempre é diretamente proporcional ao tamanho da janela. Em vez disso, a memória consumida é proporcional à cardinalidade de dados ou o número de grupos em cada janela de tempo.


Por exemplo, na consulta a seguir, o número associado a `clusterid` é a cardinalidade da consulta. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Para reduzir os problemas causados pela alta cardinalidade na consulta anterior, você pode enviar eventos para o Hub de eventos particionado pelo `clusterid` e escalar horizontalmente a consulta, permitindo que o sistema processe cada partição de entrada separadamente usando **Partition by** , conforme mostrado no exemplo abaixo:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Depois que a consulta é particionada horizontalmente, ela é espalhada em vários nós. Como resultado, o número de valores de `clusterid` em cada nó é reduzido, diminuindo assim a cardinalidade do grupo por operador. 

As partições do Hub de Evento devem ser particionadas pela chave de agrupamento para evitar a necessidade de uma etapa de redução. Para obter mais informações, confira [Visão Geral dos Hubs de Eventos](../event-hubs/event-hubs-about.md). 

## <a name="temporal-joins"></a>Junções temporais
A memória consumida (tamanho do estado) de uma junção temporal é proporcional ao número de eventos no espaço de manobra temporal da junção, que é a taxa de entrada do evento multiplicada pelo tamanho do espaço da ondulação. Em outras palavras, a memória consumida por junções é proporcional ao intervalo de tempo DateDiff multiplicado por taxa média de eventos.

O número de eventos sem correspondência na junção afeta a utilização da memória para a consulta. A consulta a seguir está tentando localizar as impressões de anúncio que geram cliques:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Neste exemplo, é possível que vários anúncios sejam exibidos e algumas pessoas cliquem neles, e é necessário manter todos os eventos em uma janela do tempo. A memória consumida é proporcional ao tamanho da janela e à taxa de eventos. 

Para corrigir isso, envie eventos para o Hub de eventos particionado pelas chaves de junção (ID nesse caso) e escale horizontalmente a consulta, permitindo que o sistema processe cada partição de entrada separadamente usando  **Partition,** conforme mostrado:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Depois que a consulta é particionada horizontalmente, ela é espalhada em vários nós. Como resultado, o número de eventos em cada nó é reduzido, diminuindo o tamanho do estado mantido na janela de junção. 

## <a name="temporal-analytic-functions"></a>Funções de análise temporal
A memória consumida (tamanho do estado) de uma função de análise temporal é proporcional à taxa de eventos multiplicada pela duração. A memória consumida por funções analíticas não é proporcional ao tamanho da janela, mas em vez disso, a contagem de partição em cada janela de tempo.

A correção é semelhante à junção temporal. Você pode escalar horizontalmente a consulta usando **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Buffer fora de ordem 
Usuário pode configurar o tamanho do buffer fora de ordem no painel de configuração de Ordenação de Eventos. O buffer é usado para armazenar as entradas durante o período e, depois, reordená-las. O tamanho do buffer é proporcional à taxa de entrada de evento multiplicada pelo tamanho da janela fora de ordem. O tamanho da janela padrão é 0. 

Para corrigir o estouro de buffer de fora de ordem, expanda a consulta usando **PARTITION BY**. Depois que a consulta é particionada horizontalmente, ela é espalhada em vários nós. Como resultado, o número de eventos em cada nó é reduzido, diminuindo o número de eventos em cada buffer de reordenação. 

## <a name="input-partition-count"></a>Contagem de partição de entrada 
Cada partição de entrada de um trabalho de entrada tem um buffer. Quanto maior o número de partições de entrada, mais recursos o trabalho consumirá. Para cada unidade de streaming, o Azure Stream Analytics pode processar aproximadamente 1 MB/s de entrada. Portanto, você pode otimizar correspondendo o número de unidades de streaming do Stream Analytics com o número de partições no seu Hub de Eventos. 

Normalmente, um trabalho configurado com uma unidade de streaming é suficiente para um Hub de Eventos com duas partições (que é o requisito mínimo para o Hub de Eventos). Se o Hub de Eventos tem mais partições, seu trabalho do Stream Analytics consumirá mais recursos, mas não necessariamente usará a taxa de transferência adicional fornecida pelo Hub de Eventos. 

Para um trabalho com 6 unidades de streaming, talvez seja necessário quatro ou oito partições do Hub de Eventos. No entanto, evite muitas partições desnecessárias uma vez que provoca o uso excessivo de recursos. Por exemplo, um Hub de Eventos com 16 partições ou mais em um trabalho do Stream Analytics que tem 1 unidade de streaming. 

## <a name="reference-data"></a>Dados de referência 
Os dados de referência no ASA são carregados na memória para uma pesquisa rápida. Com a implementação atual, cada operação de junção com dados de referência mantém uma cópia dos dados de referência na memória, mesmo se você ingressar com os mesmos dados de referência várias vezes. Para consultas com **PARTITION BY**, cada partição tem uma cópia dos dados de referência, para que as partições fiquem completamente separadas. Com o efeito multiplicador, o uso da memória pode ficar muito alto rapidamente se você ingressar com dados de referência várias vezes e com várias partições.  

### <a name="use-of-udf-functions"></a>Uso de funções UDF
Quando você adiciona uma função UDF, o Azure Stream Analytics carrega o runtime do JavaScript na memória. Isso afetará a % de SU.

## <a name="next-steps"></a>Próximas etapas
* [Criar consultas paralelizáveis no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Dimensionar Azure Stream Analytics trabalhos para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png
