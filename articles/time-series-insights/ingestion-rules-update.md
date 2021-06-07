---
title: Alterações futuras nas regras de ingestão e nivelamento no Azure Time Series Insights Gen2 | Microsoft Docs
description: Alterações nas regras de ingestão
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 44553e2a57f796dcf48561ff3b273e25514247ae
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504525"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Alterações futuras nas regras de nivelamento e de escape de JSON para novos ambientes

> [!IMPORTANT]
> As alterações serão aplicadas apenas aos ambientes *criados recentemente* do Microsoft Azure Time Series Insights Gen2. As alterações não se aplicam aos ambientes Gen1.

O ambiente do Azure Time Series Insights Gen2 cria dinamicamente colunas de armazenamento, seguindo um conjunto específico de convenções de nomenclatura. Quando um evento é ingerido, o Time Series Insights aplica um conjunto de regras ao conteúdo JSON e aos nomes de propriedades. Alterações no nivelamento e armazenamento de dados JSON entraram em vigor em julho de 2020 para ambientes novos do Azure Time Series Insights Gen2. Essa alteração afeta você nos seguintes casos:

* O conteúdo de JSON contém objetos aninhados.
* O conteúdo de JSON contém matrizes.
* Você usa algum dos quatro caracteres especiais a seguir no nome de uma propriedade JSON: `[` `\` `.` `'`
* Uma ou mais das propriedades de ID de TS (série temporal) estão dentro de um objeto aninhado.

Se você criar um ambiente, e a carga do evento se encaixar em uma ou mais das situações acima, os dados serão nivelados e armazenados de maneira diferente. A tabela a seguir resume as mudanças:

| Regra atual | Nova regra | JSON de exemplo | Nome da coluna anterior | Nome da nova coluna
|---|---| ---| ---|  ---|
| Para nivelar JSON aninhado, você usa o caractere de sublinhado como delineador. |Para nivelar JSON aninhado, você usa o ponto final como delineador.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Não é feito o escape de caracteres especiais. | O escape dos nomes de propriedades JSON que incluem os caracteres especiais `.` `[`  `\` e `'` é feito usando `['` e `']`. Em `['` e `']`, há o escape adicional de aspas simples e barras invertidas. A aspa simples é escrita como `\'`, e a barra invertida, como `\\`.  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| As matrizes de primitivos são armazenadas como uma cadeia de caracteres. | As matrizes de tipos primitivos são armazenadas como um tipo dinâmico.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
As matrizes de objetos são sempre niveladas, produzindo vários eventos. | Se os objetos na matriz não tiverem as propriedades ID de TS ou carimbo de data/hora, a matriz de objetos será totalmente armazenada como um tipo dinâmico. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Alterações recomendadas para novos ambientes

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se propriedade de ID de TS e/ou de carimbo de data/hora está aninhada em um objeto

As novas implantações precisam seguir as novas regras de ingestão. Por exemplo, se a ID de TS é `telemetry_tagId`, você precisa atualizar todos os modelos do Azure Resource Manager ou scripts de implantação automatizados para configurar `telemetry.tagId` como a ID de TS do ambiente. Você também precisa fazer essa alteração em carimbos de data/hora de origem de evento em JSON aninhado.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se o conteúdo contém caracteres JSON ou especiais aninhados, e você automatiza a criação de expressões de variável do [modelo de série temporal](./concepts-model-overview.md)

Atualize o código do cliente que executa o [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) de acordo com as novas regras de ingestão. Por exemplo, você deve atualizar uma [expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax) anterior de `"value": {"tsx": "$event.series_value.Double"}` para uma das seguintes opções:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [armazenamento e a entrada do Azure Time Series Insights Gen2](./concepts-ingestion-overview.md).

* Saiba como consultar dados usando [APIs de consulta de série temporal](./concepts-query-overview.md).

* Leia mais sobre a [nova sintaxe de expressão de séries temporais](/rest/api/time-series-insights/reference-time-series-expression-syntax).