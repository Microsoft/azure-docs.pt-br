---
title: Entender os SDKs do IoT Azure | Microsoft Docs
description: "Guia do desenvolvedor ‑ Informações e links para os vários SDKs de serviço e de dispositivo do IoT do Azure que você pode usar para criar os aplicativos do dispositivo e de back-end."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e299de0953cefac925b0015a15983d25d456576f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-use-azure-iot-sdks"></a>Entender e usar os SDKs do Azure IoT

Há três categorias de SDKs (Kits de desenvolvimento de software) para trabalhar com o Hub IoT:

* **SDKs de dispositivo** permitem a compilação de aplicativos que são executados em dispositivos IoT. Esses aplicativos enviam telemetria ao Hub IoT e, opcionalmente, recebem mensagens de seu Hub IoT.

* **SDKs de serviço** permitem o gerenciamento de seu Hub IoT e, opcionalmente, o envio de mensagens para seus dispositivos IoT.

* O **Azure IoT Edge** permite que você crie os gateways para dispositivos que não usam um dos protocolos com suporte. Gateways também podem processar mensagens na borda.

Os SDKs são fornecidos a fim de oferecer suporte a várias linguagens de programação.

## <a name="azure-iot-device-sdks"></a>SDKs do dispositivo IoT do Azure

Os SDKs do dispositivo IoT do Microsoft Azure contêm código que facilita a criação de dispositivos e aplicativos que se conectam e são gerenciados pelos serviços do Hub Iot do Azure.

Os seguintes SDKs de dispositivo do Azure IoT estão disponíveis para download no GitHub:

* [SDK do dispositivo IoT do Azure para .NET][lnk-dotnet-device-sdk]
* [SDK do dispositivo IoT do Azure para Java][lnk-java-device-sdk]
* [SDK do dispositivo IoT do Azure para Node.js][lnk-node-device-sdk]
* [SDK do dispositivo IoT do Azure para Python][lnk-python-device-sdk]
* [Dispositivo SDK do dispositivo IoT do Azure para C][lnk-c-device-sdk] gravado em ANSI C (C99) para portabilidade e compatibilidade ampla da plataforma. Há duas bibliotecas de cliente de dispositivo para C, de baixo nível **iothub_client** e o **serializador**.

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos à linguagem e à plataforma para instalar binários e dependências em seu computador de desenvolvimento.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade de plataforma do sistema operacional e hardware

Para saber mais sobre a compatibilidade do SDK com dispositivos de hardware específicos, consulte o [Catálogo de dispositivos certificados pelo Azure para IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>SDKs do serviço IoT do Azure

Os SDKs do serviço de IoT do Azure contêm código para facilitar a criação aplicativos que interagem diretamente com o Hub IoT para gerenciar dispositivos e a segurança.

Os seguintes SDKs de serviço do Azure IoT estão disponíveis para download no GitHub:

* [SDK de serviço do Azure IoT para .NET][lnk-dotnet-service-sdk]
* [SDK de serviço do Azure IoT para Java][lnk-java-service-sdk]
* [SDK de serviço do Azure IoT para Node.js][lnk-node-service-sdk]
* [SDK de serviço IoT do Azure para Python][lnk-python-service-sdk]
* [SDK de serviço do Azure IoT para C][lnk-c-service-sdk]

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos à linguagem e à plataforma para instalar binários e dependências em seu computador de desenvolvimento.

## <a name="azure-iot-edge"></a>Azure IoT Edge

O Azure IoT Edge contém a infraestrutura e os módulos para criar soluções de gateway IoT. Você pode estender o IoT Edge para criar gateways adaptados a qualquer cenário completo.

Baixe o [Azure IoT Edge][lnk-iot-edge] no GitHub.

## <a name="online-api-reference-documentation"></a>Documentação de referência online de API

A seguinte lista contém links para a documentação de referência da API online das bibliotecas de dispositivo, serviço e gateway de IoT do Azure:

* [Internet das Coisas (IoT) .NET][lnk-dotnet-ref]
* [SDK do dispositivo IoT do Azure para Java][lnk-java-ref]
* [SDK de serviço do Azure IoT para Java][lnk-java-service-ref]
* [SDK do dispositivo IoT do Azure para Node.js][lnk-node-ref]
* [SDK de serviço do Azure IoT para Node.js][lnk-node-service-ref]
* [SDK do dispositivo IoT do Azure para C][lnk-c-ref]
* [REST do Hub IoT][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>Próximas etapas

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints]
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][lnk-devguide-query]
* [Cotas e limitações][lnk-devguide-quotas]
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
