---
title: Entidades predefinidas do DatetimeV2 – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade predefinida de datetimeV2 em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013064"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entidade DatetimeV2 predefinida para um aplicativo LUIS

A entidade predefinida **datetimeV2** extrai os valores de data e hora. Esses valores são resolvidos em um formato padronizado para programas do cliente consumirem. Quando um enunciado tem uma data ou hora que não está concluído, o LUIS inclui _valores passados e futuros_ na resposta do ponto de extremidade. Como essa entidade já está treinada, não é necessário adicionar enunciados contendo datetimeV2 às intenções do aplicativo.

## <a name="types-of-datetimev2"></a>Tipos de datetimeV2
DatetimeV2 é gerenciado no repositório GitHub de [texto de reconhecedores](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) .

## <a name="example-json"></a>JSON de exemplo

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[Resposta v3](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Resposta v2](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Nome da propriedade |Tipo e descrição da propriedade|
|---|---|
|Entidade|**cadeia de caracteres** – texto extraído do enunciado com tipo de data, hora, intervalo de datas ou intervalo de tempo.|
|type|**cadeia de caracteres** – um do [subtipos de datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** – o índice no enunciado em que a entidade começa.|
|endIndex|**int** – o índice no enunciado em que a entidade termina.|
|resolução|Tem uma matriz `values` com um, dois ou quatro [valores de resolução](#values-of-resolution).|
|end|O valor final de uma hora ou intervalo de datas no mesmo formato que `value`. Usado somente se `type` for `daterange`, `timerange` ou `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Subtipos de datetimeV2

A entidade predefinida **datetimeV2** tem os seguintes subtipos, e exemplos de cada um são fornecidos na tabela a seguir:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Valores de resolução
* A matriz terá um elemento se a data ou hora no enunciado for totalmente especificado e não ambígua.
* A matriz terá dois elementos se o valor de datetimeV2 for ambíguo. Ambiguidade inclui falta de um ano, hora ou intervalo de tempo específico. Veja [datas ambíguas](#ambiguous-dates) para obter exemplos. Quando a hora é ambígua para A.M. ou P.M., ambos os valores são incluídos.
* A matriz terá quatro elementos se o enunciado tiver dois elementos com ambiguidade. Essa ambiguidade inclui elementos que têm:
  * Uma data ou um intervalo de datas ambíguo quanto ao ano
  * Um horário ou intervalo de tempo ambíguo como A.M. ou P.M. Por exemplo, 3h de 3 de abril.

Cada elemento da matriz `values` pode ter os seguintes campos:

|Nome da propriedade|Descrição da propriedade|
|--|--|
|timex|hora, data ou intervalo de datas expressado no formato TIMEX que segue o [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e os atributos TIMEX3 para anotação usando a linguagem TimeML.|
|mod|termo usado para descrever como usar o valor `before` , como, `after` .|
|type|O subtipo, que pode ser um dos seguintes itens:,,,,, `datetime` `date` ,, `time` `daterange` `timerange` `datetimerange` `duration` `set` .|
|valor|**Opcional.** Um objeto DateTime no formato AAAA-MM-DD (Date), HH: mm: SS (time) aaaa-MM-DD HH: mm: SS (DateTime). Se `type` for `duration`, o valor será o número de segundos (duration) <br/> Usado somente se `type` for `datetime` ou `date`, `time` ou `duration.|

## <a name="valid-date-values"></a>Valores de data válidos

O **datetimeV2** é compatível com datas entre os seguintes intervalos:

| Mín | Max |
|----------|-------------|
| 1º de janeiro de 1900   | 31 de dezembro de 2099 |

## <a name="ambiguous-dates"></a>Datas ambíguas

Se a data puder estar no passado ou no futuro, o LUIS fornecerá ambos os valores. Um exemplo é um enunciado que inclui o mês e o dia sem o ano.

Por exemplo, considerando o seguinte expressão:

`May 2nd`

* Se a data de hoje for 3 de maio de 2017, o LUIS fornecerá tanto "2017-05-02" quanto "2018-05-02" como valores.
* Quando a data de hoje for 1º de maio de 2017, o LUIS fornecerá tanto "2016-05-02" quanto "2017-05-02" como valores.

O exemplo a seguir mostra a resolução da entidade "may 2nd". Essa resolução pressupõe que a data de hoje é uma data entre 2 de maio de 2017 e 1º de maio de 2018.
Campos com `X` no campo `timex` fazem parte da data que não está explicitamente especificada no enunciado.

## <a name="date-resolution-example"></a>Exemplo de resolução de data


O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`May 2nd`

#### <a name="v3-response"></a>[Resposta v3](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Resposta v2](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Exemplos de resolução de intervalo de datas para data numérica

A entidade `datetimeV2` extrai os intervalos de data e hora. Os campos `start` e `end` especificam o início e o fim do intervalo. Para o expressão `May 2nd to May 5th` , Luis fornece valores de **DateRange** para o ano atual e o próximo ano. No campo `timex`, os valores `XXXX` indicam a ambiguidade do ano. `P3D` indica que o período é de três dias.

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[Resposta v3](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Resposta v2](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Exemplos de resolução de intervalo de data para o dia da semana

O exemplo a seguir mostra como o LUIS usa **datetimeV2** para resolver o expressão `Tuesday to Thursday` . Neste exemplo, a data atual é 19 de junho. O LUIS inclui valores de **daterange** para ambos os intervalos de datas que precedem e seguem a data atual.

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[Resposta v3](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Resposta v2](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Horário ambíguo
A matriz de valores terá dois elementos de tempo se o tempo ou o intervalo de tempo for ambíguo. Quando há um tempo ambíguo, os valores têm ambos A.M. e P.M. como horários.

## <a name="time-range-resolution-example"></a>Exemplo de resolução de intervalo de tempo

A resposta JSON DatetimeV2 foi alterada na API v3. O exemplo a seguir mostra como o LUIS usa **datetimeV2** para resolver o enunciado que tem um intervalo de tempo.

Alterações da API v2:
* `datetimeV2.timex.type` a propriedade não é mais retornada porque é retornada no nível pai, `datetimev2.type` .
* A `datetimeV2.value` propriedade foi renomeada para `datetimeV2.timex` .

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[Resposta v3](#tab/5-1)

O JSON a seguir é com o `verbose` parâmetro definido como `false` :

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/5-2)

O JSON a seguir é com o `verbose` parâmetro definido como `true` :

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Resposta v2](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Exemplo de resolução de tempo

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`8am`

#### <a name="v3-response"></a>[Resposta v3](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Resposta v2](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Datetime predefinido preterido

A entidade pré-compilada `datetime` é reprovada e substituída por **datetimeV2**.

Para substituir `datetime` por `datetimeV2` em seu aplicativo LUIS, conclua as seguintes etapas:

1. Abra o painel **Entidades** da interface Web do LUIS.
2. Exclua a entidade predefinida **datetime**.
3. Clique em **Adicionar entidade predefinida**
4. Selecione **datetimeV2** e clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [dimensão](luis-reference-prebuilt-dimension.md), [email](luis-reference-prebuilt-email.md) e [número](luis-reference-prebuilt-number.md).

