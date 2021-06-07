---
title: Desenvolver módulos para o Azure IoT Edge | Microsoft Docs
description: Desenvolver módulos personalizados para o Azure IoT Edge que podem se comunicar com o runtime e o IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a30b4b056d56e096f80b9494ab80a585fff76e66
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489910"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolva seus próprios módulos do IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Os módulos do Azure IoT Edge podem conectar outros serviços do Azure e contribuir para o pipeline de dados em nuvem maior. Este artigo descreve como é possível desenvolver módulos para comunicação com o runtime do IoT Edge e Hub IoT e, portanto, com o restante da nuvem do Azure.

## <a name="iot-edge-runtime-environment"></a>Ambiente de runtime do IoT Edge

O runtime do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implantá-los nos dispositivos IoT Edge. Qualquer programa pode ser empacotado como um módulo IoT Edge. Para aproveitar ao máximo as funcionalidades de IoT Edge de comunicação e gerenciamento, um programa em execução em um módulo pode usar o SDK do dispositivo IoT do Azure para se conectar ao Hub de IoT Edge local.
::: moniker range=">=iotedge-2020-11"
Os módulos também podem usar qualquer cliente MQTT para se conectar ao agente de MQTT do hub de IoT Edge local.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Empacotando seu programa como um módulo IoT Edge

Para implantar seu programa em um dispositivo IoT Edge, ele deve primeiro ser em contêiner e ser executado com um mecanismo compatível com o Docker. IoT Edge usa [Moby](https://github.com/moby/moby), o projeto de código-fonte aberto por trás do Docker, como seu mecanismo compatível com o Docker. Os mesmos parâmetros que você está acostumado com o Docker podem ser passados para seus módulos de IoT Edge. Para obter mais informações, consulte [como configurar opções de criação de contêiner para módulos de IOT Edge](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Usar o hub do IoT Edge

O hub do IoT Edge fornece duas funcionalidades principais: proxy para o Hub IoT e comunicações locais.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Conectar-se ao hub do IoT Edge a partir de um módulo

Conectar-se ao Hub de IoT Edge local de um módulo envolve as mesmas etapas de conexão para todos os clientes. Para obter mais informações, consulte [conectando-se ao Hub de IOT Edge](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

Para usar o roteamento de IoT Edge sobre AMQP ou MQTT, você pode usar o ModuleClient do SDK do Azure IoT. Crie uma instância ModuleClient para conectar o módulo ao Hub do IoT Edge em execução no dispositivo, de modo semelhante como as instâncias DeviceClient conectam dispositivos ao Hub IoT. Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para sua linguagem de SDK preferida: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)ou [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para usar IoT Edge agente MQTT, você precisa colocar seu próprio cliente MQTT e iniciar a conexão por conta própria com informações que você recupera da API de carga de trabalho IoT Edge daemon. <!--Need to add details here-->

Para obter mais informações sobre como escolher entre roteamento ou publicação/assinatura com o agente do MQTT, consulte [comunicação local](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>Primitivos do agente MQTT

#### <a name="send-a-message-on-a-user-defined-topic"></a>Enviar uma mensagem em um tópico definido pelo usuário

Com o agente do IoT Edge MQTT, você pode publicar mensagens em qualquer tópico definido pelo usuário. Para fazer isso, autorize seu módulo a publicar em tópicos específicos e, em seguida, obter um token da API de carga de trabalho para usar como uma senha ao se conectar ao agente do MQTT e, por fim, publicar mensagens nos tópicos autorizados com o cliente MQTT de sua escolha.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Receber mensagens em um tópico definido pelo usuário

Com o agente do IoT Edge MQTT, o recebimento de mensagens é semelhante. Primeiro, verifique se o módulo está autorizado a assinar tópicos específicos e, em seguida, obtenha um token da API de carga de trabalho para usar como uma senha ao se conectar ao agente do MQTT e, finalmente, assine as mensagens nos tópicos autorizados com o cliente MQTT de sua escolha.

::: moniker-end

### <a name="iot-hub-primitives"></a>Primitivos do Hub IoT

O Hub IoT vê uma instância de módulo de forma análoga a um dispositivo, no sentido de que:

* pode enviar [mensagens do dispositivo para a nuvem](../iot-hub/iot-hub-devguide-messaging.md);
* pode receber [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) destinados especificamente à sua identidade.
* Ele tem um módulo "My" que é distinto e isolado do [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) e o outro gêmeos de módulo do dispositivo;

Atualmente, os módulos não podem receber mensagens da nuvem para o dispositivo ou usam o recurso de carregamento de arquivo.

Ao gravar um módulo, você pode se conectar ao Hub de IoT Edge e usar primitivos do Hub IoT como faria ao usar o Hub IoT com um aplicativo de dispositivo. A única diferença entre os módulos IoT Edge e os aplicativos de dispositivo IoT é que você precisa se referir à identidade do módulo em vez da identidade do dispositivo.

#### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem

Um módulo IoT Edge pode enviar mensagens para a nuvem por meio do hub de IoT Edge que atua como um agente local e propaga mensagens para a nuvem. Para habilitar o processamento complexo de mensagens do dispositivo para a nuvem, um módulo IoT Edge também pode interceptar e processar mensagens enviadas por outros módulos ou dispositivos para seu hub de IoT Edge local e enviar novas mensagens com dados processados. Cadeias de módulos de IoT Edge podem, portanto, ser criadas para criar pipelines de processamento local.

Para enviar mensagens de telemetria do dispositivo para a nuvem usando o roteamento, use o ModuleClient do SDK do Azure IoT. Com o SDK do Azure IoT, cada módulo tem o conceito de pontos de extremidade de *entrada* e *saída* de módulo, que são mapeados para tópicos especiais de MQTT. Use o `ModuleClient.sendMessageAsync` método e ele enviará mensagens no ponto de extremidade de saída do seu módulo. Em seguida, configure uma rota no edgeHub para enviar esse ponto de extremidade de saída para o Hub IoT.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

O envio de mensagens de telemetria do dispositivo para a nuvem com o agente do MQTT é semelhante à publicação de mensagens em tópicos definidos pelo usuário, mas usando o seguinte tópico especial do Hub IoT para seu módulo: `devices/<device_name>/<module_name>/messages/events` . As autorizações devem ser configuradas adequadamente. A ponte MQTT também deve ser configurada para encaminhar as mensagens neste tópico para a nuvem.

::: moniker-end

Para processar mensagens usando o roteamento, primeiro configure uma rota para enviar mensagens provenientes de outro ponto de extremidade (módulo ou dispositivo) para o ponto de extremidade de entrada do seu módulo e, em seguida, ouça as mensagens no ponto de extremidade de entrada do seu módulo. Cada vez que uma nova mensagem é retornada, uma função de retorno de chamada é disparada pelo SDK do Azure IoT. Processe sua mensagem com essa função de retorno de chamada e, opcionalmente, envie novas mensagens na fila de pontos de extremidade do módulo.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

O processamento de mensagens usando o agente MQTT é semelhante à assinatura de mensagens em tópicos definidos pelo usuário, mas usando os tópicos IoT Edge especiais da fila de saída do seu módulo: `devices/<device_name>/<module_name>/messages/events` . As autorizações devem ser configuradas adequadamente. Opcionalmente, você pode enviar novas mensagens nos tópicos de sua escolha.

::: moniker-end

#### <a name="twins"></a>Gêmeos

Gêmeos são um dos primitivos fornecidos pelo Hub IoT. Há documentos JSON que armazenam informações de estado, incluindo metadados, configurações e condições. Cada módulo ou dispositivo tem o seu próprio entrelaçamento.

Para obter um módulo com o SDK do Azure IoT, chame o `ModuleClient.getTwin` método.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para obter um módulo em cima com qualquer cliente MQTT, um pouco mais de trabalho está envolvido, já que a obtenção de um número de bits não é um padrão MQTT típico. O módulo deve primeiro assinar o tópico especial do Hub IoT `$iothub/twin/res/#` . Este nome de tópico é herdado do Hub IoT e todos os dispositivos/módulos precisam assinar o mesmo tópico. Isso não significa que os dispositivos recebam as duas. O Hub IoT e o edgeHub sabem qual é o que deve ser entregue, mesmo que todos os dispositivos escutem o mesmo nome de tópico. Depois que a assinatura for feita, o módulo precisará solicitar o tipo de email publicando uma mensagem no seguinte tópico especial do Hub IoT com uma ID de solicitação `$iothub/twin/GET/?$rid=1234` . Essa ID de solicitação é uma ID arbitrária (ou seja, um GUID), que será enviada de volta pelo Hub IoT junto com os dados solicitados. É assim que um cliente pode emparelhar suas solicitações com as respostas. O código de resultado é um código de status HTTP, em que o êxito é codificado como 200.

::: moniker-end

Para receber um patch do módulo "r" com o SDK do IoT do Azure, implemente uma função de retorno de chamada e registre-a com o `ModuleClient.moduleTwinCallback` método do SDK do Azure IOT para que sua função de retorno de chamada seja disparada sempre que um patch de "atualização" for fornecido.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para receber um patch do módulo "r" com qualquer cliente MQTT, o processo é muito semelhante ao recebimento de gêmeos completo: um cliente precisa assinar o tópico do Hub IoT especial `$iothub/twin/PATCH/properties/desired/#` . Depois que a assinatura é feita, quando o Hub IoT envia uma alteração da seção desejada do conjunto de entrelaçar, o cliente a recebe.

::: moniker-end

#### <a name="receive-direct-methods"></a>Receber métodos diretos

Para receber um método direto com o SDK do IoT do Azure, implemente uma função de retorno de chamada e registre-a com o `ModuleClient.methodCallback` método do SDK do Azure IOT para que sua função de retorno de chamada seja disparada cada vez que um método direto chegar.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para receber um método direto com qualquer cliente MQTT, o processo é muito semelhante ao recebimento de patches de entrelaçamento. O cliente precisa confirmar que recebeu a chamada e pode enviar de volta algumas informações ao mesmo tempo. O tópico do Hub IoT especial para assinar é `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Suporte à linguagem e à arquitetura

O IoT Edge dá suporte a vários sistemas operacionais, arquiteturas de dispositivo e linguagens de desenvolvimento para que você possa criar o cenário que atenda às suas necessidades. Use esta seção para entender as opções para o desenvolvimento de módulos de IoT Edge personalizados. Você pode saber mais sobre o suporte a ferramentas e requisitos para cada idioma em [preparar seu ambiente de desenvolvimento e teste para IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

Para todos os idiomas na tabela a seguir, IoT Edge dá suporte ao desenvolvimento de dispositivos AMD64 e ARM32 Linux.

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>O desenvolvimento e a depuração de suporte para dispositivos ARM64 Linux estão em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, confira [Desenvolver e depurar módulos do IoT Edge ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Para todos os idiomas na tabela a seguir, IoT Edge dá suporte ao desenvolvimento de dispositivos AMD64 Windows.

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (sem recursos de depuração)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Próximas etapas

[Prepare o ambiente de desenvolvimento e teste para o IoT Edge](development-environment.md)

[Use o Visual Studio para desenvolver módulos C# para IoT Edge](how-to-visual-studio-develop-module.md)

[Use Visual Studio Code para desenvolver módulos para IoT Edge](how-to-vs-code-develop-module.md)

[Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md)
