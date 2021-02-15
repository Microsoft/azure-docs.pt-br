---
title: Desenvolver sem um SDK do Azure IoT | Microsoft Docs
description: Guia do desenvolvedor-informações e links para tópicos que você pode usar para criar aplicativos de dispositivo e aplicativos de back-end sem usar um SDK do Azure IoT.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461714"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Desenvolver sem usar um SDK do Hub IoT do Azure

Este tópico fornece informações úteis e links para desenvolvedores que desejam desenvolver dispositivos ou aplicativos de back-end sem usar os SDKs do IoT do Azure.

A Microsoft recomenda enfaticamente o uso de um SDK do Azure IoT. Os SDKs do dispositivo e do serviço do Azure IoT são publicados em muitas plataformas populares. Os SDKs fornecem uma camada de conveniência que lida com grande parte da complexidade do protocolo de comunicação subjacente, incluindo conexão e reconexão do dispositivo e política de repetição. Os SDKs são atualizados regularmente para fornecer os recursos mais recentes expostos pelo Hub IoT, bem como atualizações de segurança. O uso dos SDKs pode ajudá-lo a reduzir o tempo e o tempo de desenvolvimento dedicados à manutenção do código. Para saber mais sobre os SDKs de IoT do Azure, confira [SDKs de serviço e dispositivo IOT do Azure](iot-hub-devguide-sdks.md). Para obter mais detalhes sobre as vantagens de usar um SDK do Azure IoT, consulte os [benefícios de usar os SDKs e armadilhas do IOT do Azure para evitar se você não](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) publicar no blog.

Embora o Hub IoT dê suporte a AMQP, AMQP sobre WebSockets, HTTPS, MQTT e MQTT sobre Websockets para comunicação com dispositivos, é recomendável usar MQTT se o seu dispositivo oferecer suporte a ele.

## <a name="development-prerequisites"></a>Pré-requisitos de desenvolvimento

Antes de começar o desenvolvimento, você deve ter um conhecimento completo do Hub IoT e os recursos que deseja que seu dispositivo ou aplicativo de back-end implemente. Veja uma lista bem abreviada de tópicos com os quais você deve estar familiarizado:

* Certifique-se de que você entendeu os pontos de extremidade expostos pelo Hub IoT e os protocolos com suporte em cada um deles. Para saber mais, confira [pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md).

* Quando uma opção de protocolo está envolvida para aplicativos de dispositivo, é altamente recomendável que você use o MQTT. Antes de escolher um protocolo, no entanto, certifique-se de entender as limitações impostas por cada uma delas. Para saber mais, consulte [escolher um protocolo de comunicação](iot-hub-devguide-protocols.md).

* Para entender a autenticação com o Hub IoT, consulte [controlar o acesso ao Hub IOT](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Ajuda em diferentes protocolos

Para obter ajuda sobre como usar os seguintes protocolos sem um SDK do Azure IoT:

* Aplicativos de back-end ou dispositivo no **AMQP**, consulte [suporte do AMQP](iot-hub-amqp-support.md).

* Aplicativos de dispositivo no **MQTT**, consulte [suporte do MQTT](iot-hub-mqtt-support.md). A maior parte deste tópico trata do uso direto do protocolo MQTT. Ele também contém informações sobre como usar o [repositório de exemplo MQTT de IOT](https://github.com/Azure-Samples/IoTMQTTSample). Este repositório contém exemplos de C que usam a biblioteca mosquitto do Eclipse para enviar mensagens ao Hub IoT.

* Dispositivos ou aplicativos de back-end em **https**, consulte as [APIs REST do Hub IOT do Azure](/rest/api/iothub/). Lembre-se de que, conforme observado em [pré-requisitos de desenvolvimento](#development-prerequisites), não é possível usar a autenticação de AC (autoridade de certificação) X. 509 com HTTPS.

Para dispositivos, é altamente recomendável usar MQTT se o seu dispositivo der suporte a ele.

## <a name="next-steps"></a>Próximas etapas

* [Suporte do MQTT](iot-hub-mqtt-support.md)