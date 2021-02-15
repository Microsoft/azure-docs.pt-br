---
title: Migrando para novas Azure Time Series Insights versões da API do Gen2 | Microsoft Docs
description: Como atualizar Azure Time Series Insights ambientes Gen2 para usar as novas versões disponíveis em geral.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 15f1a814b302611029cf6459b8d2df93a32a2d36
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740548"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migrando para novas Azure Time Series Insights versões da API do Gen2

## <a name="overview"></a>Visão geral

Se você tiver criado um ambiente de Azure Time Series Insights Gen2 quando ele estava em visualização pública (antes de 16 de julho de 2020), atualize seu ambiente de TSI para usar as novas versões disponíveis em geral de APIs seguindo as etapas descritas neste artigo. Essa alteração não afeta nenhum usuário que esteja usando a versão Gen1 do Azure Time Series Insights.

> [!IMPORTANT]
> As atualizações descritas neste artigo atualizarão apenas as versões de API usadas pelo seu ambiente de TSI. Essa alteração não está relacionada ao novo [nivelamento JSON e às regras de escape](./concepts-json-flattening-escaping-rules.md) introduzidas para ambientes Gen2.

A nova versão de API é `2020-07-31` e usa uma [sintaxe de expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax)atualizada.

Os usuários devem migrar as [variáveis de modelo de série temporal](./concepts-variables.md)de seu ambiente, consultas salvas, Power bi consultas e quaisquer ferramentas personalizadas fazendo chamadas para os pontos de extremidade da API. Se você tiver dúvidas ou preocupações sobre o processo de migração, envie um tíquete de suporte por meio do portal do Azure e mencione este documento.

> [!IMPORTANT]
> A versão de API de visualização `2018-11-01-preview` continuará a ter suporte até 31 de outubro de 2020. Conclua todas as etapas aplicáveis dessa migração antes de evitar qualquer interrupção no serviço.

## <a name="migrate-time-series-model-and-saved-queries"></a>Migrar o modelo de série temporal e consultas salvas

Para ajudar os usuários a migrar suas [variáveis de modelo de série temporal](./concepts-variables.md) e consultas salvas, há uma ferramenta interna disponível por meio do [Azure Time Series insights Explorer](https://insights.timeseries.azure.com). Navegue até o ambiente que você deseja migrar e siga as etapas abaixo. **Você pode concluir a migração parcialmente e retornar para concluí-la mais tarde. no entanto, nenhuma das atualizações pode ser revertida.**

> [!NOTE]
> Você deve ser um colaborador para o ambiente para fazer atualizações no modelo de série temporal e consultas salvas. Se você não for um colaborador, poderá migrar suas consultas pessoais salvas. Examine [as políticas de acesso do ambiente](./concepts-access-policies.md) e seu nível de acesso antes de continuar.

1. Você será solicitado pelo Explorer para atualizar a sintaxe usada pelas suas variáveis de modelo de série temporal e consultas salvas.

    [![Prompt](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Se você fechar acidentalmente a notificação, ela poderá ser encontrada no painel de notificação.

1. Clique em **Mostrar atualizações** para abrir a ferramenta de migração.

1. Clique em **tipos de download**. Como a migração substituirá os tipos atuais para alterar a sintaxe variável, será necessário salvar uma cópia dos seus tipos atuais. A ferramenta irá notificá-lo quando os tipos tiverem sido baixados.

    [![Tipos de download](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Clique em **Atualizar variáveis**. A ferramenta o notificará quando as variáveis tiverem sido atualizadas.

    > [!IMPORTANT]
    > Se você atualizar seus tipos, os aplicativos personalizados que usam uma versão de API mais antiga ( `2018-11-01-preview` ) precisarão usar a nova versão de API ( `2020-07-31` ) para continuar trabalhando. Se você não tiver certeza de qual versão de API está usando, verifique com seu administrador antes de atualizar. Você pode fechar a ferramenta de migração e retornar a essas etapas posteriormente. Leia mais sobre [como migrar aplicativos personalizados](#migrate-custom-applications).

    [![Atualizar variáveis](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Clique em **Atualizar consultas salvas**. A ferramenta irá notificá-lo quando as consultas salvas forem atualizadas.

    [![Atualizar consultas salvas](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Clique em **Concluído**.

    [![Migração concluída](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Examine o ambiente atualizado criando um gráfico de algumas das variáveis recém-criadas e das consultas salvas. Se você vir qualquer comportamento inesperado durante o gráfico, envie-nos comentários usando a ferramenta de comentários no Gerenciador.

## <a name="migrate-power-bi-queries"></a>Migrar Power BI consultas

Se você tiver gerado consultas usando o conector de Power BI, elas estão fazendo chamadas para Azure Time Series Insights usando a versão da API de visualização e a sintaxe da expressão de série temporal antiga. Essas consultas continuarão a recuperar dados com êxito até que a API de visualização seja preterida.

Para atualizar as consultas para usar a nova versão de API e a nova sintaxe de expressão de série temporal, as consultas precisarão ser regeneradas no Explorer. Leia mais sobre como [criar consultas usando o conector de Power bi](./how-to-connect-power-bi.md).

> [!NOTE]
> Você deve usar pelo menos a versão de julho de 2020 do Power BI Desktop. Se você não fizer isso, poderá ver um erro "versão da carga de consulta inválida".

## <a name="migrate-custom-applications"></a>Migrar aplicativos personalizados

Se seu aplicativo personalizado estiver fazendo chamadas para os seguintes pontos de extremidade REST, será suficiente atualizar a versão da API para `2020-07-31` no URI:

- APIs de Modelo de Série Temporal
  - APIs de configurações de modelo
    - [Obter](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Atualização](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - APIs da instância
    - [Todas as operações de lote](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Pesquisar](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Sugerir](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - APIs de hierarquia
    - [Todas as operações de lote](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - APIs de tipos
    - [Excluir, obter operações](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

Para os seguintes pontos de extremidade REST, você deve atualizar a versão da API para `2020-07-31` no URI e verificar se todas as ocorrências da `tsx` Propriedade usam a [sintaxe da expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax)atualizada.

- APIs de tipos
  - [Operação put](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- APIs de consulta
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [Série](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Exemplos

#### <a name="typesbatchput"></a>TypesBatchPut

Corpo de solicitação antigo (usado por `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Corpo da solicitação atualizado (usado por `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Como alternativa, o `filter` também pode ser `$event.Mode.String = 'outdoor'` . O `value` deve usar os colchetes para escapar do caractere especial ( `_` ).

#### <a name="getevents"></a>GetEvents

Corpo de solicitação antigo (usado por `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Corpo da solicitação atualizado (usado por `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Como alternativa, o `filter` também pode ser `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>Série

Corpo de solicitação antigo (usado por `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Corpo da solicitação atualizado (usado por `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Como alternativa, o `value` também pode ser `$event['Bar-Pressure-Offset'].Double` . Se nenhum tipo de dados for especificado, o tipo de dados sempre será considerado duplo. A notação de colchete deve ser usada para escapar do caractere especial ( `-` ).

#### <a name="aggregateseries"></a>AggregateSeries

Corpo de solicitação antigo (usado por `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Corpo da solicitação atualizado (usado por `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Como alternativa, o `value` também pode ser `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>Possíveis erros

#### <a name="invalidinput"></a>InvalidInput

Se você vir o erro a seguir, você está usando a nova versão de API ( `2020-07-31` ), mas a sintaxe do TSX não foi atualizada. Examine os exemplos de sintaxe e de migração da [expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax) acima. Verifique se todas as `tsx` Propriedades foram atualizadas corretamente antes de reenviar a solicitação de API.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Teste seu ambiente por meio do [Azure Time Series insights Explorer](./concepts-ux-panels.md) ou por meio de seu aplicativo personalizado.