---
title: Migração da API v2 para v3
titleSuffix: Azure Cognitive Services
description: As APIs de ponto de extremidade da versão 3 foram alteradas. Use este guia para entender como migrar para as APIs de ponto de extremidade da versão 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: a61f196a509c3e84b518fffb4eb78f5f7430cb28
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667576"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Visualização: Migrar para a API versão 3. x para aplicativos LUIS

As APIs de ponto de extremidade de previsão de consulta foram alteradas. Use este guia para entender como migrar para as APIs de ponto de extremidade da versão 3. 

Esta API v3 fornece os novos recursos a seguir, que incluem alterações significativas de solicitação JSON e/ou resposta: 

* [Entidades externas](#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time)
* [Alterações de JSON de entidade predefinida](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

A [solicitação](#request-changes) e a [resposta](#response-changes) do ponto de extremidade de previsão de consulta têm alterações significativas para dar suporte aos novos recursos listados acima, incluindo o seguinte:

* [Alterações de objeto de resposta](#top-level-json-changes)
* [Referências de nome de função de entidade em vez do nome da entidade](#entity-role-name-instead-of-entity-name)
* [Propriedades para marcar entidades em declarações](#marking-placement-of-entities-in-utterances)

Os seguintes recursos de LUIS **não têm suporte** na API V3:

* Verificação Ortográfica do Bing v7

A [documentação de referência](https://aka.ms/luis-api-v3) está disponível para v3.

## <a name="endpoint-url-changes-by-slot-name"></a>Alterações de URL de ponto de extremidade por nome de slot

O formato da chamada HTTP do ponto de extremidade v3 foi alterado.

|FORMA|URL|
|--|--|
|OBTER|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POSTAR|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

Valores válidos para Slots:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>Alterações de URL de ponto de extremidade por ID de versão

Se desejar consultar por versão, primeiro você precisará [publicar via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com o `"directVersionPublish":true`. Consulte o ponto de extremidade que referencia a ID de versão em vez do nome do slot.


|FORMA|URL|
|--|--|
|OBTER|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POSTAR|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Entidades predefinidas com o novo JSON

As alterações de objeto de resposta v3 incluem [entidades](luis-reference-prebuilt-entities.md)predefinidas. 

## <a name="request-changes"></a>Alterações de solicitação 

### <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta

A API v3 tem parâmetros de cadeia de caracteres de consulta diferentes.

|Nome do parâmetro|Tipo|Versão|Padrão|Finalidade|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Armazenar consulta no arquivo de log.| 
|`query`|cadeia de caracteres|Somente V3|Nenhum padrão-ele é necessário na solicitação GET|**Em v2**, o expressão a ser previsto está no `q` parâmetro. <br><br>**No v3**, a funcionalidade é passada no `query` parâmetro.|
|`show-all-intents`|boolean|Somente V3|false|Retorne todas as intenções com a pontuação correspondente no objeto **preditiva. retenções** . As intenções são retornadas como objetos em um objeto `intents` pai. Isso permite o acesso programático sem a necessidade de encontrar a intenção em `prediction.intents.give`uma matriz:. Em v2, elas foram retornadas em uma matriz. |
|`verbose`|boolean|V2 & V3|false|**Em v2**, quando definido como true, todas as intenções previstas foram retornadas. Se você precisar de todas as intenções previstas, use o parâmetro `show-all-intents`v3 de.<br><br>**No v3**, esse parâmetro fornece apenas detalhes de metadados de entidade de previsão de entidade.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>O corpo JSON de previsão de consulta para `POST` a solicitação

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Propriedade|Tipo|Versão|Padrão|Finalidade|
|--|--|--|--|--|
|`dynamicLists`|array|Somente V3|Não obrigatório.|As [listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time) permitem que você estenda uma entidade de lista treinada e publicada existente, já no aplicativo Luis.|
|`externalEntities`|array|Somente V3|Não obrigatório.|[Entidades externas](#external-entities-passed-in-at-prediction-time) dão ao seu aplicativo Luis a capacidade de identificar e rotular entidades durante o tempo de execução, que pode ser usado como recursos para entidades existentes. |
|`options.datetimeReference`|cadeia de caracteres|Somente V3|Nenhum padrão|Usado para determinar o [deslocamento de datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para o datetimeReference é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|boolean|Somente V3|false|Especifica se a [entidade externa do usuário (com o mesmo nome da entidade existente)](#override-existing-model-predictions) é usada ou a entidade existente no modelo é usada para previsão. |
|`query`|cadeia de caracteres|Somente V3|Obrigatória.|**Em v2**, o expressão a ser previsto está no `q` parâmetro. <br><br>**No v3**, a funcionalidade é passada no `query` parâmetro.|



## <a name="response-changes"></a>Alterações de resposta

O JSON de resposta de consulta foi alterado para permitir um maior acesso programático aos dados usados com mais frequência. 

### <a name="top-level-json-changes"></a>Alterações de JSON de nível superior

As principais propriedades JSON para v2 são, quando `verbose` é definido como true, que retorna todas as intenções e suas pontuações `intents` na propriedade:

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
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

O `intents` objeto é uma lista não ordenada. Não assuma o primeiro filho em `intents` corresponde `topIntent`ao. Em vez disso, `topIntent` use o valor para encontrar a Pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

As alterações de esquema JSON de resposta permitem:

* Desmarque a distinção entre a `query`expressão original, e a previsão `prediction`retornada,.
* Acesso programático fácil aos dados previstos. Em vez de enumerar por meio de uma matriz na v2, você pode acessar valores por **nome** para as intenções e as entidades. Para funções de entidade previstas, o nome da função é retornado porque é exclusivo em todo o aplicativo.
* Os tipos de dados, se determinados, são respeitados. Os numéricos não são mais retornados como cadeias de caracteres.
* Distinção entre informações de previsão da primeira prioridade e metadados adicionais, retornados no `$instance` objeto. 

### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` para metadados de entidade

Se você precisar de metadados de entidade, a cadeia de caracteres de `verbose=true` consulta precisará usar o sinalizador e a resposta `$instance` conterá os metadados no objeto. Os exemplos são mostrados nas respostas JSON nas seções a seguir.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O `prediction.entities.<entity-name>` objeto contém uma matriz, pois cada entidade pode ser prevista mais de uma vez no expressão. 

### <a name="list-entity-prediction-changes"></a>Listar alterações de previsão de entidade

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

Ao mapear entre `entities` o objeto para `$instance` o objeto, a ordem dos objetos é preservada para as previsões de entidade de lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Nome da função de entidade em vez do nome da entidade 

Na v2, a `entities` matriz retornou todas as entidades previstas com o nome da entidade sendo o identificador exclusivo. Em v3, se a entidade usar funções e a previsão for para uma função de entidade, o identificador primário será o nome da função. Isso é possível porque os nomes de função de entidade devem ser exclusivos em todo o aplicativo, incluindo outros nomes de modelo (intenção, entidade).

No exemplo a seguir: Considere um expressão que inclui o texto, `Yellow Bird Lane`. Esse texto é previsto como uma função `Location` de entidade personalizada do `Destination`.

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Entidades externas passadas no momento da previsão

Entidades externas dão ao seu aplicativo LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que pode ser usado como recursos para entidades existentes. Isso permite que você use seus próprios extratores de entidade personalizados e separados antes de enviar consultas para seu ponto de extremidade de previsão. Como isso é feito no ponto de extremidade de previsão de consulta, você não precisa treinar novamente e publicar seu modelo.

O aplicativo cliente está fornecendo seu próprio extrator de entidade Gerenciando a correspondência de entidades e determinando o local dentro do expressão dessa entidade correspondente e, em seguida, enviando essas informações com a solicitação. 

Entidades externas são o mecanismo para estender qualquer tipo de entidade e, ao mesmo tempo, ser usado como sinais a outros modelos, como funções, compostos e outros.

Isso é útil para uma entidade que tem dados disponíveis somente no tempo de execução de previsão de consulta. Exemplos desse tipo de dados estão constantemente mudando de dados ou específicos por usuário. Você pode estender uma entidade de contato LUIS com informações externas da lista de contatos de um usuário. 

### <a name="entity-already-exists-in-app"></a>A entidade já existe no aplicativo

O valor de `entityName` para a entidade externa, passado no corpo da postagem da solicitação de ponto de extremidade, já deve existir no aplicativo treinado e publicado no momento em que a solicitação é feita. O tipo de entidade não importa, todos os tipos têm suporte.

### <a name="first-turn-in-conversation"></a>Primeiro ativar conversa

Considere um primeiro expressão em uma conversa de bot de chat, em que um usuário insere as seguintes informações incompletas:

`Send Hazem a new message`

A solicitação do bate-papo para Luis pode transmitir informações no corpo `Hazem` da postagem para que ele seja diretamente correspondido como um dos contatos do usuário.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui a entidade externa, com todas as outras entidades previstas, porque ela é definida na solicitação.  

### <a name="second-turn-in-conversation"></a>Segunda vez em conversa

O próximo usuário expressão no bot de chat usa um termo mais vago:

`Send him a calendar reminder for the party.`

No expressão anterior, o expressão usa `him` como uma referência a. `Hazem` O bot de chat de conversa, no corpo da postagem, `him` pode ser mapeado para o valor da entidade extraído `Hazem`da primeira expressão,.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui a entidade externa, com todas as outras entidades previstas, porque ela é definida na solicitação.  

### <a name="override-existing-model-predictions"></a>Substituir previsões de modelo existentes

A `overridePredictions` Propriedade Options Especifica que, se o usuário enviar uma entidade externa que se sobrepõe a uma entidade prevista com o mesmo nome, Luis escolherá a entidade passada ou a entidade existente no modelo. 

Por exemplo, considere a consulta `today I'm free`. Luis detecta `today` como um datetimeV2 com a seguinte resposta:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se o usuário enviar a entidade externa:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Se o `overridePredictions` for definido como `false`, Luis retornará uma resposta como se a entidade externa não fosse enviada. 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se o `overridePredictions` for definido como `true`, Luis retornará uma resposta, incluindo:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Resolução

A propriedade _opcional_ `resolution` retorna na resposta de previsão, permitindo que você passe os metadados associados à entidade externa e, em seguida, receba-os novamente na resposta. 

A principal finalidade é estender entidades predefinidas, mas não se limita a esse tipo de entidade. 

A `resolution` propriedade pode ser um número, uma cadeia de caracteres, um objeto ou uma matriz:

* Dallas
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listas dinâmicas passadas no momento da previsão

As listas dinâmicas permitem que você estenda uma entidade de lista treinada e publicada existente, já no aplicativo LUIS. 

Use esse recurso quando os valores de sua entidade de lista precisarem ser alterados periodicamente. Esse recurso permite que você estenda uma entidade de lista já treinada e publicada:

* No momento da solicitação de ponto de extremidade de previsão de consulta.
* Para uma única solicitação.

A entidade List pode estar vazia no aplicativo LUIS, mas ela deve existir. A entidade de lista no aplicativo LUIS não é alterada, mas a capacidade de previsão no ponto de extremidade é estendida para incluir até 2 listas com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Corpo da solicitação JSON da lista dinâmica

Envie o seguinte corpo JSON para adicionar uma nova sublista com sinônimos à lista e prever a entidade de lista para o texto, `LUIS`, com a solicitação de `POST` previsão de consulta:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

A resposta de previsão inclui essa entidade de lista, com todas as outras entidades previstas, porque ela é definida na solicitação. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset renomeado como datetimeReference

**No v2**, o `timezoneOffset` [parâmetro](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) é enviado na solicitação de previsão como um parâmetro de cadeia de caracteres de consulta, independentemente de a solicitação ser enviada como uma solicitação GET ou post. 

**No v3**, a mesma funcionalidade é fornecida com o parâmetro de corpo de `datetimeReference`postagem,. 

## <a name="marking-placement-of-entities-in-utterances"></a>Marcando o posicionamento de entidades no declarações

**Na v2**, uma entidade foi marcada em um expressão com o `startIndex` e `endIndex`o. 

**Em v3**, a entidade é marcada com `startIndex` e `entityLength`.

## <a name="deprecation"></a>Reprovação 

A API v2 não será preterida por pelo menos 9 meses após a visualização v3. 

## <a name="next-steps"></a>Próximas etapas

Use a documentação da API V3 para atualizar as chamadas REST existentes para as APIs de [ponto de extremidade](https://aka.ms/luis-api-v3) do Luis. 