---
title: Modelo de série temporal-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o modelo de série temporal no Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 7b37934ff3b867b83c2c958d52bea2dd4db01354
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736794"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Modelo de série temporal no Azure Time Series Insights Gen2

Este artigo descreve o modelo de série temporal, os recursos e como começar a criar e atualizar seus próprios modelos no ambiente do Azure Time Series Insights Gen2.

> [!TIP]
>
> * Vá para o ambiente de [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) para obter um exemplo de modelo de série temporal em tempo real.
> * Saiba [como trabalhar com o modelo de série temporal](./how-to-edit-your-model.md) usando o Azure Time Series insights Explorer.

## <a name="summary"></a>Resumo

Tradicionalmente, os dados que são coletados de dispositivos IoT não têm informações contextuais, o que dificulta encontrar e analisar sensores rapidamente. A principal motivação para o modelo de série temporal é simplificar a localização e a análise de dados IoT ou de série temporal. Ele alcança esse objetivo ao habilitar a consulta, a manutenção e o enriquecimento de dados de série temporal para ajudar a preparar conjuntos de os clientes prontos para o consumidor para análise.

## <a name="scenario-contosos-new-smart-oven"></a>Cenário: novo forno inteligente da contoso

**Considere o cenário fictício de um forno inteligente da contoso.** Neste cenário, suponha que cada forno inteligente da Contoso tenha cinco sensores de temperatura, um para cada um dos quatro gravadores principais e outro para o próprio forno. Até recentemente, cada sensor de temperatura da Contoso enviou, armazenou e visualizou seus dados individualmente. Para o monitoramento do seu dispositivo de cozinha, a contoso dependia de gráficos básicos, um para cada sensor individual.

Embora a contoso esteja satisfeita com sua solução inicial de dados e visualização, várias limitações se tornaram aparentes:

* Os clientes queriam saber o quanto o forno geral obteria quando a maioria dos principais gravadores estivesse. A contoso teve mais dificuldade em analisar e apresentar uma resposta unificada sobre as condições do forno geral.
* Os engenheiros da Contoso queriam verificar se os principais gravadores que estão sendo executados simultaneamente não resultarão em um consumo de energia ineficiente. Houve dificuldade em fazer referência cruzada que sensores de temperatura e voltagem foram associados entre si e como localizá-los na loja.
* A equipe de garantia de qualidade da Contoso queria auditar e comparar o histórico entre duas versões de sensor. Houve dificuldade em determinar quais dados pertenciam a qual versão do sensor.

Sem a capacidade de estruturar, organizar e definir o modelo de série temporal de forno inteligente abrangente, cada sensor de temperatura manteve pontos de dados desalocados, isolados e menos informativos. A desativação desses pontos de dados em informações acionáveis era mais difícil, pois cada um deles se vida independentemente dos outros.

Essas limitações revelaram a importância da agregação de dados inteligentes e das ferramentas de visualização para acompanhar o novo forno da Contoso:

* A visualização de dados comprova-se útil quando você é capaz de associar e combinar dados em uma exibição conveniente. Um exemplo é mostrar sensores de tensão junto com sensores de temperatura.
* O gerenciamento de dados multidimensionais para várias entidades juntamente com a comparação, o zoom e as funcionalidades de intervalo de tempo podem ser difíceis de realizar.

O **modelo de série temporal fornece uma solução conveniente** para muitos dos cenários encontrados neste exemplo fictício:

[![Exemplo de gráfico de forno inteligente de modelo de série temporal](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* O modelo de série temporal exerce um papel vital em consultas e navegação porque ele contextualiza dados, permitindo que as comparações sejam desenhadas entre intervalos de tempo e entre os tipos de sensor e de dispositivo. (**A**)
* Os dados são mais contextuais porque os dados persistentes no modelo de série temporal preservam as computações de consulta de série temporal como variáveis e as reutilizam no momento da consulta.
* O modelo de série temporal organiza e agrega dados para recursos aprimorados de gerenciamento e visualização. (**B**)

### <a name="key-capabilities"></a>Principais recursos

Com o objetivo de tornar simples e fácil gerenciar a congestão de séries temporais, o modelo de série temporal permite os seguintes recursos no Azure Time Series Insights Gen2. Ele ajuda a:

* Crie e gerencie cálculos ou fórmulas que aproveitam funções escalares, operações de agregação e assim por diante.
* Defina relações pai-filho para habilitar a navegação, pesquisa e referência.
* Defina as propriedades associadas às instâncias, definidas como campos de *instância*, e use-as para criar hierarquias.

### <a name="components"></a>Componentes

O Modelo de Série Temporal tem três componentes principais:

* [Instâncias de modelo de série temporal](#time-series-model-instances)
* [Hierarquias de modelo de série temporal](#time-series-model-hierarchies)
* [Tipos de modelo de série temporal](#time-series-model-types)

Esses componentes são combinados para especificar um modelo de série temporal e para organizar seus dados.

[![Gráfico de visão geral do modelo de série temporal](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Um modelo de série temporal pode ser criado e gerenciado por meio do [Azure Time Series insights Explorer](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels). As configurações do modelo de série temporal podem ser gerenciadas por meio da [API de configurações do modelo](/rest/api/time-series-insights/reference-model-apis).

## <a name="time-series-model-instances"></a>Instâncias do modelo do Time Series

*Instâncias* de modelo de série temporal são representações virtuais da própria série temporal.

Na maioria dos casos, as instâncias são identificadas exclusivamente por **DeviceID** ou **AssetID**, que são salvas como IDs de série temporal.

As instâncias têm informações descritivas associadas a elas chamadas *Propriedades de instância*, como uma ID de série temporal, tipo, nome, descrição, hierarquias e campos de instância. As propriedades de instância incluem, no mínimo, informações de hierarquia.

Os *campos de instância* são uma coleção de informações descritivas que podem incluir valores para níveis de hierarquia, bem como fabricante, operador e assim por diante.

Depois que uma fonte de evento é configurada para o ambiente de Azure Time Series Insights Gen2, as instâncias são descobertas automaticamente e criadas em um modelo de série temporal. As instâncias podem ser criadas ou atualizadas por meio do Azure Time Series Insights Explorer usando consultas de modelo de série temporal.

A [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) fornece vários exemplos de instância ao vivo.

[![Exemplo de instância de modelo de série temporal](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Propriedades da instância

As instâncias são definidas por **Timeseriesid**, **typeId**, **Name**, **Description**, **hierarchyIds** e **instanceFields**. Cada instância é mapeada para apenas um *tipo* e uma ou mais *hierarquias*.

| Propriedade | Descrição |
| --- | ---|
| Série temporal | A ID exclusiva da série temporal à qual a instância está associada. Na maioria dos casos, as instâncias são identificadas exclusivamente por uma propriedade como DeviceID ou AssetID. Em alguns casos, uma ID de composição mais específica que combina até três propriedades pode ser usada. |
| Identificação | A ID de cadeia de caracteres exclusiva que diferencia maiúsculas de minúsculas do tipo de modelo de série temporal ao qual a instância está associada. Por padrão, todas as novas instâncias descobertas são associadas a um tipo padrão.
| name | A propriedade **Name** é opcional e diferencia maiúsculas de minúsculas. Se o **nome** não estiver disponível, o padrão será **timeseriesid**. Se um nome for fornecido, **Timeseriesid** ainda estará disponível no [bem](./concepts-ux-panels.md#4-time-series-well). |
| descrição | Uma descrição de texto da instância. |
| hierarchyIds | Define a quais hierarquias a instância pertence. |
| instanceFields | As propriedades de uma instância e quaisquer dados estáticos que definem uma instância. Elas definem os valores das propriedades hierárquicas e não hierárquicas e também dão suporte à indexação para executar operações de pesquisa. |

> [!NOTE]
> As hierarquias são criadas usando campos de instância. **InstanceFields** adicionais podem ser adicionados para outras definições de propriedade de instância.

As instâncias têm a seguinte representação JSON:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Por exemplo, suporte para criação, leitura, atualização e exclusão (CRUD) de API de instância, leia o artigo [consulta de dados](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) e a [documentação REST da API de instância](/rest/api/time-series-insights/reference-model-apis#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarquias do modelo do Time Series

As *hierarquias* de modelo de série temporal organizam instâncias especificando nomes de propriedade e suas relações.

Você pode configurar várias hierarquias em um determinado ambiente de Azure Time Series Insights Gen2. Uma instância de modelo de série temporal pode ser mapeada para uma única hierarquia ou várias hierarquias (relação muitos para muitos).

A [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) exibe uma instância padrão e uma hierarquia de tipos.

[![Exemplo de hierarquia de modelo de série temporal](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definição de hierarquia

As hierarquias são definidas por **ID** de hierarquia, **nome** e **origem**.

| Propriedade | Descrição |
| ---| ---|
| id | O identificador exclusivo da hierarquia, que é usado, por exemplo, quando você define uma instância. |
| name | Uma cadeia de caracteres usada para fornecer um nome para a hierarquia. |
| source | Especifica a hierarquia organizacional ou o caminho, que é uma ordem pai-filho de cima para baixo da hierarquia que os usuários desejam criar. As propriedades de pai-filho mapeiamcampos de instância. |

As hierarquias são representadas em JSON como:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

No exemplo de JSON anterior:

* `Location` define uma hierarquia com pai `states` e filho `cities` . Cada um `location` pode ter vários `states` , que, por sua vez, podem ter vários `cities` .
* `ManufactureDate` define uma hierarquia com pai `year` e filho `month` . Cada um `ManufactureDate` pode ter vários `years` , que, por sua vez, podem ter vários `months` .

> [!TIP]
> Para o suporte CRUD (criação, leitura, atualização e exclusão) da API de hierarquia, leia o artigo [consulta de dados](concepts-query-overview.md#time-series-model-query-tsm-q-apis) e a [documentação REST da API de hierarquia](/rest/api/time-series-insights/reference-model-apis#hierarchies-api).

### <a name="hierarchy-example"></a>Exemplo de hierarquia

Considere um exemplo em que a hierarquia **H1** tem `building` , `floor` e `room` como parte de sua definição de **instanceFieldNames** :

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Considerando os campos de instância usados na definição anterior e várias séries temporais, os valores e atributos de hierarquia aparecem conforme mostrado na tabela a seguir:

| ID da série temporal | Campos de instância |
| --- | --- |
| ID1 | "compilando" = "1000", "piso" = "10", "sala" = "55"  |
| ID2 | "criando" = "1000", "sala" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "criando" = "1000", "Floor" = "10"  |
| ID5 | Nenhum de "prédio", "andar" ou "sala" é definido. |

A série **temporal ID1** e **ID4** são exibidos como parte da **hierarquia H1** no [Gerenciador de Azure Time Series insights](./concepts-ux-panels.md) porque eles possuem parâmetros de *compilação*, *piso* e *sala* totalmente definidos e corretamente ordenados.

Os outros são classificados em *instâncias sem pai* porque não estão em conformidade com a hierarquia de dados especificada.

## <a name="time-series-model-types"></a>Tipos do modelo do Time Series

Os *tipos* do modelo do Time Series ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica.

Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância de modelo de série temporal pode ser do tipo *sensor de temperatura*, que consiste nas variáveis de *temperatura média*, *mínima* e temperatura *máxima*.

A [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) visualiza vários tipos de modelo de série temporal associados a suas respectivas instâncias.

[![Exemplo de tipo de modelo de série temporal](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Para tipos de suporte CRUD (criação, leitura, atualização e exclusão) da API, leia o artigo [consulta de dados](concepts-query-overview.md#time-series-model-query-tsm-q-apis) e a [documentação do tipo API REST](/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Propriedades de tipo

Os tipos de modelo de série temporal são definidos por **ID**, **nome**, **Descrição** e **variáveis**.

| Propriedade | Descrição |
| ---| ---|
| id | A ID de cadeia de caracteres exclusiva que diferencia maiúsculas de minúsculas para o tipo. |
| name | Uma cadeia de caracteres usada para fornecer um nome para o tipo. |
| descrição | Uma descrição de cadeia de caracteres para o tipo. |
| variáveis | Especifique as variáveis associadas ao tipo. |

Os tipos estão em conformidade com o seguinte exemplo de JSON:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

Os tipos de modelo de série temporal podem ter muitas variáveis que especificam fórmulas e regras de computação em eventos. Leia mais sobre [como definir variáveis de modelo de série temporal](./concepts-variables.md)

## <a name="next-steps"></a>Próximas etapas

* Leia a documentação de referência do [modelo de série temporal](/rest/api/time-series-insights/reference-model-apis) para obter mais informações sobre como editar o modelo por meio de APIs.

* Explore as fórmulas e computações que você pode criar com [variáveis de modelo de série temporal](./concepts-variables.md)

* Saiba mais sobre como [consultar dados](concepts-query-overview.md) no Azure Time Series insights Gen2
