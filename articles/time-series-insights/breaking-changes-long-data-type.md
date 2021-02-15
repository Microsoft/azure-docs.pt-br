---
title: Suporte para tipo de dados Long em Azure Time Series Insights Gen2 | Microsoft Docs
description: Suporte para tipo de dados Long em Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780817"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Adicionando suporte para o tipo de dados Long no Azure Time Series Insights Gen2

A adição de suporte para o tipo de dados Long afeta a forma como armazenamos e indexamos dados numéricos somente em ambientes Azure Time Series Insights Gen2. Se você tiver um ambiente Gen1, poderá desconsiderar essas alterações.

A partir de 29 de junho ou 30 de junho de 2020, dependendo da sua região, seus dados serão indexados como **longos** e **duplos**.  Se você tiver dúvidas ou preocupações sobre essa alteração, envie um tíquete de suporte por meio do portal do Azure e mencione essa comunicação.

Se você for afetado por qualquer um dos seguintes casos, faça as alterações recomendadas:

- **Caso 1**: atualmente, você usa variáveis de modelo de série temporal e envia apenas tipos de dados integral em seus dados de telemetria.
- **Caso 2**: atualmente, você usa variáveis de modelo de série temporal e envia tipos de dados integral e não integral em seus dados de telemetria.
- **Caso 3**: você usa variáveis categóricas para mapear valores inteiros para categorias.
- **Caso 4**: você usa o SDK do JavaScript para criar um aplicativo de front-end personalizado.
- **Caso 5**: você está se aproximando do limite de nome da propriedade 1.000 na loja morna e envia dados integrais e não integrais. A contagem de propriedades pode ser exibida como uma métrica no [portal do Azure](https://portal.azure.com/).

Se qualquer um dos casos se aplicar a você, faça alterações em seu modelo. Atualize a expressão de série temporal (TSX) em sua definição de variável com as alterações recomendadas. Atualize ambos:

- Gerenciador do Azure Time Series Insights
- Qualquer cliente personalizado que usa nossas APIs

Dependendo de sua solução e restrições de IoT, talvez você não tenha visibilidade dos dados que são enviados para seu ambiente de Azure Time Series Insights Gen2. Se você não tiver certeza se seus dados são somente integrais ou integralmente e não integrantes, você tem algumas opções:

- Você pode aguardar a liberação do recurso. Em seguida, explore seus eventos brutos na interface do usuário do Explorer para entender quais propriedades são salvas em duas colunas separadas.
- Você pode, de forma preventiva, fazer as alterações recomendadas para todas as marcas numéricas.
- Você pode rotear temporariamente um subconjunto de eventos para o armazenamento para entender melhor e explorar seu esquema.

Para armazenar eventos, ative a [captura de eventos](../event-hubs/event-hubs-capture-overview.md) para os hubs de eventos do Azure ou a [rota](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) do Hub IOT para o armazenamento de BLOBs do Azure.

Os dados também podem ser observados por meio do [Gerenciador de Hub de eventos](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)ou usando o host do processador de [eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events).

Se você usar o Hub IoT, vá para [ler mensagens do dispositivo para a nuvem do ponto de extremidade interno](../iot-hub/iot-hub-devguide-messages-read-builtin.md) para saber como acessar o ponto de extremidade interno.

> [!NOTE]
> Você poderá enfrentar uma interrupção se não fizer as alterações recomendadas. Por exemplo, as variáveis Time Series Insights afetadas que são acessadas por meio das APIs de consulta ou do Time Series Insights Explorer retornarão **NULL** (ou seja, não mostrar nenhum dado no Gerenciador).

## <a name="recommended-changes"></a>Alterações recomendadas

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Caso 1: usando variáveis de modelo de série temporal e enviando apenas tipos de dados inteiros em dados de telemetria

As alterações recomendadas para o caso 1 são as mesmas que para o caso 2. Siga as instruções na seção para o caso 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Caso 2: usando variáveis de modelo de série temporal e enviando tipos integrais e não integrais em dados de telemetria

Se você enviar dados de telemetria de inteiros no momento, seus dados serão divididos em duas colunas:

- **propertyValue_double**
- **propertyValue_long**

Seus dados de inteiro gravam em **propertyValue_long**. Dados numéricos ingeridos anteriormente (e ingeridos futuramente) no **propertyValue_double** não são copiados.

Se você quiser consultar dados entre essas duas colunas para a propriedade **PropertyValue** , precisará usar a função escalar de **adesão ()** no TSX. A função aceita argumentos do mesmo **tipo de dados** e retorna o primeiro valor não nulo na lista de argumentos. Para obter mais informações, consulte [Azure Time Series insights conceitos de acesso a dados do Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Definição de variável em TSX-numeric

*Definição de variável anterior:*

[![Captura de tela mostra a caixa de diálogo Adicionar uma nova variável para a variável PropertyValue, Numeric.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nova definição de variável:*

[![Captura de tela mostra a caixa de diálogo Adicionar uma nova variável para a variável PropertyValue com um valor personalizado, Numeric.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Você também pode usar o **adesão ($Event. PropertyValue. Double e toduplo ($Event. PropertyValue. Long))** como a [expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizada.

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Definição de variável embutida usando APIs de consulta TSX-numeric

*Definição de variável anterior:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Nova definição de variável:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Você também pode usar o **adesão ($Event. PropertyValue. Double e toduplo ($Event. PropertyValue. Long))** como a [expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizada.

> [!NOTE]
> Recomendamos que você atualize essas variáveis em todos os locais em que elas possam ser usadas. Esses locais incluem o modelo de série temporal, consultas salvas e consultas de conector de Power BI.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Caso 3: usando variáveis categóricas para mapear valores inteiros para categorias

Se, no momento, você usar variáveis categóricas que mapeiem valores inteiros para categorias, provavelmente está usando a função **tolong** para converter dados do tipo **Double** para o tipo **Long** . Assim como nos casos 1 e 2, você precisa fazer a União das colunas de **tipo de dados** **Double** e **Long** .

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definição de variável no Gerenciador de séries temporais-categórica

*Definição de variável anterior:*

[![Captura de tela mostra a caixa de diálogo Adicionar uma nova variável para a variável PropertyValue, categórica.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nova definição de variável:*

[![Captura de tela mostra a caixa de diálogo Adicionar uma nova variável para a variável PropertyValue com um valor personalizado, categórico.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Você também pode usar o **adesão ($Event. PropertyValue. Double e toduplo ($Event. PropertyValue. Long))** como a [expressão de série temporal](/rest/api/time-series-insights/preview#time-series-expression-and-syntax)personalizada.

Variáveis categóricas ainda exigem que o valor seja de um tipo inteiro. O **DataType** de todos os argumentos em **adesão ()** deve ser do tipo **Long** na [expressão de série temporal personalizada.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Definição de variável embutida usando APIs de consulta TSX-categórica

*Definição de variável anterior:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Nova definição de variável:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

Variáveis categóricas ainda exigem que o valor seja de um tipo inteiro. O **DataType** de todos os argumentos em **adesão ()** deve ser do tipo **Long** na [expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizada.

> [!NOTE]
> Recomendamos que você atualize essas variáveis em todos os locais em que elas possam ser usadas. Esses locais incluem o modelo de série temporal, consultas salvas e consultas de conector de Power BI.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Caso 4: usando o SDK do JavaScript para criar um aplicativo de front-end personalizado

Se você for afetado pelos casos 1 a 3 e criar aplicativos personalizados, precisará atualizar suas consultas para usar a função de **adesão ()** , conforme demonstrado nos exemplos anteriores.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Caso 5: perto do limite de propriedades de armazenamento quente 1.000

Se você for um usuário de armazenamento quente com um grande número de propriedades e acreditar que essa alteração enviaria seu ambiente pelo limite de nome de propriedade de armazenamento quente 1.000, envie um tíquete de suporte por meio do portal do Azure e mencione essa comunicação.

## <a name="next-steps"></a>Próximas etapas

- Exiba a lista completa de [tipos de dados com suporte](concepts-supported-data-types.md).
