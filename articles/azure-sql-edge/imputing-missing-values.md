---
title: Preenchendo lacunas de tempo e entrada valores ausentes – Azure SQL Edge
description: Saiba mais sobre como preencher lacunas de tempo e entrada valores ausentes no Azure SQL Edge
keywords: Borda do SQL, timeseries
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185582"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Preenchendo intervalos de tempo e entrada valores ausentes 

Ao lidar com dados de série temporal, muitas vezes é possível que os dados de série temporal tenham valores ausentes para os atributos. Também é possível que, por causa da natureza dos dados, ou devido a interrupções na coleta de dados, haja *lacunas* de tempo no DataSet.

Por exemplo, ao coletar estatísticas de uso de energia para um dispositivo inteligente, sempre que o dispositivo não estiver operacional, haverá lacunas nas estatísticas de uso. Da mesma forma, em um cenário de coleta de dados de telemetria de máquina, é possível que os sensores diferentes sejam configurados para emitir dados em frequências diferentes, resultando em valores ausentes para os sensores. Por exemplo, se houver dois sensores, tensão e pressão, configurados a uma frequência de 100 Hz e 10 Hz, respectivamente, o sensor de tensão emitirá dados a cada um dos segundos, enquanto o sensor de pressão só emitirá dados a cada um décimo de segundo.

A tabela a seguir descreve um conjunto de uma telemetria do computador, que foi coletado em um intervalo de um segundo. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Há duas características importantes do DataSet anterior. 

- O DataSet não contém nenhum ponto de dados relacionado a vários carimbos de data/hora,,, `2020-09-07 06:14:47.000` `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` e `2020-09-07 06:14:55.000` . Esses carimbos de data/hora são *lacunas* no DataSet.  
- Há valores ausentes, representados como `null` , para a tensão e as leituras de pressão. 

## <a name="gap-filling"></a>Preenchimento de lacuna 

O preenchimento de lacunas é uma técnica que ajuda a criar um conjunto de carimbos de data/hora contíguo e ordenado para facilitar a análise dos dados de série temporal. No Azure SQL Edge, a maneira mais fácil de preencher lacunas no conjunto de tempo da série temporal é definir uma tabela temporária com a distribuição de tempo desejada e, em seguida, fazer uma `LEFT OUTER JOIN` `RIGHT OUTER JOIN` operação ou na tabela do conjunto de um. 

Ao obter os `MachineTelemetry` dados representados acima como exemplo, a consulta a seguir pode ser usada para gerar um conjunto de carimbos de data/hora contíguos e classificados para análise. 

> [!NOTE]
> A consulta abaixo gera as linhas ausentes, com os valores de carimbo de data/hora e os `null` valores dos atributos. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
A consulta acima produz a saída a seguir que contém todos os carimbos de data/hora de *um segundo* no intervalo especificado.

Aqui está o conjunto de resultados

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Como acrescentar valores ausentes

A consulta anterior gerou os carimbos de data/hora ausentes para análise de dados, mas não substituiu nenhum dos valores ausentes (representados como nulos) para `voltage` e `pressure` leituras. No Azure SQL Edge, uma nova sintaxe foi adicionada ao T-SQL `LAST_VALUE()` e às `FIRST_VALUE()` funções, que fornecem aos mecanismos para imputar valores ausentes, com base nos valores anteriores ou seguintes no conjunto de DataSet. 

A nova sintaxe adiciona `IGNORE NULLS` e `RESPECT NULLS` cláusula às `LAST_VALUE()` funções e `FIRST_VALUE()` . Uma consulta a seguir no `MachineTelemetry` conjunto de um computa os valores ausentes usando a função last_value, onde os valores ausentes são substituídos pelo último valor observado no conjunto de um.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Aqui está o conjunto de resultados

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

A consulta a seguir imputes os valores ausentes usando o `LAST_VALUE()` e a `FIRST_VALUE` função. Para, a coluna de saída `ImputedVoltage` os valores ausentes são substituídos pelo último valor observado, enquanto que, para a coluna de saída, `ImputedPressure` os valores ausentes são substituídos pelo próximo valor observado no conjunto de resultados. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Aqui está o conjunto de resultados

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> A consulta acima usa a `FIRST_VALUE()` função para substituir valores ausentes pelo próximo valor observado. O mesmo resultado pode ser obtido usando a `LAST_VALUE()` função com uma `ORDER BY <ordering_column> DESC` cláusula.

## <a name="next-steps"></a>Próximas etapas 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Funções de agregação (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)