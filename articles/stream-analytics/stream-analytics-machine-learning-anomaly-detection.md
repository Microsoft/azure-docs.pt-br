---
title: "Detecção de anomalias no Guia de Uso do Azure (versão prévia) | Microsoft Docs"
description: Use o stream analytics e machine learning para detectar anomalias.
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: 43a2a9784668fad2aa5b1441cfd37751c0c240b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-anomalydetection-operator"></a>Usar o operador ANOMALYDETECTION

> [!IMPORTANT]
> Essa funcionalidade está em versão prévia. Não recomendamos o uso em produção.

O operador**ANOMALYDETECTION** pode ser usado para detectar anomalias em fluxos de eventos.
Por exemplo, uma diminuição lenta na memória livre durante um longo tempo pode ser uma indicação de um vazamento de memória, ou o número de solicitações de serviço Web que são estáveis em um intervalo pode aumentar ou diminuir muito.

Ele verifica se há os seguintes tipos de anomalias durante o período especificado:

- Alteração no nível bidirecional
- Tendência positiva lenta
- Tendência negativa lenta

O intervalo de tempo que indica o quanto no histórico do evento atual precisa ser examinado é limitado usando a cláusula **LIMIT DURATION**. **ANOMALYDETECTION** pode, opcionalmente, ser limitado apenas aos eventos que correspondam a determinada propriedade ou condição usando a cláusula **WHEN**.

Também pode, como opção, processar grupos de eventos separadamente com base na chave especificada na cláusula **PARTITION BY**. Treinamento e previsão ocorrem independentemente em cada partição.

## <a name="syntax"></a>Sintaxe

`ANOMALYDETECTION(\<scalar_expression\>) OVER ([PARTITION BY \<partition key\>] LIMIT DURATION(\<unit\>, \<length\>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>Exemplo de uso

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id \> 100) FROM input`|


## <a name="arguments"></a>Argumentos

- **scalar_expression**

  A expressão escalar durante a qual a detecção de anomalias será executada. É uma expressão de tipo float ou bigint que retorna um valor único (escalar). A expressão curinga **\*** não é permitida. **scalar_expression** não pode conter outras funções analíticas ou externas.

- **OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])**

- **partition_by_clause** 

  A cláusula `PARTITION BY \<partition key\>` divide o aprendizado e o treinamento em partições separadas. Em outras palavras, um modelo separado seria usado por valor de `\<partition key\>`, e apenas os eventos com esse valor seriam usados para o aprendizado e treinamento nesse modelo. Por exemplo,

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  treinará e classificará uma leitura em relação a outras leituras do mesmo sensor.

- **limit_duration clause** DURATION(\<unit\>, \<length\>)

  Especifica a quantidade de histórico do evento atual a ser considerada na computação **ANOMALYDETECTION**. Consulte DATEDIFF para obter uma descrição detalhada de unidades com suporte e suas abreviações.

- **when_clause** 

  Especifica uma condição Booliana para os eventos considerados na computação **ANOMALYDETECTION**.

## <a name="return-types"></a>Tipos de retorno

A função retorna um Registro que contém todas as três pontuações como sua saída. As propriedades associadas aos diferentes tipos de detectores de anomalias são chamadas:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Para extrair os valores individuais do registro, use a função **GetRecordPropertyValue**. Por exemplo:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) \> 3.25` 


Uma anomalia de um tipo específico é detectada quando uma dessas pontuações de anomalias cruza um limite. O limite pode ser qualquer número de ponto flutuante \>= 0. O limite é um equilíbrio entre sensibilidade e confiança. Por exemplo, um limite inferior tornaria a detecção mais sensível a alterações e geraria mais alertas, enquanto um limite superior poderia tornar a detecção menos sensível e mais segura, mas mascararia algumas anomalias. O valor exato do limite a ser usado depende do cenário. Não há limite superior, mas o intervalo recomendado é 3,25-5.

## <a name="algorithm"></a>Algoritmo

**ANOMALYDETECTION** usa semântica de janela deslizante, o que significa que o cálculo é executado por evento que insere a função, e uma pontuação é gerada para o evento. O cálculo tem base em Permutabilidade Martingales, que opera verificando se a distribuição dos valores de evento foi alterado. Nesse caso, uma possível anomalia foi detectada. A pontuação retornada é uma indicação do nível de confiança dessa anomalia. Como uma otimização interna, **ANOMALYDETECTION** calcula a pontuação de anomalia de um evento com base em *d* a *2d* eventos, em que *d* é o tamanho da janela de detecção especificado.

**ANOMALYDETECTION** espera que a série de tempo de entrada seja uniforme. Um fluxo de eventos pode ficar uniforme pela agregação sobre uma janela em cascata ou de salto. Em cenários onde a lacuna entre os eventos sempre é menor do que a janela de agregação, uma janela em cascata é suficiente para tornar a série temporal uniforme. Quando o intervalo puder ser maior, os intervalos poderão ser preenchidos repetindo o último valor usando uma janela de salto. Esses dois cenários podem ser tratados pelo exemplo a seguir. Atualmente, a etapa `FillInMissingValuesStep` não pode ser ignorada. A ausência dessa etapa resultará em um erro de compilação.

## <a name="performance-guidance"></a>Diretriz de desempenho

- Use 6 UA para trabalhos. 
- Envie eventos com pelo menos 1 segundo de intervalo.
- Uma consulta não particionada usando a função **ANOMALYDETECTION** pode produzir resultados com uma latência de computação de aproximadamente 25 ms em média.
- A latência experimentada por uma consulta particionada varia um pouco com o número de partições, à medida que o número de cálculos for maior. No entanto, a latência é quase a mesma que o caso não particionado para um número total comparável de eventos em todas as partições.
- Durante a leitura de dados não em tempo real, uma grande quantidade de dados é incluída rapidamente. O processamento de dados é consideravelmente mais lento no momento. Descobriu-se que a latência nesses cenários aumenta linearmente com o número de pontos de dados na janela, em vez do tamanho da janela o intervalo de eventos propriamente dito. Para reduzir a latência em casos de não em tempo real, considere o uso de um tamanho menor de janela. Como alternativa, considere começar seu trabalho na hora atual. Alguns exemplos de latências em uma consulta não particionada: 
    - 60 pontos de dados na janela de detecção podem resultar em uma latência de 10 segundos, com uma taxa de transferência de 3 Mbps. 
    - Em 600 pontos de dados, a latência pode atingir aproximadamente 80 segundos com uma taxa de transferência de 0,4 MBps.

## <a name="example"></a>Exemplo

A consulta a seguir pode ser usada para gerar um alerta se uma anomalia for detectada.
Quando o fluxo de entrada não é uniforme, a etapa de agregação pode ajudar a transformá-lo em uma série temporal uniforme. O exemplo usa **AVG**, mas o tipo específico de agregação depende do cenário do usuário. Além disso, quando uma série temporal tem lacunas maiores do que a janela de agregação, não haverá nenhum evento na série temporal para disparar a detecção de anomalias (de acordo com a semântica de janela deslizante). Como resultado, a suposição de uniformidade será interrompida quando o próximo evento chegar. Em tais situações, precisamos de um modo de preenchimento das lacunas na série temporal. Uma abordagem possível é colocar o último evento em cada janelas de salto, conforme mostrado abaixo.

Conforme observado anteriormente, não ignore a etapa `FillInMissingValuesStep` por enquanto. A ausência dessa etapa resultará em um erro de compilação.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) \>= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) \>=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) \>=
       3.25

## <a name="references"></a>Referências

* [Detecção de Anomalias – Usar Machine Learning para detectar anormalidades nos dados da série temporal](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [API de detecção de anomalias do Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Detecção de anomalias de série temporal](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

