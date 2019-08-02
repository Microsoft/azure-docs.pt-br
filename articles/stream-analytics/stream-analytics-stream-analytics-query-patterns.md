---
title: Padrões de consulta comuns no Azure Stream Analytics
description: Este artigo descreve vários designs e padrões de consulta comuns que são úteis em trabalhos do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 729385a2ce9feb6e69f9be29c2175b403093be3f
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413373"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemplos de consulta para padrões de uso do Stream Analytics

As consultas no Azure Stream Analytics são expressas em uma linguagem de consulta parecida com o SQL. Essas construções de linguagem estão documentadas no guia [Referência de linguagem de consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). 

O design de consulta pode expressar a lógica de passagem simples para mover dados de um fluxo de entrada para um armazenamento de dados de saída ou pode fazer uma correspondência de padrão avançada e análise temporal para calcular agregações em várias janelas de tempo como na [criação de uma solução de IOT usando](stream-analytics-build-an-iot-solution-using-stream-analytics.md) o guia Stream Analytics. Você pode unir dados de várias entradas para combinar eventos de streaming e pode fazer pesquisas em dados de referência estática para enriquecer os valores de evento. Você também pode gravar dados em várias saídas.

Este artigo descreve soluções para vários padrões de consulta comuns com base em cenários do mundo real.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Trabalhar com Tipos de Dados complexos em JSON e AVRO

O Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro.

JSON e Avro podem conter tipos complexos, como matrizes ou objetos aninhados (registros). Para obter mais informações sobre como trabalhar com esses tipos de dados complexos, consulte o artigo [analisando dados JSON e Avro](stream-analytics-parsing-json.md) .

## <a name="query-example-convert-data-types"></a>Exemplo de consulta: Converter tipos de dados

**Descrição**: Defina os tipos de propriedades no fluxo de entrada. Por exemplo, o peso do carro está chegando no fluxo de entrada como cadeias de caracteres e precisa ser convertido em **int** para executar **sum**.

**Entrada**:

| Faça | Time | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Saída**:

| Faça | Peso |
| --- | --- |
| Honda |3000 |

**Solução**:

```SQL
    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Explicação**: Use uma instrução **CAST** no campo **Weight** para especificar o tipo de dados. Veja a lista de tipos de dados com suporte em [Tipos de dados (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Exemplo de consulta: Usar LIKE/não gostar de fazer correspondência de padrões

**Descrição**: Verifique se um valor de campo no evento corresponde a um determinado padrão.
Por exemplo, verifique se o resultado retorna placas de licença que começam com A e terminam com 9.

**Entrada**:

| Faça | PlacaDeCarro | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Saída**:

| Faça | PlacaDeCarro | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Solução**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Explicação**: Use a instrução **LIKE** para verificar o valor do campo **LicensePlate**. Ele deve começar com a letra A e, em seguida, ter qualquer cadeia de caracteres de zero ou mais e terminar com o número 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemplo de consulta: Especifique a lógica para casos/valores diferentes (instruções CASE)

**Descrição**: Forneça uma computação diferente para um campo com base em um critério específico. Por exemplo, forneça uma descrição de cadeia de caracteres para quantos carros da mesma marca passaram, com um caso especial para 1.

**Entrada**:

| Faça | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída**:

| CarrosPassaram | Time |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Solução**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Explicação**: A expressão **CASE** compara uma expressão com um conjunto de expressões simples para determinar o resultado. Neste exemplo, o veículo faz com uma contagem de 1 retornou uma descrição de cadeia de caracteres diferentes de veículo faz com um número diferente de 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemplo de consulta: Enviar dados para várias saídas

**Descrição**: Envie dados para vários destinos de saída de um único trabalho. Por exemplo, analise dados de um alerta baseado em limite e arquive todos os eventos no armazenamento de blobs.

**Entrada**:

| Faça | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída1**:

| Faça | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída2**:

| Faça | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Solução**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3
```

**Explicação**: A cláusula **INTO** informa ao Stream Analytics qual das saídas gravar os dados a partir dessa instrução. A primeira consulta é uma passagem dos dados recebidos para uma saída chamada **saídadearquivos**. A segunda consulta faz uma agregação e filtragem simples e envia os resultados para um sistema de alerta downstream, **AlertOutput**.

Observe que também é possível reutilizar os resultados dos CTEs (expressões de tabela comuns) (como instruções **WITH**) em várias instruções de saída. Essa opção tem o benefício adicional de abrir menos leitores para a fonte de entrada.

Por exemplo: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Exemplo de consulta: Contagem de valores exclusivos

**Descrição**: Contagem do número de valores de campo exclusivos que aparecem no fluxo dentro de uma janela de tempo. Por exemplo, quantas marcas de carro exclusivas passaram pelo pedágio em uma janela de dois segundos?

**Entrada**:

| Faça | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Solução:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Explicação:** 
**COUNT(DISTINCT Marca)** retorna o número de valores distintos na coluna **Marca** dentro de uma janela de tempo.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemplo de consulta: Determinar se um valor foi alterado

**Descrição**: Observe um valor anterior para determinar se é diferente do valor atual. Por exemplo, o carro anterior na rodovia é da mesma marca que o carro atual?

**Entrada**:

| Faça | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Saída**:

| Faça | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Solução**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Explicação**: Use **LAG** para inspecionar um fluxo de entrada do evento anterior e obter o valor **Make**. Em seguida, compare-o ao valor **Marca** no evento atual e retire o evento se eles forem diferentes.

## <a name="query-example-find-the-first-event-in-a-window"></a>Exemplo de consulta: Localizar o primeiro evento em uma janela

**Descrição**: Localize o primeiro carro a cada intervalo de 10 minutos.

**Entrada**:

| PlacaDeCarro | Faça | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Saída**:

| PlacaDeCarro | Faça | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Solução**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

Agora, vamos alterar o problema e encontrar o primeiro carro de uma marca específica em cada intervalo de 10 minutos.

| PlacaDeCarro | Faça | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solução**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Exemplo de consulta: Localizar o último evento em uma janela

**Descrição**: Localize o último carro a cada intervalo de 10 minutos.

**Entrada**:

| PlacaDeCarro | Faça | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Saída**:

| PlacaDeCarro | Faça | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solução**:

```SQL
    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Explicação**: Há duas etapas na consulta. A primeira localiza o carimbo de hora mais recente em uma janela de 10 minutos. A segunda etapa une os resultados da primeira consulta com fluxo original para localizar eventos que correspondem aos últimos carimbos de data e hora em cada janela. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Exemplo de consulta: Localizar eventos correlacionados em um fluxo

**Descrição**: Localizar eventos correlacionados em um fluxo. Por exemplo, dois carros consecutivos da mesma marca entraram na rodovia nos últimos 90 segundos?

**Entrada**:

| Faça | PlacaDeCarro | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Saída**:

| Faça | Time | PlacaDoCarroAtual | PlacaDoPrimeiroCarro | HoraDoPrimeiroCarro |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Solução**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Explicação**: Use **LAG** para inspecionar um fluxo de entrada do evento anterior e obter o valor **Make**. Compare-o ao valor **MAKE** no evento atual e retire o evento se eles forem iguais. Você também pode usar **LAG** para obter dados sobre o carro anterior.

## <a name="query-example-detect-the-duration-between-events"></a>Exemplo de consulta: Detectar a duração entre os eventos

**Descrição**: Localize a duração de um determinado evento. Por exemplo, dada uma sequência de cliques na Web, determine o tempo gasto em um recurso.

**Entrada**:  

| User | Recurso | evento | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Início |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Saída**:  

| User | Recurso | Duração |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Solução**:

```SQL
    SELECT
        [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Explicação**: Use a função **LAST** para recuperar o último valor de **TIME**, quando o tipo de evento era **Start**. Observe que a função **LAST** usa **PARTITION BY [usuário]** para indicar que o resultado foi calculado por usuário exclusivo. A consulta tem um limite máximo de uma hora de diferença de tempo entre os eventos **Start** e **Stop**, mas é configurável como necessária **(LIMIT DURATION(hour, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Exemplo de consulta: Detectar a duração de uma condição
**Descrição**: Descubra por quanto tempo ocorreu uma condição.
Por exemplo, suponha que um bug resultou no peso incorreto de todos os carros (acima de 9.000 quilos). Queremos calcular a duração do bug.

**Entrada**:

| Faça | Time | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Saída**:

| FalhaInicial | FalhaFinal |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Solução**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Explicação**: Use **LAG** para exibir o fluxo de entrada de 24 horas e procurar por instâncias, nas quais **StartFault** e **StopFault** são estendidas pelo peso < 20000.

## <a name="query-example-fill-missing-values"></a>Exemplo de consulta: Preencher valores ausentes

**Descrição**: Para o fluxo de eventos que possuem valores ausentes, produza um fluxo de eventos com intervalos regulares. Por exemplo, gere um evento a cada 5 segundos que relatam o ponto de dados visto mais recentemente.

**Entrada**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**(10 primeiras linhas) de saída**:

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Solução**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Explicação**: Essa consulta gera eventos a cada 5 segundos e gera o último evento recebido anteriormente. A duração da [janela de salto](/stream-analytics-query/hopping-window-azure-stream-analytics) determina quanto tempo a consulta procura localizar o evento mais recente (300 segundos neste exemplo).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Exemplo de consulta: Correlacionar dois tipos de eventos no mesmo fluxo

**Descrição**: Às vezes, os alertas precisam ser gerados com base em vários tipos de eventos que ocorreram em um determinado intervalo de tempo. Por exemplo, no cenário IoT para fornos domésticos, queremos gerar um alerta quando a temperatura do ventilador for inferior a 40 e a potência máxima durante os últimos 3 minutos tiver sido inferior a 10.

**Entrada**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**Saída**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Elementos de aquecimento em curto-circuito" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Elementos de aquecimento em curto-circuito" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Elementos de aquecimento em curto-circuito" |15 |

**Solução**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Explicação**: A primeira consulta `max_power_during_last_3_mins` usa a [Janela deslizante](/stream-analytics-query/sliding-window-azure-stream-analytics) para localizar o valor máximo do sensor de potência de cada dispositivo durante os últimos 3 minutos. A segunda consulta é unida à primeira consulta para localizar o valor de potência na janela mais recente relevante para o evento atual. E, em seguida, desde que as condições sejam atendidas, um alerta é gerado para o dispositivo.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Exemplo de consulta: Processar eventos independentes de Distorção do Relógio do Dispositivo (subfluxos)

**Descrição**: Os eventos podem chegar atrasados ou fora de ordem devido a distorções de relógio entre produtores de eventos, desvios de clock entre partições ou latência de rede. No exemplo a seguir, o relógio do dispositivo para a chamada de Tarifaid 2 é de cinco segundos atrás da chamada 1 e o relógio do dispositivo para o Chamadaid 3 é de dez segundos atrás da chamada 1. 

**Entrada**:

| PlacaDeCarro | Faça | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**Saída**:

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Solução**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Explicação**: A cláusula [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) examina cada linha do tempo do dispositivo separadamente usando subfluxos. Os eventos de saída para cada TollID são gerados como eles são computados, que significa que os eventos estão na ordem em relação a cada TollID em vez de ser reordenadas como se todos os dispositivos foram no relógio do mesmo.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Exemplo de consulta: Remover eventos duplicados em uma janela

**Descrição**: Ao realizar uma operação, como calcular médias sobre eventos em uma determinada janela de tempo, os eventos duplicados devem ser filtrados. No exemplo a seguir, o segundo evento é uma duplicata do primeiro.

**Entrada**:  

| DeviceID | Time | Atributo | Valor |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Saída**:  

| AverageValue | DeviceID |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Solução**:

```SQL
With Temp AS (
    SELECT
        COUNT(DISTINCT Time) AS CountTime,
        Value,
        DeviceId
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Value,
        DeviceId,
        SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Explicação**: [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics) retorna o número de valores distintos na coluna Time dentro de uma janela de tempo. Em seguida, você poderá usar a saída dessa etapa para computar a média por dispositivo, descartando duplicatas.

## <a name="geofencing-and-geospatial-queries"></a>Doisolamento geográfico e consultas geoespaciais
O Azure Stream Analytics fornece funções geoespaciais internas que podem ser usadas para implementar cenários como gerenciamento de frota, compartilhamento de Rides, carros conectados e acompanhamento de ativos. Os dados geoespaciais podem ser ingeridos em formatos geojson ou WKT como parte do fluxo de eventos ou de dados de referência. Para obter mais informações, consulte os cenários de Unificação [geográfica e agregação geoespacial com Azure Stream Analytics](geospatial-scenarios.md) artigo.

## <a name="language-extensibility-through-javascript-and-c"></a>Extensibilidade de linguagem por meio de JavaScript eC#
O Azure Stream Ananlytics Query langugae pode ser estendido com funções personalizadas escritas em C# JavaScript ou linguagens. Para obter mais informações, consulte os artigos foolowing:
* [Azure Stream Analytics funções definidas pelo usuário do JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics agregações definidas pelo usuário do JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Desenvolver .NET Standard funções definidas pelo usuário para trabalhos do Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

