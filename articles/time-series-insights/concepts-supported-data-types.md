---
title: Tipos de dados com suporte-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre os tipos de dados com suporte no Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: b0536a152797d17cba0930b3a142a7fb92eaf5ea
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685150"
---
# <a name="supported-data-types"></a>Tipos de dados com suporte

A tabela a seguir lista os tipos de dados com suporte pelo Azure Time Series Insights Gen2

| Tipo de dados | Descrição | Exemplo | [Sintaxe de expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Nome da coluna de propriedade em parquet
|---|---|---|---|---|
| **bool** | Um tipo de dados com um dos dois estados: `true` ou `false`. | `"isQuestionable" : true` | `$event.isQuestionable.Bool` ou `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | Representa um momento no tempo, geralmente expresso como uma data e hora do dia. Expresso no formato [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). As propriedades DateTime são sempre armazenadas no formato UTC. Os deslocamentos de fuso horário, se formatados corretamente, serão aplicados e, em seguida, o valor armazenado em UTC. Consulte [esta](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) seção para obter mais informações sobre a propriedade timestamp do ambiente e os deslocamentos de DateTime | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Se "eventProcessedLocalTime" for o carimbo de data/hora da origem do evento: `$event.$ts` . Se for outra propriedade JSON: `$event.eventProcessedLocalTime.DateTime` ou `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | Um número de precisão dupla de 64 bits  | `"value": 31.0482941` | `$event.value.Double` ou `$event['value'].Double` |  `value_double`
| **longo** | Um inteiro de 64 bits assinado  | `"value" : 31` | `$event.value.Long` ou `$event['value'].Long` |  `value_long`
| **cadeia de caracteres** | Os valores de texto devem consistir em UTF-8 válido. Cadeias de caracteres nulas e vazias são tratadas da mesma. |  `"site": "DIM_MLGGG"`| `$event.site.String` ou `$event['site'].String`| `site_string`
| **dinâmico** | Um tipo complexo (não primitivo) que consiste em uma matriz ou um recipiente de Propriedades (dicionário). No momento, apenas as matrizes JSON em cadeias de primitivos ou matrizes de objetos que não contêm a propriedade de TS ID ou Timestamp (s) serão armazenadas como dinâmicas. Leia este [artigo](./concepts-json-flattening-escaping-rules.md) para entender como os objetos serão mesclados e as matrizes podem ser desvertidas. As propriedades de carga armazenadas como esse tipo só podem ser acessadas selecionando `Explore Events` no time Series insights Explorer para exibir eventos brutos ou por meio da [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API de consulta para análise do lado do cliente. |  `"values": "[197, 194, 189, 188]"` | A referência a tipos dinâmicos em uma expressão de série temporal ainda não tem suporte | `values_dynamic`

> [!NOTE]
> Há suporte para valores inteiros de 64 bits, mas o maior número que o Azure Time Series Insights Explorer pode expressar com segurança é 9.007.199.254.740.991 (2 ^ 53-1) devido a limitações de JavaScript. Se você trabalhar com números em seu modelo de dados acima disso, poderá reduzir o tamanho criando uma [variável de modelo de série temporal](./concepts-variables.md#numeric-variables) e [convertendo](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) o valor.

> [!NOTE]
> O tipo de **cadeia de caracteres** não permite valor nulo:
>
> * Uma [expressão de série temporal (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) expressa em uma [consulta de série temporal](/rest/api/time-series-insights/reference-query-apis) que compara o valor de uma cadeia de caracteres vazia (**' '**) com **NULL** se comportará da mesma maneira: `$event.siteid.String = NULL` é equivalente a `$event.siteid.String = ''` .
> * A API pode retornar valores **nulos** mesmo se os eventos originais contivessem cadeias de caracteres vazias.
> * Não assuma a dependência de valores **nulos** em colunas de **cadeia de caracteres** para fazer comparações ou avaliações, tratá-los da mesma maneira que as cadeias de caracteres vazias.

## <a name="sending-mixed-data-types"></a>Enviando tipos de dados mistos

Seu ambiente de Azure Time Series Insights Gen2 é fortemente tipado. Se dispositivos ou marcas enviarem dados de tipos diferentes para uma propriedade de dispositivo, os valores serão armazenados em duas colunas separadas e a [função de adesão ()](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) deverá ser usada ao definir suas expressões de variáveis de modelo de série temporal em chamadas à API.

O Azure Time Series Insights Explorer oferece uma maneira de fazer a União automática das colunas separadas da mesma propriedade de dispositivo. No exemplo a seguir, o sensor envia uma `PresentValue` propriedade que pode ser longa ou dupla. Para consultar todos os valores armazenados (independentemente do tipo de dados) da `PresentValue` propriedade, escolha `PresentValue (Double | Long)` e as colunas serão Unidas para você.

[![União automática do Gerenciador](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objetos e matrizes

Você pode enviar tipos complexos, como objetos e matrizes, como parte da carga do evento. Os objetos aninhados serão mesclados e as matrizes serão armazenadas como `dynamic` ou achatadas para produzir vários eventos, dependendo da sua configuração de ambiente e da forma JSON. Para saber mais sobre as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Próximas etapas

* Leia as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados.

* Entenda as limitações de [taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente

* Saiba mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para ingerir dados de streaming.
