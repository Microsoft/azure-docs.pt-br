---
title: Estender o aplicativo em tempo de execução-LUIS
description: Saiba como estender um ponto de extremidade de previsão já publicado para passar novas informações.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953014"
---
# <a name="extend-app-at-prediction-runtime"></a>Estender o aplicativo no tempo de execução de previsão

O esquema do aplicativo (modelos e recursos) é treinado e publicado no ponto de extremidade de previsão. Esse modelo publicado é usado no tempo de execução de previsão. Você pode passar novas informações, junto com o expressão do usuário, para o tempo de execução de previsão para aumentar a previsão.

Duas alterações de esquema de tempo de execução de previsão incluem:
* [Entidades externas](#external-entities)
* [Listas dinâmicas](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Entidades externas

Entidades externas dão ao seu aplicativo LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que pode ser usado como recursos para entidades existentes. Isso permite que você use seus próprios extratores de entidade personalizados e separados antes de enviar consultas para seu ponto de extremidade de previsão. Como isso é feito no ponto de extremidade de previsão de consulta, você não precisa treinar novamente e publicar seu modelo.

O aplicativo cliente está fornecendo seu próprio extrator de entidade Gerenciando a correspondência de entidades e determinando o local dentro do expressão dessa entidade correspondente e, em seguida, enviando essas informações com a solicitação.

Entidades externas são o mecanismo para estender qualquer tipo de entidade e, ao mesmo tempo, ser usado como sinais para outros modelos.

Isso é útil para uma entidade que tem dados disponíveis somente no tempo de execução de previsão de consulta. Exemplos desse tipo de dados estão constantemente mudando de dados ou específicos por usuário. Você pode estender uma entidade de contato LUIS com informações externas da lista de contatos de um usuário.

As entidades externas fazem parte da API de criação v3. Saiba mais sobre como [migrar](luis-migration-api-v3.md) para esta versão.

### <a name="entity-already-exists-in-app"></a>A entidade já existe no aplicativo

O valor de `entityName` para a entidade externa, passado no corpo da postagem da solicitação de ponto de extremidade, já deve existir no aplicativo treinado e publicado no momento em que a solicitação é feita. O tipo de entidade não importa, todos os tipos têm suporte.

### <a name="first-turn-in-conversation"></a>Primeiro ativar conversa

Considere um primeiro expressão em uma conversa de bot de chat, em que um usuário insere as seguintes informações incompletas:

`Send Hazem a new message`

A solicitação do bate-papo para LUIS pode transmitir informações no corpo da POSTAgem para que `Hazem` ele seja diretamente correspondido como um dos contatos do usuário.

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

Nessa rodada, o expressão usa `him` como uma referência a `Hazem` . O bot de chat de conversa, no corpo da POSTAgem, pode `him` ser mapeado para o valor da entidade extraído da primeira expressão, `Hazem` .

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

A `preferExternalEntities` Propriedade Options Especifica que, se o usuário enviar uma entidade externa que se sobrepõe a uma entidade prevista com o mesmo nome, Luis escolherá a entidade passada ou a entidade existente no modelo.

Por exemplo, considere a consulta `today I'm free`. LUIS detecta `today` como um datetimeV2 com a seguinte resposta:

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

Se o `preferExternalEntities` for definido como `false` , Luis retornará uma resposta como se a entidade externa não fosse enviada.

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

Se o `preferExternalEntities` for definido como `true` , Luis retornará uma resposta, incluindo:

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
* {"texto": "valor"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Listas dinâmicas

As listas dinâmicas permitem que você estenda uma entidade de lista treinada e publicada existente, já no aplicativo LUIS.

Use esse recurso quando os valores de sua entidade de lista precisarem ser alterados periodicamente. Esse recurso permite que você estenda uma entidade de lista já treinada e publicada:

* No momento da solicitação de ponto de extremidade de previsão de consulta.
* Para uma única solicitação.

A entidade List pode estar vazia no aplicativo LUIS, mas ela deve existir. A entidade de lista no aplicativo LUIS não é alterada, mas a capacidade de previsão no ponto de extremidade é estendida para incluir até 2 listas com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Corpo da solicitação JSON da lista dinâmica

Envie o seguinte corpo JSON para adicionar uma nova sublista com sinônimos à lista e prever a entidade de lista para o texto, `LUIS` , com a solicitação de `POST` previsão de consulta:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
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

## <a name="next-steps"></a>Próximas etapas

* [Pontuação de previsão](luis-concept-prediction-score.md)
* [Criando alterações da API v3](luis-migration-api-v3.md)
