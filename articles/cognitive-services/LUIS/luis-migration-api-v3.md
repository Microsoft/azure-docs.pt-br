---
title: Alterações de ponto de extremidade de previsão na API v3
description: As APIs de ponto de extremidade de previsão de consulta foram alteradas. Use este guia para entender como migrar para as APIs de ponto de extremidade da versão 3.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59dfa439f6428f2db972a8f848887e1a74bc2622
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624296"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Alterações de ponto de extremidade de previsão para v3

As APIs de ponto de extremidade de previsão de consulta foram alteradas. Use este guia para entender como migrar para as APIs de ponto de extremidade da versão 3.

**Status de disponibilidade geral** – esta API v3 inclui alterações de resposta e solicitação JSON significativas da API v2.

A API v3 fornece os seguintes novos recursos:

* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Alterações de JSON de entidade predefinida](#prebuilt-entity-changes)

A [solicitação](#request-changes) e a [resposta](#response-changes) do ponto de extremidade de previsão têm alterações significativas para dar suporte aos novos recursos listados acima, incluindo o seguinte:

* [Alterações de objeto de resposta](#top-level-json-changes)
* [Referências de nome de função de entidade em vez do nome da entidade](#entity-role-name-instead-of-entity-name)
* [Propriedades para marcar entidades em declarações](#marking-placement-of-entities-in-utterances)

A [documentação de referência](https://aka.ms/luis-api-v3) está disponível para v3.

## <a name="v3-changes-from-preview-to-ga"></a>O V3 muda de visualização para GA

A V3 fez as seguintes alterações como parte da mudança para GA:

* As seguintes entidades predefinidas têm respostas JSON diferentes:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Nome da chave de unidade mensurável de `units` para `unit`

* Alteração de JSON do corpo da solicitação:
    * de `preferExternalEntities` para `preferExternalEntities`
    * `score`parâmetro opcional para entidades externas

* Alterações de JSON do corpo da resposta:
    * `normalizedQuery` removido

## <a name="suggested-adoption-strategy"></a>Estratégia de adoção sugerida

Se você usar o bot Framework, Verificação Ortográfica do Bing v7 ou desejar migrar apenas a criação de aplicativos LUIS, continue a usar o ponto de extremidade v2.

Se você souber que nenhum de seus aplicativos cliente ou integrações (bot Framework e Verificação Ortográfica do Bing v7) são afetados e você está confortável migrando sua criação de aplicativo LUIS e seu ponto de extremidade de previsão ao mesmo tempo, comece a usar o ponto de extremidade de previsão v3. O ponto de extremidade de previsão v2 ainda estará disponível e será uma boa estratégia de retorno.


## <a name="not-supported"></a>Sem suporte

### <a name="bing-spell-check"></a>Verificação Ortográfica do Bing

Esta API não tem suporte no ponto de extremidade de previsão v3-continue a usar o ponto de extremidade de previsão da API v2 para obter correções ortográficas. Se você precisar de correção ortográfica ao usar a API v3, faça com que o aplicativo cliente chame a API de [verificação ortográfica do Bing](../bing-spell-check/overview.md) e altere o texto para a grafia correta, antes de enviar o texto para a API Luis.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Aplicativos cliente do bot Framework e do serviço de bot do Azure

Continue a usar o ponto de extremidade de previsão da API v2 até que a V 4.7 da estrutura de bot seja lançada.


## <a name="endpoint-url-changes"></a>Alterações de URL de ponto de extremidade

### <a name="changes-by-slot-name-and-version-name"></a>Alterações por nome do slot e nome da versão

O [formato da chamada http do ponto de extremidade v3](developer-reference-resource.md#rest-endpoints) foi alterado.

Se desejar consultar por versão, primeiro você precisará [publicar via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com `"directVersionPublish":true` . Consulte o ponto de extremidade que referencia a ID de versão em vez do nome do slot.

|Valores válidos para `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Alterações de solicitação

### <a name="query-string-changes"></a>Alterações na cadeia de consulta

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>Corpo da POSTAgem v3

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Propriedade|Type|Versão|Padrão|Finalidade|
|--|--|--|--|--|
|`dynamicLists`|array|Somente V3|Não necessário.|As [listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) permitem que você estenda uma entidade de lista treinada e publicada existente, já no aplicativo Luis.|
|`externalEntities`|array|Somente V3|Não necessário.|[Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) dão ao seu aplicativo Luis a capacidade de identificar e rotular entidades durante o tempo de execução, que pode ser usado como recursos para entidades existentes. |
|`options.datetimeReference`|string|Somente V3|Nenhum padrão|Usado para determinar o [deslocamento de datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para o datetimeReference é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Somente V3|false|Especifica se a [entidade externa do usuário (com o mesmo nome da entidade existente)](schema-change-prediction-runtime.md#override-existing-model-predictions) é usada ou a entidade existente no modelo é usada para previsão. |
|`query`|string|Somente V3|Obrigatórios.|**Em v2**, o expressão a ser previsto está no `q` parâmetro. <br><br>**No v3**, a funcionalidade é passada no `query` parâmetro.|

## <a name="response-changes"></a>Alterações de resposta

O JSON de resposta de consulta foi alterado para permitir um maior acesso programático aos dados usados com mais frequência.

### <a name="top-level-json-changes"></a>Alterações de JSON de nível superior



As principais propriedades JSON para v2 são, quando `verbose` é definido como true, que retorna todas as intenções e suas pontuações na `intents` Propriedade:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

As principais propriedades JSON para v3 são:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

O `intents` objeto é uma lista não ordenada. Não assuma o primeiro filho em `intents` corresponde ao `topIntent` . Em vez disso, use o `topIntent` valor para encontrar a Pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

As alterações de esquema JSON de resposta permitem:

* Desmarque a distinção entre a expressão original, `query` e a previsão retornada, `prediction` .
* Acesso programático fácil aos dados previstos. Em vez de enumerar por meio de uma matriz na v2, você pode acessar valores por **nome** para as intenções e as entidades. Para funções de entidade previstas, o nome da função é retornado porque é exclusivo em todo o aplicativo.
* Os tipos de dados, se determinados, são respeitados. Os numéricos não são mais retornados como cadeias de caracteres.
* Distinção entre informações de previsão da primeira prioridade e metadados adicionais, retornados no `$instance` objeto.

### <a name="entity-response-changes"></a>Alterações de resposta de entidade

#### <a name="marking-placement-of-entities-in-utterances"></a>Marcando o posicionamento de entidades no declarações

**Na v2**, uma entidade foi marcada em um expressão com o `startIndex` e o `endIndex` .

**Em v3**, a entidade é marcada com `startIndex` e `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` para metadados de entidade

Se você precisar de metadados de entidade, a cadeia de caracteres de consulta precisará usar o `verbose=true` sinalizador e a resposta conterá os metadados no `$instance` objeto. Os exemplos são mostrados nas respostas JSON nas seções a seguir.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O `prediction.entities.<entity-name>` objeto contém uma matriz, pois cada entidade pode ser prevista mais de uma vez no expressão.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Alterações de entidade predefinidas

O objeto de resposta v3 inclui alterações em entidades predefinidas. Examine [entidades predefinidas específicas](luis-reference-prebuilt-entities.md) para saber mais.

#### <a name="list-entity-prediction-changes"></a>Listar alterações de previsão de entidade

O JSON para uma previsão de entidade de lista mudou para ser uma matriz de matrizes:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Cada matriz interior corresponde ao texto dentro de expressão. O objeto interior é uma matriz porque o mesmo texto pode aparecer em mais de uma sublista de uma entidade de lista.

Ao mapear entre o `entities` objeto para o `$instance` objeto, a ordem dos objetos é preservada para as previsões de entidade de lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome da função de entidade em vez do nome da entidade

Na v2, a `entities` matriz retornou todas as entidades previstas com o nome da entidade sendo o identificador exclusivo. Em v3, se a entidade usar funções e a previsão for para uma função de entidade, o identificador primário será o nome da função. Isso é possível porque os nomes de função de entidade devem ser exclusivos em todo o aplicativo, incluindo outros nomes de modelo (intenção, entidade).

No exemplo a seguir: Considere um expressão que inclui o texto, `Yellow Bird Lane` . Esse texto é previsto como uma `Location` função de entidade personalizada do `Destination` .

|Texto expressão|Nome da entidade|Nome da função|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Na v2, a entidade é identificada pelo _nome da entidade_ com a função como uma propriedade do objeto:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

Em v3, a entidade é referenciada pela _função de entidade_, se a previsão for para a função:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Em v3, o mesmo resultado com o `verbose` sinalizador para retornar os metadados da entidade:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Estender o aplicativo no momento da previsão

Aprenda [conceitos](schema-change-prediction-runtime.md) sobre como estender o aplicativo no tempo de execução de previsão.


## <a name="next-steps"></a>Próximas etapas

Use a documentação da API V3 para atualizar as chamadas REST existentes para as APIs de [ponto de extremidade](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) do Luis.
