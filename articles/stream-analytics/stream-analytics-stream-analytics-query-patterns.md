---
title: Padrões de consulta comuns no Azure Stream Analytics
description: Este artigo descreve vários designs e padrões de consulta comuns que são úteis em trabalhos do Azure Stream Analytics.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019985"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Padrões de consulta comuns no Azure Stream Analytics

As consultas no Azure Stream Analytics são expressas em uma linguagem de consulta parecida com o SQL. Essas construções de linguagem estão documentadas no guia [Referência de linguagem de consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). 

O design da consulta pode expressar a lógica de passagem simples para mover dados de evento de um fluxo de entrada para armazenamento de dados de saída, ou ele pode fazer uma correspondente de padrões avançada e uma análise temporal para calcular agregações entre várias janelas de tempo como no guia [Criar uma solução IoT usando o Stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md). Você pode associar dados de várias entradas para combinar eventos de fluxo contínuo, além de fazer pesquisas nos dados de referência estática para enriquecer os valores de evento. Você também pode gravar dados para várias saídas.

Este artigo descreve soluções para vários padrões comuns de consulta com base em cenários do mundo real.

## <a name="supported-data-formats"></a>Formatos de dados com suporte

O Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro.

JSON e Avro podem conter tipos complexos, como matrizes ou objetos aninhados (registros). Para obter mais informações sobre como trabalhar com esses tipos de dados complexos, consulte o artigo [Análise de JSON e AVRO](stream-analytics-parsing-json.md).

## <a name="send-data-to-multiple-outputs"></a>Enviar dados para várias saídas

É possível usar várias instruções **SELECT** usadas para gerar dados de saída para diferentes coletores de saída. Por exemplo, uma instrução **SELECT** pode gerar um alerta baseado em limite, enquanto outra pode gerar eventos para o armazenamento de blobs.

**Entrada**:

| Faça | Hora |
| --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |
| Marca1 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:01.0000000Z |
| Marca2 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:03.0000000Z |

**Saída ArchiveOutput**:

| Faça | Hora |
| --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |
| Marca1 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:01.0000000Z |
| Marca2 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:03.0000000Z |

**Saída AlertOutput**:

| Faça | Hora | Contagem |
| --- | --- | --- |
| Marca2 |2015-01-01T00:00:10.0000000Z |3 |

**Consulta**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
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

A cláusula **INTO** informa ao Stream Analytics em qual das saídas gravar os dados. A primeira **SELECT** define uma consulta passagem que recebe dados da entrada e os envia para a saída chamada **ArchiveOutput**. A segunda consulta faz uma agregação e uma filtragem simples antes de enviar os resultados a uma saída do sistema de alerta de downstream chamada **AlertOutput**.

Note que é possível usar a cláusula **WITH** para definir vários blocos de subconsultas. Essa opção tem o benefício de abrir menos leitores para a fonte de entrada.

**Consulta**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Para obter mais informações, consulte a [cláusula **WITH**](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Consulta passagem simples

É possível usar uma consulta de passagem simples para copiar os dados de fluxo de entrada para a saída. Por exemplo, se um fluxo de dados contendo informações em tempo real de veículos precisar ser salvo em uma análise de carta, uma simples consulta de passagem fará isso.

**Entrada**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Marca1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Saída**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Marca1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Consulta**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Uma consulta **SELECT** * projeta todos os campos de um evento de entrada e os envia para a saída. Da mesma forma, também é possível usar a **SELECT** para projetar apenas os campos obrigatórios da entrada. Nesse exemplo, se os campos *Modelo* e *Tempo* do veículo forem os únicos campos obrigatórios a serem salvos, eles podem ser especificados na instrução **SELECT**.

**Entrada**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |1000 |
| Marca1 |2015-01-01T00:00:02.0000000Z |2000 |
| Marca2 |2015-01-01T00:00:04.0000000Z |1500 |

**Saída**:

| Faça | Hora |
| --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |
| Marca1 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:04.0000000Z |

**Consulta**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Correspondência de cadeia de caracteres com LIKE e NOT LIKE

**LIKE** e **NOT LIKE** podem ser usados para verificar se um campo corresponde a um determinado padrão. Por exemplo, um filtro pode ser criado para retornar apenas as placas que começam com a letra “A” e terminam com o número 9.

**Entrada**:

| Faça | License_plate | Hora |
| --- | --- | --- |
| Marca1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Marca2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Marca3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Saída**:

| Faça | License_plate | Hora |
| --- | --- | --- |
| Marca2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Marca3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Consulta**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Use a instrução **LIKE** para verificar o valor do campo **License_plate**. Ele deve começar com a letra “A”, ter uma qualquer cadeia de caracteres com nenhum ou mais caracteres, e terminar com o número 9.

## <a name="calculation-over-past-events"></a>Cálculo de eventos passados

É possível usar a função **LAG** para examinar eventos passados dentro de uma janela de tempo e compará-los ao evento atual. Por exemplo, a marca do carro atual poderá ser gerada se for diferente do último carro que passou pelo pedágio.

**Entrada**:

| Faça | Hora |
| --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |
| Marca2 |2015-01-01T00:00:02.0000000Z |

**Saída**:

| Faça | Hora |
| --- | --- |
| Marca2 |2015-01-01T00:00:02.0000000Z |

**Consulta**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Use **LAG** para espiar o fluxo de entrada de um evento atrás, recuperando o valor *Marca* e comparando-o ao valor *Marca* do evento atual e gerar o evento.

Para obter mais informações, consulte [**LAG**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Retorna o último evento de uma janela

Conforme os eventos são consumidos pelo sistema em tempo real, não há nenhuma função que possa determinar se um evento será o último a chegar naquela janela de tempo. Para isso, o fluxo de entrada precisa ser unido a outro no ponto em que a hora de um evento seja o tempo máximo para todos os eventos nessa janela.

**Entrada**:

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Marca1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Marca3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Marca1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Marca2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Marca2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Marca1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Marca4 |2015-07-27T00:13:45.0000000Z |

**Saída**:

| License_plate | Faça | Hora |
| --- | --- | --- |
| VFE 1616 |Marca2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Marca4 |2015-07-27T00:13:45.0000000Z |

**Consulta**:

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
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

A primeira etapa na consulta localiza o carimbo de data/hora máximo em janelas de 10 minutos, o qual é o carimbo de data/hora do último evento dessa janela. A segunda etapa une os resultados da primeira consulta com fluxo original para localizar o evento que corresponda aos últimos carimbos de data/hora em cada janela. 

**DATEDIFF** é uma função específica de datas que compara e retorna a diferença de tempo entre dois campos DateTime. Para obter mais informações, consulte [funções de data](/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Para obter mais informações sobre a união de fluxos, consulte [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Agregação de dados ao longo do tempo

Para computar informações durante uma janela de tempo, os dados podem ser agregados juntos. Neste exemplo, uma contagem é calculada nos últimos 10 segundos de tempo para cada marca de carro específica.

**Entrada**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |1000 |
| Marca1 |2015-01-01T00:00:02.0000000Z |2000 |
| Marca2 |2015-01-01T00:00:04.0000000Z |1500 |

**Saída**:

| Faça | Contagem |
| --- | --- |
| Marca1 | 2 |
| Marca2 | 1 |

**Consulta**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Essa agregação agrupa os carros por *Modelo* e os conta a cada 10 segundos. A saída apresenta o *Modelo* e a *Contagem* de carros que passaram pelo pedágio.

TumblingWindow é uma função de exibição de janelas usada para agrupar eventos. As agregações podem ser aplicadas em todos os eventos agrupados. Para saber mais, confira [funções de exibição de janelas](stream-analytics-window-functions.md).

Para obter mais informações sobre a agregação, consulte [funções de agregação](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="periodically-output-values"></a>Valores de saída periódica

Caso haja eventos irregulares ou ausentes, uma saída de intervalo regular pode ser gerada a partir de uma entrada de dados mais esparsa. Por exemplo, gere um evento a cada 5 segundos que relatam o ponto de dados visto mais recentemente.

**Entrada**:

| Hora | Valor |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**(10 primeiras linhas) de saída**:

| Window_end | Last_event.Time | Last_event.Value |
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

**Consulta**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Essa consulta gera eventos a cada 5 segundos e gera o último evento recebido anteriormente. A duração da **HOPPINGWINDOW** determina por quanto tempo a consulta tenta encontrar o evento mais recente.

Para obter mais informações, consulte [Janela de salto](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Correlacionar eventos presentes em um fluxo

É possível correlacionar eventos no mesmo fluxo por meio da análise de eventos passados usando a função **LAG**. Por exemplo, uma saída pode ser gerada sempre que dois carros consecutivos da mesma *Marca* passarem pelo pedágio nos últimos 90 segundos.

**Entrada**:

| Faça | License_plate | Hora |
| --- | --- | --- |
| Marca1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Marca1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Marca2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Marca1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Saída**:

| Faça | Hora | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Marca1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Consulta**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

A função **LAG** pode examinar o fluxo de entrada do evento anterior e recuperar o valor da *Marca*, comparando-o com o valor da *Marca* do evento atual.  Assim que a condição for atendida, os dados do evento anterior poderão ser projetados usando a **LAG** na instrução **SELECT**.

Para obter mais informações, consulte [LAG](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Detectar a duração entre os eventos

A duração de um evento pode ser computada ao verificar o último evento de Início assim que um evento de Término for recebido. Essa consulta pode ajudar a determinar o tempo que um usuário gasta em uma página ou em um recurso.

**Entrada**:  

| Usuário | Recurso | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Iniciar |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Saída**:  

| Usuário | Recurso | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Consulta**:

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

É possível usar a função **LAST** para recuperar o último evento dentro de uma condição específica. Nesse exemplo, a condição é um evento do tipo Início, particionando a pesquisa por **PARTITION BY** usuário e recurso. Dessa forma, cada usuário e recurso é tratado de modo independente ao pesquisar o evento de Início. **LIMIT DURATION** limita a hora de pesquisa em 1 hora atrás entre os eventos de Término e de Início.

## <a name="count-unique-values"></a>Contagem de valores exclusivos

É possível usar **COUNT** e **DISTINCT** para contar a quantidade de valores de campo exclusivos que aparecem no fluxo em uma janela de tempo. Uma consulta pode ser criada para calcular quantas *Marcas* de carro exclusivas passaram por uma cabine de pedágio em uma janela de dois segundos.

**Entrada**:

| Faça | Hora |
| --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |
| Marca1 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:01.0000000Z |
| Marca2 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:03.0000000Z |

**Saída:**

| Count_make | Hora |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Consulta:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** retorna a contagem de valores distintos na coluna **Marca** dentro de uma janela de tempo.
Para obter mais informações, consulte a [função de agregação **COUNT**](/stream-analytics-query/count-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Recuperar o primeiro evento em uma janela

É possível usar o **IsFirst** para recuperar o primeiro evento em uma janela de tempo. Por exemplo, a saída das informações do primeiro carro em cada intervalo de 10 minutos.

**Entrada**:

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Marca1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Marca3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Marca1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Marca2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Marca2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Marca1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Marca4 |2015-07-27T00:13:45.0000000Z |

**Saída**:

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Marca1 |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Marca1 |2015-07-27T00:12:02.0000000Z |

**Consulta**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** também pode particionar os dados e calcular o primeiro evento para cada *Marca* de carro específica encontrada em cada intervalo de 10 minutos.

**Saída**:

| License_plate | Faça | Hora |
| --- | --- | --- |
| DXE 5291 |Marca1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Marca3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Marca2 |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Marca1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Marca4 |2015-07-27T00:13:45.0000000Z |

**Consulta**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Para obter mais informações, consulte [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="remove-duplicate-events-in-a-window"></a>Remover eventos duplicados em uma janela

Ao realizar uma operação, como calcular médias sobre eventos em uma determinada janela de tempo, os eventos duplicados devem ser filtrados. No exemplo a seguir, o segundo evento é uma cópia do primeiro.

**Entrada**:  

| deviceId | Hora | Atributo | Valor |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Saída**:  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Consulta**:

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

**COUNT(DISTINCT Time)** retorna o número de valores distintos na coluna Time dentro de uma janela de tempo. A saída da primeira etapa pode ser usada para computar a média por dispositivo, descartando cópias.

Para obter mais informações, consulte [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Especifique a lógica para casos/valores diferentes (instruções CASE)

Instruções **CASE** podem fornecer cálculos diferentes para campos diferentes com base em um critério específico. Por exemplo, atribua a pista “A” a carros da *Marca1* e a pista “B” para qualquer outro modelo.

**Entrada**:

| Faça | Hora |
| --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |
| Marca2 |2015-01-01T00:00:02.0000000Z |
| Marca2 |2015-01-01T00:00:03.0000000Z |

**Saída**:

| Faça |Dispatch_to_lane | Hora |
| --- | --- | --- |
| Marca1 |"A" |2015-01-01T00:00:01.0000000Z |
| Marca2 |"B" |2015-01-01T00:00:02.0000000Z |

**Solução**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

A expressão **CASE** compara uma expressão com um conjunto de expressões simples para determinar seu resultado. Nesse exemplo, os veículos da *Marca1* são enviados para a pista “A”, enquanto os veículos de qualquer outro modelo serão atribuídos à pista “B”.

Para obter mais informações, consulte a [expressão CASE](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Conversão de dados

Os dados podem ser convertidos em tempo real por meio do método **CAST**. Por exemplo, o peso do carro pode ser convertido do tipo **nvarchar (max)** para o tipo **bigint** e ser usado em um cálculo numérico.

**Entrada**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Marca1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Saída**:

| Faça | Peso |
| --- | --- |
| Marca1 |3000 |

**Consulta**:

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

Use uma instrução **CAST** para especificar o tipo de dados. Veja a lista de tipos de dados com suporte em [Tipos de dados (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Para obter mais informações sobre [funções de conversão de dados](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>Detectar a duração de uma condição

Para condições que abrangem vários eventos, é possível usar a função **LAG** para identificar a duração dessa condição. Por exemplo, suponha que um bug resultou no peso incorreto de todos os carros (acima de 9.000 quilos). Queremos calcular a duração do bug.

**Entrada**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Marca1 |2015-01-01T00:00:01.0000000Z |2000 |
| Marca2 |2015-01-01T00:00:02.0000000Z |25000 |
| Marca1 |2015-01-01T00:00:03.0000000Z |26000 |
| Marca2 |2015-01-01T00:00:04.0000000Z |25000 |
| Marca1 |2015-01-01T00:00:05.0000000Z |26000 |
| Marca2 |2015-01-01T00:00:06.0000000Z |25000 |
| Marca1 |2015-01-01T00:00:07.0000000Z |26000 |
| Marca2 |2015-01-01T00:00:08.0000000Z |2000 |

**Saída**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Consulta**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
A primeira instrução **SELECT** correlaciona a medição de peso atual com a medida anterior, projetando-a junto com a medição atual. A segunda **SELECT** verifica o último evento em que o *previous_weight* é menor que 20.000, em que o peso atual é menor que 20.000 e o *previous_weight* do evento atual era maior que 20.000.

End_fault é o evento sem falha atual no qual o evento anterior tinha falha, e Start_fault é o último evento sem falha antes disso.

## <a name="process-events-with-independent-time-substreams"></a>Processar eventos com hora independente (subfluxos)

Os eventos podem chegar atrasados ou fora de ordem devido a distorções de relógio entre produtores de eventos, desvios de clock entre partições ou latência de rede.
Por exemplo, o relógio do dispositivo para *TollID* 2 está cinco segundos atrás do *TollID* 1, e o relógio do dispositivo *TollID* 3 está cinco segundos atrás do *TollID* 1. Uma computação pode ocorrer de modo independente para cada pedágio, considerando apenas seus próprios dados de relógio como um carimbo de data/hora.

**Entrada**:

| PlacaDeCarro | Faça | Hora | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Marca1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Marca2 |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Marca1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Marca3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Marca2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Marca1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Marca3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Marca4 |2015-07-27T00:00:07.0000000Z | 3 |

**Saída**:

| TollID | Contagem |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Consulta**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

A cláusula **TIMESTAMP BY OVER** examina cada linha do tempo do dispositivo de modo independente usando subfluxos. O evento de saída para cada *TollID* é gerado conforme é computado, o que significa que os eventos estão na ordem em relação a cada *TollID* em vez de ser reordenados como se todos os dispositivos estivessem no mesmo relógio.

Para obter mais informações, consulte [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="session-windows"></a>Janela de sessão

Uma Janela de sessão é uma janela que continua se expandindo conforme os eventos ocorrem e fecha para computação caso nenhum evento seja recebido após um período específico ou caso a janela atinja sua duração máxima.
Essa janela ajuda bastante ao computar dados de interação do usuário. Uma janela é iniciada quando um usuário começa a interagir com o sistema e é fechada quando não forem observados mais eventos, o que significa que o usuário parou de interagir.
Por exemplo, um usuário está interagindo com uma página da Web em que o número de cliques é registrado, uma Janela de sessão pode ser usada para descobrir por quanto tempo o usuário interagiu com o site.

**Entrada**:

| User_id | Hora | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**Saída**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Consulta**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

A **SELECT** projeta os dados relevantes para a interação do usuário, juntamente com a duração da interação. Agrupar os dados por usuário e uma **SessionWindow** que fecha se nenhuma interação ocorrer dentro de 1 minuto, com um tamanho máximo de janela de 60 minutos.

Para obter mais informações sobre **SessionWindow**, consulte [Janela de sessão](/stream-analytics-query/session-window-azure-stream-analytics).

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Extensibilidade de linguagem com função definida pelo usuário em JavaScript e C#

A linguagem de consulta do Azure Stream Analytics pode ser estendida com funções personalizadas escritas em JavaScript ou linguagem C#. As Funções definidas pelo usuário (UDF) são computações personalizadas/complexas que não podem ser facilmente expressas usando a linguagem **SQL**. É possível definir essas UDFs uma vez e usá-las várias vezes em uma consulta. Por exemplo, é possível usar uma UDF para converter um valor hexadecimal *nvarchar(max)* em um valor *bigint*.

**Entrada**:

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Saída**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

A UDF computará o valor *bigint* do HexValue em todos os eventos consumidos.

Para obter mais informações, consulte [JavaScript](./stream-analytics-javascript-user-defined-functions.md) e [C#](./stream-analytics-edge-csharp-udf.md).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Correspondência de padrões avançados com MATCH_RECOGNIZE

**MATCH_RECOGNIZE** é um mecanismo de correspondência de padrões avançados que pode ser usado para corresponder uma sequência de eventos a um padrão de expressão regular bem-definido.
Por exemplo, um ATM está sendo monitorado em tempo real em busca de falhas e caso haja duas mensagens de aviso consecutivas durante sua operação, o administrador precisará ser notificado.

**Entrada**:

| ATM_id | Operation_id | Return_Code | Hora |
| --- | --- | --- | --- |
| 1 | "Inserção do PIN" | "Success" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Abertura do slot de dinheiro" | "Success" | 2017-01-26T00:10:07.0000000Z |
| 2 | "Fechamento do slot de dinheiro" | "Success" | 2017-01-26T00:10:11.0000000Z |
| 1 | "Inserção da quantidade de retirada" | "Success" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Abertura do slot de dinheiro" | "Warning" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Impressão do saldo bancário" | "Warning" | 2017-01-26T00:10:19.0000000Z |

**Saída**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Abertura do slot de dinheiro" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Essa consulta corresponde a ao menos dois eventos de falha consecutivos e gerará um alarme quando as condições forem atendidas.
**PATTERN** define a expressão regular a ser usada na correspondência que, nesse caso, é qualquer quantidade de operações bem-sucedidas seguidas por pelo menos duas falhas consecutivas.
O sucesso e a falha são definidos usando o valor Return_Code e, depois que a condição for atendida, as **MEASURES** são projetadas com a *ATM_id*, a primeira operação de aviso e a primeira hora de aviso.

Para obter mais informações, consulte [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Consultas de delimitação geográfica e geoespaciais

O Azure Stream Analytics fornece funções geoespaciais internas que podem ser usadas para implementar cenários como gerenciamento de frota, compartilhamento de caronas, carros conectados e acompanhamento de ativos.
Os dados geoespaciais podem ser ingeridos em formatos GeoJSON ou WKT como parte do fluxo de eventos ou de dados de referência.
Por exemplo, uma empresa especializada na fabricação de máquinas para a impressão de passaportes que concede suas máquinas para governos e consulados. A localização dessas máquinas é muito controlada para evitar extravios e seu possível uso para a falsificação de passaportes. Cada máquina vem equipada com um rastreador GPS, cujas informações são retransmitidas para um trabalho do Azure Stream Analytics.
O fabricante gostaria de monitorar a localização dessas máquinas e ser alertado caso uma delas saia de uma área autorizada. Dessa forma, ele poderá desabilitá-las remotamente, alertar as autoridades e recuperar o equipamento.

**Entrada**:

| Equipment_id | Equipment_current_location | Hora |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**Entrada de dados de referência**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Saída**:

| Equipment_id | Equipment_alert_location | Hora |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

A consulta permite que o fabricante monitore a localização das máquinas automaticamente, recebendo alertas quando uma delas deixar a delimitação geográfica permitida. A função geoespacial interna permite aos usuários usarem dados de GPS dentro da consulta sem bibliotecas de terceiros.

Para obter mais informações, consulte o artigo [Cenários de agregação de delimitação geográfica e geoespaciais com o Azure Stream Analytics](geospatial-scenarios.md).

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente a nossa [página de Perguntas e respostas da Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)