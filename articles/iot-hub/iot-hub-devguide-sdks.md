---
title: Entender os SDKs do IoT Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ Informações e links para os vários SDKs de serviço e de dispositivo do IoT do Azure que você pode usar para criar os aplicativos do dispositivo e de back-end.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 9921ed500f831f2a002335fe83eae8f1bb47ecd4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095331"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Entender e usar os SDKs de Hub IoT do Azure

Há duas categorias de SDKs (Kits de desenvolvimento de software) para trabalhar com o Hub IoT:

* Os **SDKs de dispositivo do Hub IOT** permitem que você crie aplicativos que são executados em seus dispositivos IOT usando o cliente do dispositivo ou o cliente do módulo. Esses aplicativos enviam telemetria ao Hub IoT e, opcionalmente, recebem mensagens, trabalho, método ou atualizações de gêmeos de seu Hub IoT.  Você também pode usar o cliente do módulo para criar [módulos](../iot-edge/iot-edge-modules.md) para [runtime do Azure IoT Edge](../iot-edge/about-iot-edge.md).

* Os **SDKs de serviço do Hub IOT** permitem criar aplicativos de back-end para gerenciar o Hub IOT e, opcionalmente, enviar mensagens, agendar trabalhos, invocar métodos diretos ou enviar atualizações de propriedades desejadas para seus dispositivos ou módulos IOT.

Além disso, também fornecemos um conjunto de SDKs para trabalhar com o [Serviço de Provisionamento de Dispositivos](../iot-dps/about-iot-dps.md).

* **SDKs de Provisionamento de Dispositivos** permitem que você crie aplicativos que são executados em seus dispositivos IoT para se comunicarem com o Serviço de Provisionamento de Dispositivos.

* **SDKs de Serviços de Provisionamento** permitem que você crie aplicativos de back-end para gerenciar seus registros no Serviço de Provisionamento de Dispositivos.

Saiba mais sobre os [benefícios do desenvolvimento usando SDKs de IoT do Azure ](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade de plataforma do sistema operacional e hardware

Plataformas com suporte para os SDKs que podem ser encontradas no [suporte de plataforma de SDKs de IoT do Azure](iot-hub-device-sdk-platform-support.md).

Para saber mais sobre a compatibilidade do SDK com dispositivos de hardware específicos, consulte o [Catálogo de dispositivos certificados pelo Azure para IoT](https://catalog.azureiotsolutions.com/) ou repositório individual.

## <a name="azure-iot-hub-device-sdks"></a>SDKs de dispositivos do Hub IoT do Azure

Os SDKs de dispositivos IoT do Microsoft Azure contêm códigos que facilitam a criação de aplicativos que se conectam e são gerenciados pelos serviços do Hub IoT do Azure.

SDK do dispositivo de Hub IoT do Azure para .NET: 

* Baixar do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  O namespace é Microsoft.Azure.Devices.Clients, que contém os Clientes de Dispositivos do Hub IoT (DeviceClient, ModuleClient).
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência de API](/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Referência de módulo](/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)


SDK do dispositivo do Hub IoT do Azure para C inserido (ANSI C-C99):
* [Compilar o SDK do C inserido](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Código-fonte](https://github.com/Azure/azure-sdk-for-c)
* [Gráfico de tamanho](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart) para dispositivos restritos.
* [Referência de API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Identity/1.0.0/api/index.html)


SDK de dispositivos do Hub IoT do Azure para C (ANSIC - C99):

* Instale a partir de [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-c)
* [Compilar o SDK do dispositivo C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Referência de API](/azure/iot-hub/iot-c-sdk-ref/)
* [Referência de módulo](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Portabilidade do SDK C para outras plataformas](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentação do desenvolvedor](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) para saber mais sobre compilação cruzada, introdução em diferentes plataformas, etc.
* [Informações de consumo de recursos do SDK C do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

SDK do dispositivo de Hub IoT do Azure para Java:

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência de API](/java/api/com.microsoft.azure.sdk.iot.device)
* [Referência de módulo](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

SDK do dispositivo de Hub IoT do Azure para Node.js:

* Instalar do arquivo [npm](https://www.npmjs.com/package/azure-iot-device)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência de API](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Referência de módulo](/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

SDK do dispositivo de Hub IoT do Azure para Python:

* Instalar do arquivo [pip](https://pypi.org/project/azure-iot-device/)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-python)
* [Referência de API](/python/api/azure-iot-device)

SDK do dispositivo do Hub IoT para iOS:

* Instalar de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Referência da API: consulte [referência da API C](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>SDKs de serviços do Hub IoT

Os SDKs do serviço de IoT do Azure contêm código para facilitar a criação aplicativos que interagem diretamente com o Hub IoT para gerenciar dispositivos e a segurança.

SDK de serviço de Hub IoT do Azure para .NET:

* Baixar do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  O namespace é Microsoft.Azure.Devices, que contém os Clientes de Dispositivos do Hub IoT (RegistryManager, ServiceClients).
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência de API](/dotnet/api/microsoft.azure.devices)

SDK de serviço de Hub IoT do Azure para Java:

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência de API](/java/api/com.microsoft.azure.sdk.iot.service)

SDK de serviço de Hub IoT do Azure para Node.js:

* Fazer download do [npm](https://www.npmjs.com/package/azure-iothub)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência de API](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

SDK de serviço de Hub IoT do Azure para Python:

* Fazer download do [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [Referência de API](/python/api/azure-iot-hub)

SDK de serviço de Hub IoT do Azure para C:

O SDK do serviço IoT do Azure para C não está mais em desenvolvimento ativo.
Continuaremos a corrigir bugs críticos, como falhas, corrupção de dados e vulnerabilidades de segurança. No entanto, não adicionaremos nenhum novo recurso ou corrigiremos bugs que não são críticos.

O suporte do SDK do serviço IOT do Azure está disponível em linguagens de nível superior ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [node](https://github.com/Azure/azure-iot-sdk-node), [Python](https://github.com/Azure/azure-iot-sdk-python)).

* Baixar de [apt-get, MBED, ARDUINO IDE ou NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-c)

SDK do serviço do Hub IoT para iOS:

* Instalar de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos à linguagem e à plataforma para instalar binários e dependências em seu computador de desenvolvimento.

## <a name="microsoft-azure-provisioning-sdks"></a>SDKs de provisionamento do Microsoft Azure

As **SDKs de provisionamento do Microsoft Azure** permitem que você provisione dispositivos ao Hub IoT usando o [serviço de provisionamento de dispositivo](../iot-dps/about-iot-dps.md).

Dispositivo de Provisionamento do Azure e SDKs de serviço para C#:

* Baixe a [SDK de Dispositivo](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) e a [SDK de Serviço](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) no NuGet.
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Referência de API](/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Dispositivo de Provisionamento do Azure e SDKs de serviço para C:

* Instale a partir de [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Referência de API](/azure/iot-hub/iot-c-sdk-ref/)

Dispositivo de Provisionamento do Azure e SDKs de serviço para Java:

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Referência de API](/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Dispositivo de Provisionamento do Azure e SDKs de serviço para Node.js:

* [Código-fonte](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Referência de API](/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Baixe a [SDK de Dispositivo](https://badge.fury.io/js/azure-iot-provisioning-device) e a [SDK de Serviço](https://badge.fury.io/js/azure-iot-provisioning-service) no npm

Dispositivo de Provisionamento do Azure e SDKs de serviço para Python:

* [Código-fonte](https://github.com/Azure/azure-iot-sdk-python)
* Baixe a [SDK de Dispositivo](https://pypi.org/project/azure-iot-device/) e a [SDK de Serviço](https://pypi.org/project/azure-iothub-provisioningserviceclient/) no pip

## <a name="next-steps"></a>Próximas etapas

Os SDKs de IoT do Azure também fornecem um conjunto de ferramentas para ajudá-lo com o desenvolvimento:

* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): uma ferramenta de linha de comando de plataforma cruzada para ajudar a diagnosticar problemas relacionados à conexão com o IoT Hub.
* [Azure-IOT-Explorer](https://github.com/Azure/azure-iot-explorer): um aplicativo de área de trabalho de plataforma cruzada para se conectar ao Hub IOT e adicionar/gerenciar/se comunicar com dispositivos IOT.

Documentos relevantes relacionados ao desenvolvimento usando as SDKs de IoT do Azure:

* Consulte [como gerenciar a conectividade e mensagens confiáveis](iot-hub-reliability-features-in-sdks.md) usando SDKs do Hub IoT.
* Saiba mais sobre como [desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte de plataforma do SDK do IoT do Azure](iot-hub-device-sdk-platform-support.md)

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md)
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md)
* [Cotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md)
* [Referência de API REST do Hub IoT](/rest/api/iothub/)
