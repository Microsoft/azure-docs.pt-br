---
title: Alterações futuras nas regras de ingestão e mesclagem no Azure Time Series Insights Gen2 | Microsoft Docs
description: Alterações da regra de ingestão
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995226"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Alterações futuras nas regras de saída e mesclagem JSON para novos ambientes

> [!IMPORTANT]
> Essas alterações serão aplicadas a apenas os ambientes de Microsoft Azure *criados recentemente* Time Series insights Gen2. As alterações não se aplicam a ambientes Gen1.

O ambiente do Azure Time Series Insights Gen2 cria dinamicamente suas colunas de armazenamento, seguindo um conjunto específico de convenções de nomenclatura. Quando um evento é ingerido, Time Series Insights aplica um conjunto de regras para a carga JSON e nomes de propriedade. Alterações na forma como os dados JSON são achatados e armazenados entrarão em vigor para novos Azure Time Series Insights ambientes de Gen2 em julho de 2020. Essa alteração afeta você nos seguintes casos:

* Seu conteúdo JSON contém objetos aninhados.
* Seu conteúdo JSON contém matrizes.
* Você usa qualquer um dos quatro caracteres especiais a seguir em um nome de propriedade JSON: `[` `\` `.``'`
* Uma ou mais das suas propriedades de ID de TS (série temporal) estão dentro de um objeto aninhado.

Se você criar um novo ambiente e um ou mais desses casos se aplicar à carga do evento, seus dados serão mesclados e armazenados de forma diferente. A tabela a seguir resume as alterações:

| Regra atual | Nova regra | JSON de exemplo | Nome da coluna anterior | Nome da nova coluna
|---|---| ---| ---|  ---|
| O JSON aninhado é achatado usando um sublinhado como o delineador. |O JSON aninhado é achatado usando um ponto como o delineador.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Caracteres especiais não são de escape. | Os nomes de propriedade JSON que incluem os caracteres especiais `.` `[`   `\` e `'` têm escape usando `['` e `']` . Em `['` e `']` , há um escape adicional de aspas simples e barras invertidas. Uma aspa simples será escrita como `\'` e uma barra invertida como `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Matrizes de primitivos são armazenadas como uma cadeia de caracteres. | Matrizes de tipos primitivos são armazenadas como um tipo dinâmico.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Matrizes de objetos são sempre achatadas, produzindo vários eventos. | Se os objetos dentro de uma matriz não tiverem a ID de TS ou as propriedades de carimbo de data/hora, a matriz de objetos será totalmente armazenada como um tipo dinâmico. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Alterações recomendadas para novos ambientes

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se sua ID de TS e/ou a propriedade Timestamp estiver aninhada dentro de um objeto

Todas as novas implantações precisam corresponder às novas regras de ingestão. Por exemplo, se sua ID de TS for `telemetry_tagId` , você precisará atualizar todos os modelos de Azure Resource Manager ou scripts de implantação automatizados para configurar `telemetry.tagId` como a ID de TS do ambiente. Você também precisa dessa alteração para carimbos de data/hora de origem do evento em JSON aninhado.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se sua carga contiver caracteres JSON ou especiais aninhados e você automatizar a criação de expressões de variável do [modelo de série temporal](./concepts-model-overview.md)

Atualize o código do cliente que executa o [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) para corresponder às novas regras de ingestão. Por exemplo, você deve atualizar uma [expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax) anterior de `"value": {"tsx": "$event.series_value.Double"}` para uma das seguintes opções:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Time Series insights o armazenamento e a entrada do Gen2](./concepts-ingestion-overview.md).

* Saiba como consultar seus dados usando APIs de [consulta de série temporal](./concepts-query-overview.md).

* Leia mais sobre a [nova sintaxe de expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax).