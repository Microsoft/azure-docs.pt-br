---
title: SDKs e bibliotecas de Plug and Play de IoT
description: Informações sobre as bibliotecas de dispositivo e serviço disponíveis para o desenvolvimento de soluções habilitadas para IoT Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3ff01455c56dd259ea64878f912eb3f2431547e7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495244"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDKs para IoT Plug and Play

As bibliotecas e SDKs do IoT Plug and Play permitem que os desenvolvedores criem soluções de IoT usando uma variedade de linguagens de programação em várias plataformas. A tabela a seguir inclui links para exemplos e guias de início rápido para ajudá-lo a começar:

## <a name="device-sdks"></a>SDKs de dispositivo

| Linguagem | Pacote | Repositório de código | Exemplos | Guia de Início Rápido | Referência |
|---|---|---|---|---|---|
| Dispositivo C | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Amostras](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Conectar ao Hub IoT](quickstart-connect-device.md) | [Referência](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-dispositivo | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Conectar ao Hub IoT](quickstart-connect-device.md) | [Referência](/dotnet/api/microsoft.azure.devices.client?preserve-view=true&view=azure-dotnet) |
| Dispositivo Java | [1.26.0 Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Conectar ao Hub IoT](quickstart-connect-device.md) | [Referência](/java/api/com.microsoft.azure.sdk.iot.device?preserve-view=true&view=azure-java-stable) |
| Python-dispositivo | [2.3.0 Pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Conectar ao Hub IoT](quickstart-connect-device.md) | [Referência](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| Nó-dispositivo | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Conectar ao Hub IoT](quickstart-connect-device.md) | [Referência](/javascript/api/azure-iot-device/?preserve-view=true&view=azure-node-latest) |
| C-dispositivo inserido | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Amostras](howto-use-embedded-c.md#samples) | [Como usar C inserido](howto-use-embedded-c.md) | N/D

## <a name="service-sdks"></a>SDKs do Serviço

| Plataforma  | Pacote | Repositório de código | Exemplos | Guia de Início Rápido | Referência |
|---|---|---|---|---|---|
| .NET-serviço de Hub IoT | [1.27.1 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/D | [Referência](/dotnet/api/microsoft.azure.devices?preserve-view=true&view=azure-dotnet) |
| Java-serviço de Hub IoT | [1.26.0 Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/D | [Referência](/java/api/com.microsoft.azure.sdk.iot.service?preserve-view=true&view=azure-java-stable) |
| Nó-serviço de Hub IoT | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/D | [Referência](/javascript/api/azure-iothub/?preserve-view=true&view=azure-node-latest) |
| Python – serviço gêmeos digital | [2.2.3 Pip](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interagir com a API gêmeos digital do Hub IoT](quickstart-service.md) | N/D |
| Nó-serviço gêmeos digital | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interagir com a API gêmeos digital do Hub IoT](quickstart-service.md) | N/D |

## <a name="next-steps"></a>Próximas etapas

Para experimentar os SDKs e as bibliotecas, consulte o  [Guia do desenvolvedor](concepts-developer-guide-device.md) e os guias de início rápido do [dispositivo](quickstart-connect-device.md) e os guias de [início rápido do serviço](quickstart-service.md).