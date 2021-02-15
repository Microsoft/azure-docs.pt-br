---
title: Usar o reparticionamento para otimizar trabalhos de Azure Stream Analytics
description: Este artigo descreve como usar o reparticionamento para otimizar Azure Stream Analytics trabalhos que não podem ser paralelizados.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 72f81a0eac81acdca71c8ed81695789c417898ca
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014188"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Use o reparticionamento para otimizar o processamento com Azure Stream Analytics

Este artigo mostra como usar o reparticionamento para dimensionar sua consulta de Azure Stream Analytics para cenários que não podem ser totalmente [paralelizados](stream-analytics-scale-jobs.md).

Talvez você não consiga usar a [paralelização](stream-analytics-parallelization.md) se:

* Você não controla a chave de partição para seu fluxo de entrada.
* A entrada "pulveriza" de origem em várias partições que posteriormente precisam ser mescladas.

O reparticionamento, ou embaralhando, é necessário quando você processa dados em um fluxo que não é fragmentado de acordo com um esquema de entrada natural, como **PartitionID** para hubs de eventos. Quando você reparticiona, cada fragmento pode ser processado de forma independente, o que permite que você expanda linearmente o pipeline de streaming.

## <a name="how-to-repartition"></a>Como reparticionar

Para reparticionar, use a palavra-chave **into** após uma instrução **Partition by** em sua consulta. O exemplo a seguir particiona os dados por **DeviceID** em uma contagem de partições de 10. O hash de **DeviceID** é usado para determinar qual partição deve aceitar qual Subfluxo. Os dados são liberados de forma independente para cada fluxo particionado, supondo que a saída dê suporte a gravações particionadas e tenha 10 partições.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

A consulta de exemplo a seguir une dois fluxos de dados reparticionados. Ao unir dois fluxos de dados reparticionados, os fluxos devem ter a mesma chave de partição e contagem. O resultado é um fluxo que tem o mesmo esquema de partição.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

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
