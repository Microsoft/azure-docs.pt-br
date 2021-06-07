---
title: Usar dados de localização em uma solução de IoT Central do Azure
description: Saiba como usar os dados de local enviados de um dispositivo conectado ao seu aplicativo IoT Central. Plote dados de localização em um mapa ou crie regras de isolamento geográfico.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98127991"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Usar dados de localização em uma solução de IoT Central do Azure

Este artigo mostra como usar dados de local em um aplicativo IoT Central. Um dispositivo conectado a IoT Central pode enviar dados de local como fluxo de telemetria ou usar uma propriedade de dispositivo para relatar dados de localização.

Um Solution Builder pode usar os dados de localização para:

* Plotar o local relatado em um mapa.
* Plote o histórico de localização de telemetria OM um mapa.
* Crie regras de isolamento geográfico para notificar um operador quando um dispositivo entrar ou sair de uma área específica.

## <a name="add-location-capabilities-to-a-device-template"></a>Adicionar recursos de localização a um modelo de dispositivo

A captura de tela a seguir mostra um modelo de dispositivo com exemplos de uma propriedade de dispositivo e um tipo de telemetria que usam dados de localização. As definições usam o tipo semântico de **local** e o tipo de esquema **geolocalização** :

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Captura de tela mostrando a definição da propriedade local no modelo do dispositivo":::

Para referência, as definições de [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para esses recursos são semelhantes ao seguinte trecho:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> O tipo de esquema de **ponto** não faz parte da [especificação DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). O IoT Central atualmente dá suporte ao tipo de esquema **geopoint** e ao tipo semântico de **localização** para compatibilidade com versões anteriores.

## <a name="send-location-data-from-a-device"></a>Enviar dados de local de um dispositivo

Quando um dispositivo envia dados para a propriedade **DeviceLocation** mostrada na seção anterior, a carga é semelhante ao seguinte trecho JSON:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Quando um dispositivo envia dados para a telemetria de **rastreamento** mostrada na seção anterior, a carga é semelhante ao seguinte trecho de JSON:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Exibir local do dispositivo

Você pode exibir dados de localização em vários locais em seu aplicativo IoT Central. Por exemplo, em exibições associadas a um dispositivo individual ou a painéis.

Ao criar um modo de exibição para um dispositivo, você pode optar por plotar o local em um mapa ou mostrar os valores individuais:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Captura de tela mostrando exibição de exemplo com dados de localização":::

Você pode adicionar blocos de mapa a um painel para plotar o local de um ou mais dispositivos. Ao adicionar um bloco de mapa para mostrar a telemetria de localização, você pode plotar o local em um período de tempo. A captura de tela a seguir mostra o local relatado por um dispositivo simulado nos últimos 30 minutos:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Captura de tela mostrando painel de exemplo com dados de localização":::

## <a name="create-a-geofencing-rule"></a>Criar uma regra de isolamento geográfica

Você pode usar a telemetria de localização para criar uma regra de isolamento geográfica que gera um alerta quando um dispositivo passa para dentro ou para fora de uma área retangular. A captura de tela a seguir mostra uma regra que usa quatro condições para definir uma área retangular usando valores de latitude e longitude. A regra gera um email quando o dispositivo passa para a área retangular:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Captura de tela que mostra uma definição de regra de isolamento geográfico":::

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar as propriedades em seu aplicativo de IoT Central do Azure, consulte:

* [Cargas](concepts-telemetry-properties-commands.md)
* [Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central](tutorial-connect-device.md)