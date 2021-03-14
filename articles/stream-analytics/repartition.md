---
title: Usar o reparticionamento para otimizar trabalhos de Azure Stream Analytics
description: Este artigo descreve como usar o reparticionamento para otimizar Azure Stream Analytics trabalhos que não podem ser paralelizados.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182529"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Use o reparticionamento para otimizar o processamento com Azure Stream Analytics

Este artigo mostra como usar o reparticionamento para dimensionar sua consulta de Azure Stream Analytics para cenários que não podem ser totalmente [paralelizados](stream-analytics-scale-jobs.md).

Talvez você não consiga usar a [paralelização](stream-analytics-parallelization.md) se:

* Você não controla a chave de partição para seu fluxo de entrada.
* A entrada "pulveriza" de origem em várias partições que posteriormente precisam ser mescladas.

O reparticionamento, ou embaralhando, é necessário quando você processa dados em um fluxo que não é fragmentado de acordo com um esquema de entrada natural, como **PartitionID** para hubs de eventos. Quando você reparticiona, cada fragmento pode ser processado de forma independente, o que permite que você expanda linearmente o pipeline de streaming. 

## <a name="how-to-repartition"></a>Como reparticionar
Você pode reparticionar sua entrada de duas maneiras:
1. Usar um trabalho de Stream Analytics separado que faz o reparticionamento
2. Use um único trabalho, mas faça o reparticionamento primeiro antes da lógica de análise personalizada

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>Criando um trabalho de Stream Analytics separado para reparticionar a entrada
Você pode criar um trabalho que lê entrada e grava em uma saída do hub de eventos usando uma chave de partição. Esse Hub de eventos pode servir como entrada para outro Stream Analytics trabalho onde você implementa a lógica de análise. Ao configurar essa saída do hub de eventos em seu trabalho, você deve especificar a chave de partição pela qual Stream Analytics reparticionará os dados. 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>Reparticionar a entrada em um único trabalho de Stream Analytics
Você também pode introduzir uma etapa em sua consulta que primeiro reparticiona a entrada e isso pode ser usado por outras etapas em sua consulta. Por exemplo, se você quiser reparticionar a entrada com base em **DeviceID**, sua consulta será:
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

A consulta de exemplo a seguir une dois fluxos de dados reparticionados. Ao unir dois fluxos de dados reparticionados, os fluxos devem ter a mesma chave de partição e contagem. O resultado é um fluxo que tem o mesmo esquema de partição.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

O esquema de saída deve corresponder à chave do esquema de fluxo e a contagem para que cada Subfluxo possa ser liberado de forma independente. O fluxo também pode ser mesclado e reparticionado novamente por um esquema diferente antes de ser liberado, mas você deve evitar esse método porque ele adiciona à latência geral do processamento e aumenta a utilização de recursos.

## <a name="streaming-units-for-repartitions"></a>Unidades de streaming para repartições

Experimente e observe o uso de recursos de seu trabalho para determinar o número exato de partições de que você precisa. O número de [unidades de streaming (Su)](stream-analytics-streaming-unit-consumption.md) deve ser ajustado de acordo com os recursos físicos necessários para cada partição. Em geral, seis SUs são necessários para cada partição. Se houver recursos insuficientes atribuídos ao trabalho, o sistema só aplicará a repartição se ele beneficiar o trabalho.

## <a name="repartitions-for-sql-output"></a>Repartições para saída SQL

Quando seu trabalho usa o banco de dados SQL para saída, use o reparticionamento explícito para corresponder à contagem de partições ideal para maximizar a taxa de transferência. Como o SQL funciona melhor com oito gravadores, reparticionar o fluxo para oito antes de liberar, ou em outro lugar, pode beneficiar o desempenho do trabalho. 

Quando há mais de oito partições de entrada, a herança de entrada de esquema de particionamento pode não ser uma opção apropriada. Considere o [uso do em em](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) sua consulta para especificar explicitamente o número de gravadores de saída. 

O exemplo a seguir lê a partir da entrada, independentemente de ser particionado naturalmente, e reparticiona o fluxo tenfold de acordo com a dimensão DeviceID e libera os dados para a saída. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Aproveitar a paralelização de consultas no Azure Stream Analytics](stream-analytics-parallelization.md)
