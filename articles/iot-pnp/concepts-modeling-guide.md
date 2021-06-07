---
title: Entender os modelos de dispositivo Plug and Play IoT | Microsoft Docs
description: Entenda a linguagem de modelagem DTDL (digital gêmeos Definition Language) para dispositivos IoT Plug and Play. O artigo descreve os tipos de datatexto primitivos e complexos, reutilizar padrões que usam componentes e herança, e tipo semântico. O artigo fornece orientação sobre a escolha do identificador de modelo do dispositivo e do suporte de ferramentas para a criação de modelos.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a3389408b0942875aa7d760f1c514b995e381f9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608937"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Guia de modelagem de Plug and Play de IoT

No núcleo do Plug and Play IoT, é um _modelo_ de dispositivo que descreve os recursos de um dispositivo para um aplicativo habilitado para IOT plug and Play. Esse modelo é estruturado como um conjunto de interfaces que definem:

- _Propriedades_ que representam o estado somente leitura ou gravável de um dispositivo ou outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade somente leitura e uma temperatura de destino em um termostato pode ser uma propriedade gravável.
- Campos de _telemetria_ que definem os dados emitidos por um dispositivo, se os dados são um fluxo regular de leituras de sensor, um erro ocasional ou uma mensagem de informações.
- _Comandos_ que descrevem uma função ou operação que pode ser feita em um dispositivo. Por exemplo, um comando pode reinicializar um gateway ou tirar uma foto usando uma câmera remota.

Para saber mais sobre como a IoT Plug and Play usa modelos de dispositivo, consulte Guia do [desenvolvedor do dispositivo iot plug and Play](concepts-developer-guide-device.md) e [Guia do desenvolvedor do serviço IOT plug and Play](concepts-developer-guide-service.md).

Para definir um modelo, use a linguagem de definição de gêmeos digital (DTDL). DTDL usa uma variante JSON chamada [JSON-LD](https://json-ld.org/). O trecho a seguir mostra o modelo para um dispositivo termostato que:

- Tem uma ID de modelo exclusiva: `dtmi:com:example:Thermostat;1` .
- Envia telemetria de temperatura.
- Tem uma propriedade gravável para definir a temperatura de destino.
- Tem uma propriedade somente leitura para relatar a temperatura máxima desde a última reinicialização.
- Responde a um comando que solicita temperaturas máximas, mínimas e médias em um período de tempo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

O modelo termostato tem uma única interface. Os exemplos posteriores neste artigo mostram modelos mais complexos que usam componentes e herança.

Este artigo descreve como projetar e criar seus próprios modelos e abrange tópicos como tipos de dados, estrutura de modelo e ferramentas.

Para saber mais, confira a especificação do [digital gêmeos Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl) .

## <a name="model-structure"></a>Estrutura do modelo

Propriedades, telemetria e comandos são agrupados em interfaces. Esta seção descreve como você pode usar interfaces para descrever modelos simples e complexos usando componentes e herança.

### <a name="model-ids"></a>IDs de modelo

Cada interface tem um DTMI (identificador de modelo de entrelaçamento digital exclusivo). Modelos complexos usam o DTMIs para identificar componentes. Os aplicativos podem usar o DTMIs que os dispositivos enviam para localizar definições de modelo em um repositório.

DTMIs deve seguir a Convenção de nomenclatura exigida pelo [repositório de modelo de plug and Play de IOT](https://github.com/Azure/iot-plugandplay-models):

- O prefixo DTMI é `dtmi:` .
- O sufixo DTMI é o número de versão do modelo, como `;2` .
- O corpo do DTMI é mapeado para a pasta e o arquivo no repositório de modelos em que o modelo está armazenado. O número de versão é parte do nome do arquivo.

Por exemplo, o modelo identificado pelo DTMI `dtmi:com:Example:Thermostat;2` é armazenado no *DTMI/com/example/thermostat-2.jsno* arquivo.

O trecho a seguir mostra o contorno de uma definição de interface com seu DTMI exclusivo:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Nenhum componente

Um modelo simples, como o termostato mostrado anteriormente, não usa componentes incorporados ou em cascata. Telemetria, propriedades e comandos são definidos no `contents` nó da interface.

O exemplo a seguir mostra parte de um modelo simples que não usa componentes:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Ferramentas como o Azure IoT Explorer e o IoT Central designer de modelo de dispositivo rotulam uma interface autônoma como o termostato como um _componente padrão_.

A captura de tela a seguir mostra como o modelo é exibido na ferramenta do Azure IoT Explorer:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Componente padrão no Azure IoT Explorer":::

A captura de tela a seguir mostra como o modelo é exibido como o componente padrão no designer de modelo de dispositivo IoT Central. Selecione **Exibir identidade** para ver o DTMI do modelo:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Captura de tela mostrando o modelo termostato no designer de IoT Central":::

A ID do modelo é armazenada em uma propriedade mydo dispositivo, como mostra a captura de tela a seguir:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="ID do modelo na propriedade de propriedades digitais":::

Um modelo de DTDL sem componentes é uma simplificação útil para um dispositivo ou um módulo IoT Edge com um único conjunto de telemetria, propriedades e comandos. Um modelo que não usa componentes facilita a migração de um dispositivo ou módulo existente para ser um dispositivo IoT Plug and Play ou módulo-você cria um modelo DTDL que descreve seu dispositivo ou módulo real sem a necessidade de definir quaisquer componentes.

> [!TIP]
> Um módulo pode ser um [módulo](../iot-hub/iot-hub-devguide-module-twins.md) de dispositivo ou um [módulo IOT Edge](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Reutilizar

Há duas maneiras de reutilizar as definições de interface. Use vários componentes em um modelo para fazer referência a outras definições de interface. Use a herança para estender as definições de interface existentes.

### <a name="multiple-components"></a>Vários componentes

Os componentes permitem criar uma interface de modelo como um assembly de outras interfaces.

Por exemplo, a interface [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) é definida como um modelo. Você pode incorporar essa interface como um ou mais componentes ao definir o [modelo de controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). No exemplo a seguir, esses componentes são chamados `thermostat1` e `thermostat2` .

Para um modelo DTDL com vários componentes, há duas ou mais seções de componente. Cada seção foi `@type` definida como `Component` e se refere explicitamente a um esquema, conforme mostrado no trecho a seguir:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Esse modelo tem três componentes definidos na seção conteúdo-dois `Thermostat` componentes e um `DeviceInformation` componente. A seção conteúdo também inclui propriedades, telemetria e definições de comando.

As capturas de tela a seguir mostram como esse modelo aparece em IoT Central. A propriedade, a telemetria e as definições de comando no controlador de temperatura aparecem no **componente padrão** de nível superior. A propriedade, a telemetria e as definições de comando para cada termostato aparecem nas definições de componente:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Captura de tela mostrando o modelo de dispositivo do controlador de temperatura em IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Captura de tela mostrando os componentes do termostato no modelo de dispositivo do controlador de temperatura no IoT Central.":::

Para saber como escrever o código do dispositivo que interage com componentes, consulte [Guia do desenvolvedor do dispositivo IoT plug and Play](concepts-developer-guide-device.md).

Para saber como escrever código de serviço que intergatos com componentes em um dispositivo, consulte [Guia do desenvolvedor do serviço de IoT plug and Play](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Herança

A herança permite que você reutilize recursos em interfaces base para estender os recursos de uma interface. Por exemplo, vários modelos de dispositivo podem compartilhar recursos comuns, como um número de série:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Exemplo de herança em um modelo de dispositivo que mostra um termostato e um controlador de fluxo que compartilham recursos de uma interface base." border="false":::

O trecho a seguir mostra um modelo DTML que usa a `extends` palavra-chave para definir a relação de herança mostrada no diagrama anterior:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

A captura de tela a seguir mostra esse modelo no ambiente de modelo de dispositivo IoT Central:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Captura de tela mostrando a herança de interface no IoT Central":::

Quando você escreve o dispositivo ou o código do lado do serviço, seu código não precisa fazer nada especial para lidar com interfaces herdadas. No exemplo mostrado nesta seção, o código do dispositivo relata o número de série como se ele fizer parte da interface termostato.

### <a name="tips"></a>Dicas

Você pode combinar componentes e herança ao criar um modelo. O diagrama a seguir mostra um `thermostat` modelo herdado de uma `baseDevice` interface. A `baseDevice` interface tem um componente que, por sua vez, herda de outra interface:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagrama mostrando um modelo que usa componentes e herança." border="false":::

O trecho a seguir mostra um modelo DTML que usa `extends` as `component` palavras-chave e para definir a relação de herança e o uso do componente mostrado no diagrama anterior:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Tipos de dados

Use tipos de dados para definir telemetria, propriedades e parâmetros de comando. Os tipos de dados podem ser primitivos ou complexos. Os tipos de texto complexos usam primitivos ou outros tipos complexos. A profundidade máxima para tipos complexos é de cinco níveis.

### <a name="primitive-types"></a>Tipos primitivos

A tabela a seguir mostra o conjunto de tipos primitivos que você pode usar:

| Tipo primitivo | Descrição |
| --- | --- |
| `boolean` | Um valor booliano |
| `date` | Uma data completa, conforme definido na [seção 5,6 da RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Uma data e hora conforme definido no [RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Um ponto flutuante de 8 bytes IEEE |
| `duration` | Uma duração no formato ISO 8601 |
| `float` | Um ponto flutuante de 4 bytes IEEE |
| `integer` | Um inteiro de 4 bytes assinado |
| `long` | Um inteiro de 8 bytes assinado |
| `string` | Uma cadeia de caracteres UTF8 |
| `time` | Um tempo integral, conforme definido na [seção 5,6 da RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

O trecho a seguir mostra uma definição de telemetria de exemplo que usa o `double` tipo no `schema` campo:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Tipos de datacomplexos

Os tipos de texto complexos são um de *matriz*, *Enumeração*, *mapa*, *objeto* ou um dos tipo geoespaciais.

#### <a name="arrays"></a>Matrizes

Uma matriz é um tipo de dados Indexável em que todos os elementos são do mesmo tipo. O tipo de elemento pode ser um tipo primitivo ou complexo.

O trecho a seguir mostra uma definição de telemetria de exemplo que usa o `Array` tipo no `schema` campo. Os elementos da matriz são boolianos:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Enumerações

Uma enumeração descreve um tipo com um conjunto de rótulos nomeados que são mapeados para valores. Os valores podem ser inteiros ou cadeias de caracteres, mas os rótulos são sempre cadeias de caracteres.

O trecho a seguir mostra uma definição de telemetria de exemplo que usa o `Enum` tipo no `schema` campo. Os valores na enumeração são inteiros:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Mapas

Um mapa é um tipo com pares de chave-valor onde todos os valores têm o mesmo tipo. A chave em um mapa deve ser uma cadeia de caracteres. Os valores em um mapa podem ser qualquer tipo, incluindo outro tipo complexo.

O trecho a seguir mostra uma definição de propriedade de exemplo que usa o `Map` tipo no `schema` campo. Os valores no mapa são cadeias de caracteres:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objetos

Um tipo de objeto é composto de campos nomeados. Os tipos de campos em um mapa de objetos podem ser tipos primitivos ou complexos.

O trecho a seguir mostra uma definição de telemetria de exemplo que usa o `Object` tipo no `schema` campo. Os campos no objeto são `dateTime` , `duration` e `string` tipos:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Tipos geoespaciais

O DTDL fornece um conjunto de tipos geoespaciais, com base em [geojson](https://geojson.org/), para modelagem de estruturas de dados geográficos:,,,, `point` `multiPoint` `lineString` `multiLineString` `polygon` e `multiPolygon` . Esses tipos são estruturas aninhadas predefinidas de matrizes, objetos e enumerações.

O trecho a seguir mostra uma definição de telemetria de exemplo que usa o `point` tipo no `schema` campo:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Como os tipos geoespaciais são baseados em matriz, eles não podem ser usados no momento em definições de propriedade.

## <a name="semantic-types"></a>Tipos semânticos

O DataType de uma definição de propriedade ou de telemetria especifica o formato dos dados que um dispositivo troca com um serviço. O tipo semântico fornece informações sobre telemetria e propriedades que um aplicativo pode usar para determinar como processar ou exibir um valor. Cada tipo semântico tem uma ou mais unidades associadas. Por exemplo, Celsius e Fahrenheit são unidades para o tipo semântico de temperatura. IoT Central dashboards e análises podem usar as informações de tipo semântico para determinar como plotar os valores de telemetria ou de propriedade e as unidades de exibição. Para saber como você pode usar o analisador de modelo para ler os tipos semânticos, consulte [entender o analisador de modelo digital gêmeos](concepts-model-parser.md).

O trecho a seguir mostra uma definição de telemetria de exemplo que inclui informações de tipo semântico. O tipo semântico `Temperature` é adicionado à `@type` matriz, e o `unit` valor `degreeCelsius` é uma das unidades válidas para o tipo semântico:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Localização

Aplicativos, como IoT Central, usam informações no modelo para criar dinamicamente uma interface do usuário em volta dos dados trocados com um dispositivo de IoT Plug and Play. Por exemplo, blocos em um painel podem exibir nomes e descrições de telemetria, propriedades e comandos.

Os `description` campos e opcionais `displayName` no modelo contêm cadeias de caracteres pretendidas para uso em uma interface do usuário. Esses campos podem conter cadeias de caracteres localizadas que um aplicativo pode usar para renderizar uma interface do usuário localizada.

O trecho a seguir mostra um exemplo de definição de telemetria de temperatura que inclui cadeias de caracteres localizadas:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

A adição de cadeias de caracteres localizadas é opcional. O exemplo a seguir tem apenas um único idioma padrão:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Ciclo de vida e ferramentas

Os quatro estágios do ciclo de vida de um modelo de dispositivo são criação, publicação, uso e controle de versão:

### <a name="author"></a>Autor

Os modelos de dispositivo DTML são documentos JSON que você pode criar em um editor de texto. No entanto, no IoT Central você pode usar o ambiente de GUI do modelo de dispositivo para criar um modelo DTML. No IoT Central você pode:

- Crie interfaces que definem propriedades, telemetria e comandos.
- Use componentes para montar várias interfaces juntas.
- Defina relações de herança entre interfaces.
- Importar e exportar arquivos de modelo DTML.

Para saber mais, consulte [definir um novo tipo de dispositivo IOT em seu aplicativo IOT central do Azure](../iot-central/core/howto-set-up-template.md).

O [Editor DTDL para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) fornece um ambiente de edição baseado em texto com validação de sintaxe e preenchimento automático para exercer um melhor controle sobre a experiência de criação de modelos.

### <a name="publish"></a>Publicação

Para tornar seus modelos DTML compartilháveis e detectáveis, você os publica em um repositório de modelos de dispositivo.

Antes de publicar um modelo no repositório público, você pode usar as `dmr-client` ferramentas para validar seu modelo.

Para saber mais, confira [repositório de modelos de dispositivo](concepts-model-repository.md).

### <a name="use"></a>Uso

Aplicativos, como IoT Central, usam modelos de dispositivo. No IoT Central, um modelo faz parte do modelo de dispositivo que descreve os recursos do dispositivo. IoT Central usa o modelo de dispositivo para criar dinamicamente uma interface do usuário para o dispositivo, incluindo painéis e análises.

Uma solução personalizada pode usar o [analisador de modelo digital gêmeos](concepts-model-parser.md) para entender os recursos de um dispositivo que implementa o modelo. Para saber mais, consulte [usar modelos de iot plug and Play em uma solução de IOT](concepts-model-discovery.md).

### <a name="version"></a>Versão

Para garantir que os dispositivos e as soluções do lado do servidor que usam modelos continuem a funcionar, os modelos publicados são imutáveis.

O DTMI inclui um número de versão que você pode usar para criar várias versões de um modelo. Dispositivos e soluções do lado do servidor podem usar a versão específica que foram projetadas para usar.

IoT Central implementa mais regras de controle de versão para modelos de dispositivo. Se você tiver a versão de um modelo de dispositivo e seu modelo no IoT Central, poderá migrar dispositivos de versões anteriores para versões posteriores. No entanto, os dispositivos migrados não podem usar novos recursos sem uma atualização de firmware. Para saber mais, confira [criar uma nova versão de modelo de dispositivo](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Limites e restrições

A lista a seguir resume algumas restrições de chave e limites em modelos:

- Atualmente, a profundidade máxima para matrizes, mapas e objetos é de cinco níveis de profundidade.
- Você não pode usar matrizes em definições de propriedade.
- Você pode estender interfaces para uma profundidade de 10 níveis.
- Uma interface pode estender no máximo duas outras interfaces.
- Um componente não pode conter outro componente.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a modelagem de dispositivo, aqui estão alguns recursos adicionais:

- [Instalar e usar as ferramentas de criação do DTDL](howto-use-dtdl-authoring-tools.md)
- [DTDL (digital gêmeos Definition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositórios de modelo](./concepts-model-repository.md)
