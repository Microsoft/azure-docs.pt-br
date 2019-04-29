---
title: Criar um modelo de dispositivo simulado avançado - Azure | Microsoft Docs
description: Neste guia prático, você aprende como criar um modelo de dispositivo avançado para uso com o acelerador de solução do Device Simulation.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/18/2019
ms.author: troyhop
ms.openlocfilehash: 4401d4b93a27e76554368ce72d256b38de61df4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448967"
---
# <a name="create-an-advanced-device-model"></a>Criar um modelo de dispositivo avançados

Este guia prático descreve os arquivos JSON e JavaScript que definem um modelo de dispositivo personalizado. O artigo inclui alguns arquivos de definição de modelo de dispositivo de amostra e mostra como fazer o upload deles para sua instância de simulação de dispositivo. Você pode criar modelos de dispositivos avançado para simular os comportamentos de dispositivo mais realistas para seus testes.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste guia prático, você precisa de uma instância implantada do Device Simulation em sua assinatura do Azure.

Se você ainda não implantou o Device Simulation, deverá concluir o [Implantar e executar uma simulação de dispositivo IoT no início rápido do Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Abrir a Simulação de Dispositivo

Para executar a Simulação de Dispositivo no navegador, primeiro navegue até [Aceleradores de Solução do Microsoft Azure IoT](https://www.azureiotsolutions.com).

Você pode ser solicitado a entrar usando suas credenciais da assinatura do Azure.

Em seguida, clique em **Ativar** na peça da Simulação de Dispositivo que você implantou no [Implantar e executar uma simulação de dispositivo IoT no início rápido do Azure](quickstart-device-simulation-deploy.md).

## <a name="device-models"></a>Modelos de dispositivo

Cada dispositivo simulado pertence a um modelo de dispositivo específico que define o comportamento da simulação. Esse comportamento inclui com que frequência enviar telemetria, que tipo de mensagens enviar e os métodos com suporte.

Você define um modelo de dispositivo usando um arquivo de definição de dispositivo JSON e um conjunto de arquivos JavaScript. Esses arquivos JavaScript definem o comportamento da simulação, como a telemetria aleatória e a lógica do método.

Tem um modelo de dispositivo típico:

* Um arquivo JSON para cada modelo de dispositivo (por exemplo, elevator.json).
* Um arquivo de script de comportamento JavaScript para cada modelo de dispositivo (por exemplo, elevator-state.js)
* Um arquivo de script de método JavaScript para cada método de dispositivo (por exemplo, elevator-go-down.js)

> [!NOTE]
> Nem todos os modelos de dispositivos definem métodos. Portanto, um modelo de dispositivo pode ou não ter scripts de método. No entanto, todos os modelos de dispositivos devem ter um script de comportamento.

## <a name="device-definition-file"></a>Arquivo de definição de dispositivo

Cada arquivo de definição de dispositivo contém detalhes de um modelo de dispositivo simulado, incluindo as seguintes informações:

* Nome do modelo do dispositivo: cadeia de caracteres.
* Protocolo: AMQP | MQTT | HTTP.
* O estado inicial do dispositivo.
* Com que frequência atualizar o estado do dispositivo.
* Qual arquivo JavaScript a ser usado para atualizar o estado do dispositivo.
* Uma lista de mensagens de telemetria para enviar, cada uma com uma frequência específica.
* O esquema das mensagens de telemetria, usado pelo aplicativo de back-end para analisar a telemetria recebida.
* Uma lista de métodos suportados e o arquivo JavaScript a ser usado para simular cada método.

### <a name="file-schema"></a>Esquema de arquivo

A versão do esquema é sempre "1.0.0" e é específica para o formato deste arquivo:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Descrição do modelo do dispositivo

As propriedades a seguir descrevem o modelo do dispositivo. Cada tipo tem um identificador único, uma versão semântica, um nome e uma descrição:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protocolo de IoT

Os dispositivos de IoT podem se conectar usando protocolos diferentes. A simulação permite usar **AMQP**, **MQTT** ou **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Estado do dispositivo simulado

Cada dispositivo simulado possui um estado interno, que deve ser definido. O estado também define as propriedades que podem ser relatadas na telemetria. Por exemplo, um resfriador pode ter um estado inicial, como:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Um dispositivo móvel com vários sensores pode ter mais propriedades, por exemplo:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

O estado do dispositivo é mantido na memória pelo serviço de simulação e fornecido como entrada para a função JavaScript. A função do JavaScript poderia decidir:

* Ignorar o estado e gerar alguns dados aleatórios.
* Para atualizar o estado do dispositivo de alguma maneira realista para um determinado cenário.

A função que gera o estado também recebe como entrada:

* A ID do dispositivo.
* O modelo do dispositivo.
* A hora atual. Esse valor possibilita gerar dados diferentes por dispositivo e por tempo.

### <a name="generating-telemetry-messages"></a>Gerando mensagens de telemetria

O serviço de simulação pode enviar vários tipos de telemetria para cada dispositivo. Normalmente, a telemetria inclui dados do estado do dispositivo. Por exemplo, uma sala simulada pode enviar informações sobre temperatura e umidade a cada 10 segundos. Observe os espaços reservados no snippet a seguir, que são substituídos automaticamente pelos valores do estado do dispositivo:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Os espaços reservados usam uma sintaxe especial **$ {NAME}**, onde **NAME** é uma chave do objeto de estado do dispositivo retornado pela função **principal** do JavaScript. As strings devem ser citadas, enquanto os números não devem.

#### <a name="message-schema"></a>Esquema de mensagem

Cada tipo de mensagem deve ter um esquema bem definido. O esquema da mensagem também é publicado no Hub IoT, para que os aplicativos de back-end possam reutilizar as informações para interpretar a telemetria recebida.

O esquema suporta o formato JSON, que permite fácil análise, transformação e análise em vários sistemas e serviços.

Os campos listados no esquema podem ser dos tipos a seguir:

* Objeto - serializado usando JSON
* Binário - serializado usando base64
* Text
* Boolean
* Número inteiro
* Double
* DateTime

### <a name="supported-methods"></a>Métodos com suporte

Dispositivos simulados também podem reagir a chamadas de método, caso em que executam alguma lógica e fornecem alguma resposta. Da mesma forma que a simulação, a lógica do método é armazenada em um arquivo JavaScript e pode interagir com o estado do dispositivo. Por exemplo: 

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Crie um arquivo de definição de dispositivo

Neste how-to-guia, você verá como criar um modelo de dispositivo para um drone. O drone voará aleatoriamente em torno de um conjunto inicial de coordenadas, alterando a localização e a altitude.

Copie o seguinte JSON em um editor de texto e salve-o como **drone.json**.

### <a name="device-definition-json-example"></a>Exemplo de JSON de definição de dispositivo

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Arquivos de script de comportamento

O código no arquivo de script de comportamento move o drone. O script altera a elevação e localização do drone manipulando o estado da memória do dispositivo.

Os arquivos JavaScript devem ter uma função **principal**, que aceita dois parâmetros:

* Um **contexto** objeto que contém três propriedades:
    * **currentTime** como uma cadeia de caracteres com o formato **aaaa-MM-dd'T'HH: mm: sszzz**.
    * **deviceId**. Por exemplo, **Simulated.Elevator.123**.
    * **deviceModel**. Por exemplo, **elevador**.
* Um **estado** objeto que é o valor retornado pela função na chamada anterior. Esse estado do dispositivo é mantido pelo serviço de simulação e usado para gerar mensagens de telemetria.

A função **principal** retorna o novo estado do dispositivo. Por exemplo: 

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Crie um arquivo de script de comportamento

Copie o seguinte JavaScript em um editor de texto e salve-o como **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Exemplo de simulação do dispositivo modelo JavaScript

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Crie um arquivo de script de método

Os scripts de métodos são semelhantes aos scripts de comportamento. Eles definem o comportamento do dispositivo quando um método específico de nuvem para dispositivo é chamado.

O script de recall do drone define as coordenadas do drone para um ponto fixo para simular o drone voltando para casa.

Copie o seguinte JavaScript em um editor de texto e salve-o como **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Exemplo de simulação do dispositivo modelo JavaScript

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Depuração dos arquivos de script

Embora você não possa anexar um depurador a um arquivo de comportamento em execução, é possível gravar informações no log de serviço usando a função **log**. Erros de sintaxe, o interpretador falha e grava informações sobre a exceção no log.

Exemplo de registro em log:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Implantar um modelo de dispositivo avançado

Para implantar seu modelo de dispositivo avançado, faça o upload dos arquivos da sua instância de Simulação do dispositivo:

Selecione **Modelos de dispositivos** na barra de menus. A página **Modelos de dispositivos** lista os modelos de dispositivos disponíveis nesta instância da Simulação de dispositivos:

![Modelos de dispositivo](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Clique em **+ Adicionar Modelos de Dispositivo** no canto superior direito da página:

![Adicionar modelo de dispositivo](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Clique em **Avançado** para abrir a guia de modelo avançado do dispositivo:

![Guia Avançado](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Clique em **Procurar** e selecione os arquivos JSON e JavaScript que você criou. Certifique-se de selecionar todos os três arquivos. Se algum arquivo estiver faltando, a validação falhará:

![Procurar arquivos](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Se seus arquivos passarem pela validação, clique em **Salvar** e seu modelo de dispositivo estará pronto para ser usado em uma simulação. Caso contrário, corrija os erros e recarregar os arquivos:

![Salvar](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia prático, você aprendeu sobre os arquivos de modelo de dispositivo usados na Simulação de dispositivos e como criar um modelo de dispositivo avançado. Em seguida, você pode explorar como [Usar as Estatísticas da Série Temporal para visualizar a telemetria enviada a partir do acelerador da solução Simulação de Dispositivo](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
