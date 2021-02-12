---
title: Telemetria, propriedade e cargas de comando no Azure IoT Central | Microsoft Docs
description: Os modelos de dispositivo IoT Central do Azure permitem que você especifique a telemetria, as propriedades e os comandos de um dispositivo que devem ser implementados. Entenda o formato dos dados que um dispositivo pode trocar com IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 5a9f6fa79da59425e4972dddd21ffdea15af73e7
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127899"
---
# <a name="telemetry-property-and-command-payloads"></a>Telemetria, propriedade e payloads de comando

_Este artigo se aplica a desenvolvedores de dispositivos._

Um modelo de dispositivo no Azure IoT Central é um plano gráfico que define:

* Telemetria que um dispositivo envia para IoT Central.
* Propriedades que um dispositivo sincroniza com IoT Central.
* Comandos que IoT Central chamadas em um dispositivo.

Este artigo descreve, para desenvolvedores de dispositivos, os conteúdos JSON que os dispositivos enviam e recebem para telemetria, propriedades e comandos definidos em um modelo de dispositivo.

O artigo não descreve todos os tipos possíveis de telemetria, propriedade e carga de comando, mas os exemplos ilustram todos os tipos de chave.

Cada exemplo mostra um trecho de código do modelo de dispositivo que define o tipo e as cargas de JSON de exemplo para ilustrar como o dispositivo deve interagir com o aplicativo IoT Central.

> [!NOTE]
> IoT Central aceita qualquer JSON válido, mas só pode ser usado para visualizações se ele corresponder a uma definição no modelo do dispositivo. Você pode exportar dados que não correspondem a uma definição, consulte [exportar dados de IOT para destinos no Azure](howto-export-data.md).

O arquivo JSON que define o modelo de dispositivo usa o [DTDL (digital Mydefinition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Para obter um exemplo de código de dispositivo que mostra algumas dessas cargas em uso, consulte o tutorial [criar e conectar um aplicativo cliente ao seu aplicativo do Azure IOT central](tutorial-connect-device.md) .

## <a name="view-raw-data"></a>Exibir dados brutos

IoT Central permite exibir os dados brutos que um dispositivo envia para um aplicativo. Essa exibição é útil para solucionar problemas com a carga enviada de um dispositivo. Para exibir os dados brutos que um dispositivo está enviando:

1. Navegue até o dispositivo na página **dispositivos** .

1. Selecione a guia **dados brutos** :

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Exibição de dados brutos":::

    Nessa exibição, você pode selecionar as colunas a serem exibidas e definir um intervalo de tempo para exibição. A coluna **Dados não modelados** mostra dados do dispositivo que não correspondem a nenhuma propriedade ou definição de telemetria no modelo de dispositivo.

## <a name="telemetry"></a>Telemetria

### <a name="primitive-types"></a>Tipos primitivos

Esta seção mostra exemplos de tipos de telemetria primitivos que um dispositivo transmite para um aplicativo IoT Central.

O trecho a seguir de um modelo de dispositivo mostra a definição de um `boolean` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir:

```json
{ "BooleanTelemetry": true }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `string` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `integer` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir:

```json
{ "IntegerTelemetry": 23 }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `double` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir:

```json
{ "DoubleTelemetry": 56.78 }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `dateTime` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo `DateTime` : os tipos devem estar no formato ISO 8061.

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `duration` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON que se parece com o seguinte exemplo – as durações devem estar no formato ISO 8601:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Tipos complexos

Esta seção mostra exemplos de tipos de telemetria complexos que um dispositivo transmite para um aplicativo IoT Central.

O trecho a seguir de um modelo de dispositivo mostra a definição de um `geopoint` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

> [!NOTE]
> O tipo de esquema de **ponto** não faz parte da [especificação da linguagem de definição de gêmeos digital](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). O IoT Central atualmente dá suporte ao tipo de esquema **geopoint** e ao tipo semântico de **localização** para compatibilidade com versões anteriores.

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir. IoT Central exibe o valor como um PIN em um mapa:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `Enum` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir. Os valores possíveis são `0` , `1` , e são `2` exibidos em IOT central como `Item1` , `Item2` e `Item3` :

```json
{ "EnumTelemetry": 1 }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `Object` tipo de telemetria. Esse objeto tem três campos com tipos `dateTime` , `integer` e `Enum` :

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir. `DateTime` os tipos devem ser compatíveis com ISO 8061. Os valores possíveis para `Property3` são `0` , `1` , e são exibidos em IOT central como `Item1` , `Item2` e `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `vector` tipo de telemetria:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Um cliente de dispositivo deve enviar a telemetria como JSON semelhante ao exemplo a seguir:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Tipos de evento e estado

Esta seção mostra exemplos de eventos de telemetria e Estados que um dispositivo envia para um aplicativo IoT Central.

O trecho a seguir de um modelo de dispositivo mostra a definição de um `integer` tipo de evento:

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Um cliente de dispositivo deve enviar os dados de evento como JSON que se parece com o exemplo a seguir:

```json
{ "IntegerEvent": 74 }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `integer` tipo de Estado:

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Um cliente de dispositivo deve enviar o estado como JSON semelhante ao exemplo a seguir. Os valores de estado inteiro possíveis são `1` , `2` ou `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Propriedades

> [!NOTE]
> Os formatos de carga para propriedades se aplicam a aplicativos criados em ou após 07/14/2020.

### <a name="primitive-types"></a>Tipos primitivos

Esta seção mostra exemplos de tipos de propriedade primitivos que um dispositivo envia para um aplicativo IoT Central.

O trecho a seguir de um modelo de dispositivo mostra a definição de um `boolean` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

```json
{ "BooleanProperty": false }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `boolean` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

```json
{ "LongProperty": 439 }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `date` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

Um cliente de dispositivo deve enviar uma carga JSON semelhante ao exemplo a seguir como uma propriedade relatada no dispositivo. `Date` os tipos devem ser compatíveis com ISO 8061:

```json
{ "DateProperty": "2020-05-17" }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `duration` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

Um cliente de dispositivo deve enviar uma carga JSON semelhante ao exemplo a seguir, uma vez que uma propriedade relatada no dispositivo é compatível com a duração ISO 8601:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `float` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

```json
{ "FloatProperty": 1.9 }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `string` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Tipos complexos

Esta seção mostra exemplos de tipos de propriedade complexos que um dispositivo envia para um aplicativo IoT Central.

O trecho a seguir de um modelo de dispositivo mostra a definição de um `geopoint` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

> [!NOTE]
> O tipo de esquema de **ponto** não faz parte da [especificação da linguagem de definição de gêmeos digital](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). O IoT Central atualmente dá suporte ao tipo de esquema **geopoint** e ao tipo semântico de **localização** para compatibilidade com versões anteriores.

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `Enum` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Um cliente de dispositivo deve enviar uma carga JSON semelhante ao exemplo a seguir como uma propriedade relatada no dispositivo. Os valores possíveis são `0` , `1` , e são exibidos em IOT central como `Item1` , `Item2` e `Item3` :

```json
{ "EnumProperty": 1 }
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `Object` tipo de propriedade. Este objeto tem dois campos com tipos `string` e `integer` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `vector` tipo de propriedade:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Tipos de propriedade graváveis

Esta seção mostra exemplos de tipos de propriedades graváveis que um dispositivo recebe de um aplicativo IoT Central.

IoT Central espera uma resposta do dispositivo para atualizações de propriedade gravável. A mensagem de resposta deve incluir `ac` os `av` campos e. O campo `ad` é opcional. Consulte os trechos de código a seguir para obter exemplos.

`ac` é um campo numérico que usa os valores na tabela a seguir:

| Valor | Rotular | Descrição |
| ----- | ----- | ----------- |
| `'ac': 200` | Concluído | A operação de alteração de propriedade foi concluída com êxito. |
| `'ac': 202`  or `'ac': 201` | Pendente | A operação de alteração de propriedade está pendente ou em andamento |
| `'ac': 4xx` | Erro | A alteração de propriedade solicitada não era válida ou teve um erro |
| `'ac': 5xx` | Erro | O dispositivo apresentou um erro inesperado ao processar a alteração solicitada. |

`av` é o número de versão enviado ao dispositivo.

`ad` é uma descrição de cadeia de caracteres de opção.

O trecho a seguir de um modelo de dispositivo mostra a definição de um `string` tipo de propriedade gravável:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

O dispositivo recebe a seguinte carga do IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

O dispositivo deve enviar a carga JSON a seguir para IoT Central depois de processar a atualização. Essa mensagem inclui o número de versão da atualização original recebida do IoT Central. Quando IoT Central recebe essa mensagem, ela marca a propriedade como **sincronizada** na interface do usuário:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

O trecho a seguir de um modelo de dispositivo mostra a definição de um `Enum` tipo de propriedade gravável:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

O dispositivo recebe a seguinte carga do IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

O dispositivo deve enviar a carga JSON a seguir para IoT Central depois de processar a atualização. Essa mensagem inclui o número de versão da atualização original recebida do IoT Central. Quando IoT Central recebe essa mensagem, ela marca a propriedade como **sincronizada** na interface do usuário:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Comandos

O trecho a seguir de um modelo de dispositivo mostra a definição de um comando que não tem parâmetros e que não espera que o dispositivo retorne nada:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

O dispositivo recebe uma carga vazia na solicitação e deve retornar uma carga vazia na resposta com um código de `200` resposta http para indicar êxito.

O trecho a seguir de um modelo de dispositivo mostra a definição de um comando que tem um parâmetro de número inteiro e que espera que o dispositivo retorne um valor inteiro:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

O dispositivo recebe um valor inteiro como a carga de solicitação. O dispositivo deve retornar um valor inteiro como a carga de resposta com um `200` código de resposta http para indicar êxito.

O trecho a seguir de um modelo de dispositivo mostra a definição de um comando que tem um parâmetro de objeto e que espera que o dispositivo retorne um objeto. Neste exemplo, os dois objetos têm inteiros e campos de cadeia de caracteres:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

O trecho a seguir mostra um exemplo de carga de solicitação enviada para o dispositivo:

```json
{ "Field1": 56, "Field2": "A string value" }
```

O trecho a seguir mostra um exemplo de carga de resposta enviada do dispositivo. Use um `200` código de resposta http para indicar êxito:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>Comandos de longa execução

O trecho a seguir de um modelo de dispositivo mostra a definição de um comando. O comando tem um parâmetro inteiro e espera que o dispositivo retorne um valor inteiro:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

O dispositivo recebe um valor inteiro como a carga de solicitação. Se o dispositivo precisar de tempo para processar esse comando, ele deverá retornar uma carga de resposta vazia com um `202` código de resposta http para indicar que o dispositivo aceitou a solicitação de processamento.

Quando o dispositivo terminar de processar a solicitação, ele deverá enviar uma propriedade para IoT Central que se parece com o exemplo a seguir. O nome da propriedade deve ser igual ao nome do comando:

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>Comandos offline

Na interface do usuário da Web do IoT Central, você pode selecionar a opção **fila se estiver offline** para um comando. Os comandos offline são notificações unidirecionais para o dispositivo de sua solução que são entregues assim que um dispositivo se conecta. Comandos offline podem ter parâmetros de solicitação, mas não retornam uma resposta.

A configuração **fila se offline** não será incluída se você exportar um modelo ou interface do modelo de dispositivo. Não é possível saber como examinar um modelo ou uma interface JSON exportada que um comando é um comando offline.

Comandos offline usam [mensagens da nuvem para o dispositivo do Hub IOT](../../iot-hub/iot-hub-devguide-messages-c2d.md) para enviar o comando e a carga para o dispositivo.

O trecho a seguir de um modelo de dispositivo mostra a definição de um comando. O comando tem um parâmetro de objeto com um campo DateTime e uma enumeração:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 2,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

Se você habilitar a opção **fila se estiver offline** na interface do usuário do modelo de dispositivo para o comando no trecho anterior, a mensagem que o dispositivo recebe incluirá as seguintes propriedades:

| Nome da propriedade | Valor de exemplo |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>Próximas etapas

Como um desenvolvedor de dispositivos, agora que você aprendeu sobre modelos de dispositivo, as próximas etapas sugeridas são ler [conectar-se ao Azure IOT central](./concepts-get-connected.md) para saber mais sobre como registrar dispositivos com IOT central e como IOT central protegem as conexões de dispositivos.
