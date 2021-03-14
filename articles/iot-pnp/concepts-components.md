---
title: Entender os componentes em modelos de Plug and Play IoT | Microsoft Docs
description: Entenda a diferença entre os modelos de IoT Plug and Play DTDL que usam componentes e modelos que não usam componentes.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: eef8179567d83e3727c3ab949eef2706ce2a9b16
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175797"
---
# <a name="iot-plug-and-play-components-in-models"></a>Componentes de IoT Plug and Play em modelos

Nas convenções de IoT Plug and Play, um dispositivo será um dispositivo IoT Plug and Play se apresentar sua ID de modelo de DTDL (linguagem de definição de gêmeos digitais) quando se conectar a um hub IoT.

O trecho a seguir mostra algumas IDs de modelo de exemplo:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Nenhum componente

Um modelo simples não usa componentes incorporados ou em cascata. Ele inclui informações de cabeçalho e uma seção de conteúdo para definir telemetria, propriedades e comandos.

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

Embora o modelo não defina explicitamente um componente, ele se comporta como se há um único _componente padrão_, com todas as definições de telemetria, propriedade e comando.

A captura de tela a seguir mostra como o modelo é exibido na ferramenta do Azure IoT Explorer:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Componente padrão no Azure IoT Explorer":::

A ID do modelo é armazenada em uma propriedade mydo dispositivo, como mostra a captura de tela a seguir:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="ID do modelo na propriedade de propriedades digitais":::

Um modelo de DTDL sem componentes é uma simplificação útil para um dispositivo ou IoT Edge módulo com um único conjunto de comandos, propriedades e telemetria. Um modelo que não usa componentes facilita a migração de um dispositivo ou módulo existente para ser um dispositivo IoT Plug and Play ou módulo-você cria um modelo DTDL que descreve seu dispositivo ou módulo real sem a necessidade de definir quaisquer componentes.

> [!TIP]
> Um módulo pode ser um [módulo](../iot-hub/iot-hub-devguide-module-twins.md) de dispositivo ou um [módulo IOT Edge](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Vários componentes

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
...
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
...
```

Esse modelo tem três componentes definidos na seção conteúdo-dois `Thermostat` componentes e um `DeviceInformation` componente. Também há um componente padrão.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre componentes de modelo, aqui estão alguns recursos adicionais:

- [Instalar e usar as ferramentas de criação do DTDL](howto-use-dtdl-authoring-tools.md)
- [DTDL (digital gêmeos Definition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositórios de modelo](./concepts-model-repository.md)
