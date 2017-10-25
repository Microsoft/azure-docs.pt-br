---
title: Compreender mensagens de dispositivo para a nuvem do Hub IoT do Azure | Microsoft Docs
description: "Guia do desenvolvedor – como usar mensagens de dispositivo para a nuvem com o Hub IoT. Inclui informações sobre o envio de dados de telemetria e não telemetria e o uso do direcionamento para entregar mensagens."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: 4e346306ecb8f4897a249454c537ce9a1a4c4011
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Enviar mensagens de dispositivo para a nuvem para o Hub IoT

Para enviar telemetria de série temporal e alertas de dispositivos ao back-end da solução, envie mensagens de dispositivo para a nuvem do dispositivo para o hub IoT. Para ver uma discussão sobre outras opções de dispositivo para a nuvem com suporte no Hub IoT, confira [Orientação sobre comunicações de dispositivo para a nuvem][lnk-d2c-guidance].

Você envia mensagens do dispositivo para a nuvem por meio de um ponto de extremidade voltado para o dispositivo (**/devices/{deviceId}/messages/events**). As regras de roteamento roteiam as mensagens para um dos pontos de extremidade voltados para o serviço em seu Hub IoT. As regras de roteamento usam os cabeçalhos e o corpo das mensagens de dispositivo para a nuvem que fluem através de seu hub para determinar para onde roteá-las. Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) compatíveis com [Hubs de Eventos][lnk-event-hubs]. Portanto, você pode usar os [SDKs e integração com Hubs de Evento][lnk-compatible-endpoint] Standard para receber mensagens de dispositivo para a nuvem no back-end da solução.

O Hub IoT implementa mensagens de dispositivo para nuvem usando um padrão de sistema de mensagens de streaming. As mensagens do dispositivo para nuvem do Hub IoT são mais semelhantes a *eventos* de [Hubs de Eventos][lnk-event-hubs] do que a *mensagens* do [Barramento de Serviço][lnk-servicebus] na medida em que há um alto volume de eventos passando pelo serviço que pode ser lido por vários leitores.

As mensagens de dispositivo para a nuvem com o Hub IoT têm as seguintes características:

* As mensagens do dispositivo para a nuvem são duráveis e mantidas em um ponto de extremidade de **mensagens/eventos** padrão do Hub IoT por até sete dias.
* As mensagens de dispositivo para a nuvem podem ter no máximo 256 KB e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 KB.
* Como explicado na seção [Controlar o acesso ao Hub IoT][lnk-devguide-security], o Hub IoT habilita a autenticação e o controle de acesso por dispositivo.
* O Hub IoT permite que você crie até 10 pontos de extremidade personalizados. As mensagens são entregues aos pontos de extremidade com base nas rotas configuradas em seu Hub IoT. Para saber mais, confira [Regras de direcionamento](#routing-rules).
* O Hub IoT habilita milhões de dispositivos conectados simultaneamente (confira [Cotas e limitação][lnk-quotas]).
* O Hub IoT não permite o particionamento arbitrário. As mensagens do dispositivo para a nuvem são particionadas com base em sua **deviceId**de origem.

Para saber mais sobre as diferenças entre os serviços de Hubs de Eventos e o Hub IoT, confira [Comparação do Hub IoT do Azure e Hubs de Eventos do Azure][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Enviar tráfego sem telemetria

Muitas vezes, além dos pontos de dados de telemetria, os dispositivos enviam mensagens e solicitações que exigem execução separada e manipulação no back-end de solução. Por exemplo, alertas críticos que devem disparar uma ação específica no back-end. Você pode escrever facilmente uma [regra de direcionamento][lnk-devguide-custom] para enviar esses tipos de mensagens a um ponto de extremidade dedicado ao respectivo processamento, com base em um cabeçalho da mensagem ou um valor no corpo da mensagem.

Para obter mais informações sobre a melhor maneira de processar esse tipo de mensagem, consulte o [Tutorial: como processar mensagens do dispositivo para a nuvem do Hub IoT][lnk-d2c-tutorial].

## <a name="route-device-to-cloud-messages"></a>Encaminhar mensagens do dispositivo para a nuvem

Você tem duas opções para direcionamento de mensagens de dispositivo para a nuvem para os aplicativos de back-end:

* Use o [ponto de extremidade compatível com o Hub de Eventos][lnk-compatible-endpoint] interno para habilitar os aplicativos de back-end para ler as mensagens de dispositivo para a nuvem recebidas pelo hub. Para saber mais sobre o ponto de extremidade compatível com o Hub de Eventos interno, confira [Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno][lnk-devguide-builtin].
* Use regras de direcionamento para enviar mensagens a pontos de extremidade personalizados no hub IoT. Os pontos de extremidade personalizados permitem que os aplicativos de back-end a ler mensagens de dispositivo para a nuvem usando Hubs de Eventos, filas do Barramento de Serviço ou tópicos do Barramento de Serviço. Para saber mais sobre pontos de extremidade personalizados e direcionamentos, confira [Usar pontos de extremidade personalizados e regras de direcionamento de mensagens de dispositivo para a nuvem][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Propriedades antifalsificação

Para evitar a falsificação em mensagens do dispositivo para a nuvem, o Hub IoT carimba todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

As duas primeiras contêm a **deviceId** e a **generationId** do dispositivo de origem, conforme as [Propriedades de identidade do dispositivo][lnk-device-properties].

A propriedade **ConnectionAuthMethod** contém um objeto JSON serializado com as seguintes propriedades:

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os SDKs que você pode usar para enviar mensagens de dispositivo para a nuvem, confira [SDKs do Azure IoT][lnk-sdks].

Os tutoriais de [Introdução][lnk-get-started] mostram como enviar mensagens de dispositivo para a nuvem de dispositivos simulados e físicos. Para saber mais, confira o tutorial [Como processar as mensagens entre o dispositivo e a nuvem do Hub IoT usando rotas][lnk-d2c-tutorial].

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
