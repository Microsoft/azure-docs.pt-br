---
title: Convenções de Plug and Play IoT | Microsoft Docs
description: A descrição das convenções que o IoT Plug and Play espera que os dispositivos usem quando enviam telemetria e propriedades e manipulam comandos e atualizações de propriedade.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a8dfb3cacf798559273361ec75cab4570a8a5228
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582673"
---
# <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Os dispositivos de Plug and Play IoT devem seguir um conjunto de convenções quando trocam mensagens com um hub IoT. Os dispositivos IoT Plug and Play usam o protocolo MQTT para se comunicar com o Hub IoT.

Os dispositivos podem incluir [módulos](../iot-hub/iot-hub-devguide-module-twins.md)ou ser implementados em um [módulo IoT Edge](../iot-edge/about-iot-edge.md) hospedado pelo IOT Edge Runtime.

Você descreve a telemetria, as propriedades e os comandos que um dispositivo de Plug and Play IoT implementa com um _modelo_ [DTDL (digital gêmeos Definition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl) . Há dois tipos de modelo referenciados neste artigo:

- **Nenhum componente** -um modelo sem componentes. O modelo declara telemetria, propriedades e comandos como propriedades de nível superior na seção de conteúdo da interface principal. Na ferramenta do Azure IoT Explorer, esse modelo aparece como um único _componente padrão_.
- **Vários componentes** – um modelo composto por duas ou mais interfaces. Uma interface principal, que aparece como o _componente padrão_, com telemetria, propriedades e comandos. Uma ou mais interfaces declaradas como componentes com telemetria, propriedades e comandos adicionais.

Para obter mais informações, consulte [Guia de modelagem do IoT plug and Play](concepts-modeling-guide.md).

## <a name="identify-the-model"></a>Identificar o modelo

Para anunciar o modelo que ele implementa, um dispositivo ou módulo de Plug and Play IoT inclui a ID do modelo no pacote de conexão MQTT adicionando `model-id` ao `USERNAME` campo.

Para identificar o modelo que um dispositivo ou módulo implementa, um serviço pode obter a ID do modelo de:

- O campo de dispositivo `modelId` .
- O campo de `$metadata.$model` área digital.
- Uma notificação de alteração de troca digital.

## <a name="telemetry"></a>Telemetria

A telemetria enviada de um dispositivo sem componente não requer metadados extras. O sistema adiciona a `dt-dataschema` propriedade.

A telemetria enviada de um dispositivo de vários componentes deve ser adicionada `$.sub` como uma propriedade de mensagem. O sistema adiciona as `dt-subject` `dt-dataschema` Propriedades e.

## <a name="read-only-properties"></a>Propriedades somente leitura

### <a name="sample-no-component-read-only-property"></a>Exemplo sem propriedade somente leitura do componente

Um dispositivo ou módulo pode enviar qualquer JSON válido que siga as regras de DTDL v2.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Conteúdo da propriedade relatada de exemplo:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Propriedade somente leitura de vários componentes de exemplo

O dispositivo ou módulo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento refere-se a um componente.

DTDL que faz referência a um componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL que define o componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Conteúdo da propriedade relatada de exemplo:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Propriedades graváveis

O dispositivo ou módulo deve confirmar que recebeu a propriedade enviando uma propriedade relatada. A propriedade relatada deve incluir:

- `value` -o valor real da propriedade (normalmente o valor recebido, mas o dispositivo pode decidir relatar um valor diferente).
- `ac` -um código de confirmação que usa um código de status HTTP.
- `av` -uma versão de confirmação que se refere ao `$version` da propriedade desejada. Você pode encontrar esse valor no conteúdo JSON da propriedade desejada.
- `ad` -uma descrição de confirmação opcional.

Quando um dispositivo é iniciado, ele deve solicitar o dispositivo e verificar se há atualizações de propriedade gravável. Se a versão de uma propriedade gravável aumentou enquanto o dispositivo estava offline, o dispositivo deve enviar uma resposta de propriedade relatada para confirmar que recebeu a atualização.

Quando um dispositivo é iniciado pela primeira vez, ele pode enviar um valor inicial para uma propriedade relatada se ele não receber uma propriedade desejada inicial do Hub. Nesse caso, o dispositivo deve definir `av` como `1` . Por exemplo:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Um dispositivo pode usar a propriedade relatada para fornecer outras informações ao Hub. Por exemplo, o dispositivo pode responder com uma série de mensagens em andamento, como:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Quando o dispositivo atinge a temperatura de destino, ele envia a seguinte mensagem:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Um dispositivo pode relatar um erro como:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Exemplo de propriedade não gravável de componente

Quando um dispositivo recebe várias propriedades relatadas em uma única carga, ele pode enviar as respostas de propriedade relatadas entre várias cargas.

Um dispositivo ou módulo pode enviar qualquer JSON válido que siga as regras DTDL v2:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Exemplo de carga de propriedade desejada:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Primeira carga da propriedade relatada de exemplo:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Carga de segundo da propriedade relatada de exemplo:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Propriedade gravável de vários componentes de exemplo

O dispositivo ou módulo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento refere-se a um componente.

O marcador é enviado somente para atualizações de propriedades definidas em um componente. As atualizações das propriedades definidas no componente padrão não incluem o marcador, consulte [Propriedade sem gravação de exemplo para componente](#sample-no-component-writable-property)

Quando um dispositivo recebe várias propriedades relatadas em uma única carga, ele pode enviar as respostas de propriedade relatadas entre várias cargas.

O dispositivo ou módulo deve confirmar que recebeu as propriedades enviando Propriedades relatadas:

DTDL que faz referência a um componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL que define o componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Exemplo de carga de propriedade desejada:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Primeira carga da propriedade relatada de exemplo:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Carga de segundo da propriedade relatada de exemplo:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Comandos

Nenhuma interface de componente usa o nome de comando sem um prefixo.

Em um dispositivo ou módulo, várias interfaces de componente usam nomes de comando com o seguinte formato: `componentName*commandName` .

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre as convenções de Plug and Play IoT, aqui estão alguns recursos adicionais:

- [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK do dispositivo C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](/rest/api/iothub/device)
- [Guia de modelagem de Plug and Play de IoT](concepts-modeling-guide.md)