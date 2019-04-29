---
title: Desenvolver módulos para o Azure IoT Edge | Microsoft Docs
description: Desenvolver módulos personalizados para o Azure IoT Edge que podem se comunicar com o tempo de execução e o IoT Hub
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 02/25/2019
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ea2d1dbcf991ce858f07905bc1bb41809f9bcdd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322389"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolva seus próprios módulos do IoT Edge

Os módulos do Azure IoT Edge podem conectar outros serviços do Azure e contribuir para o pipeline de dados em nuvem maior. Este artigo descreve como é possível desenvolver módulos para comunicação com o tempo de execução do IoT Edge e Hub IoT e, portanto, com o restante da nuvem do Azure. 

## <a name="iot-edge-runtime-environment"></a>Ambiente de tempo de execução do IoT Edge
O tempo de execução do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implantá-los nos dispositivos IoT Edge. Em um nível alto, qualquer programa pode ser empacotado como um módulo do IoT Edge. No entanto, para aproveitar ao máximo as funcionalidades de gerenciamento e comunicação do IoT Edge, um programa em execução em um módulo pode se conectar ao hub do IoT Edge local, integrado no tempo de execução do IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Usar o hub do IoT Edge
O hub do IoT Edge fornece duas funcionalidades principais: proxy para o Hub IoT e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos do Hub IoT
O Hub IoT vê uma instância de módulo de forma análoga a um dispositivo, no sentido de que:

* ele tem um módulo gêmeo que é distinto e isolado do [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md) e dos outros módulos gêmeos desse dispositivo;
* ele pode enviar [mensagens de dispositivo para nuvem](../iot-hub/iot-hub-devguide-messaging.md);
* ele pode receber [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) direcionados especificamente para sua identidade.

No momento, um módulo não pode receber mensagens de nuvem para dispositivo nem usar o recurso de carregamento de arquivo.

Ao gravar um módulo, você pode usar o [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) para se conectar ao hub IoT Edge e usar a funcionalidade acima como faria ao usar o IoT Hub com um aplicativo de dispositivo, a única diferença sendo que, de seu back-end de aplicativo, você deve se referir à identidade do módulo em vez da identidade do dispositivo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem
Para habilitar o processamento complexo de mensagens de dispositivo para nuvem, o Hub do IoT Edge fornece roteamento declarativo de mensagens entre módulos e entre módulos e Hub IoT. O roteamento declarativo permite que os módulos interceptem e processem mensagens enviadas por outros módulos e os propaguem em pipelines complexos. Para obter mais informações, consulte [implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

Um módulo IoT Edge, em oposição a um aplicativo de dispositivo Hub IoT normal, pode receber mensagens de dispositivo para nuvem que estão sendo intermediadas por proxy por seu hub IoT Edge local para processá-las.

O Hub do IoT Edge propaga as mensagens para o módulo com base nas rotas declarativas descritas no [manifesto de implantação](module-composition.md). Ao desenvolver um módulo IoT Edge, você pode receber essas mensagens definindo manipuladores de mensagens.

Para simplificar a criação de rotas, o IoT Edge inclui o conceito de pontos de extremidade de *entrada* e *saída*. Um módulo pode receber todas as mensagens de dispositivo para nuvem roteadas a ele sem especificar nenhuma entrada e pode enviar mensagens de dispositivo para nuvem sem especificar nenhuma saída. Usar entradas e saídas explícitas, porém, torna as regras de roteamento mais simples de entender. 

Por fim, as mensagens de dispositivo para nuvem tratadas pelo hub do Edge são marcadas com as seguintes propriedades de sistema:

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| $connectionDeviceId | A ID do dispositivo do cliente que enviou a mensagem |
| $connectionDeviceId | A ID do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode ser um vazia. |
| $outputName | A saída usada para enviar a mensagem. Pode ser um vazia. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Conectar-se ao hub do IoT Edge a partir de um módulo
Conectar-se ao hub IoT Edge local a partir de um módulo envolve duas etapas: 
1. Crie uma instância ModuleClient no aplicativo.
2. Verifique se que seu aplicativo aceita o certificado apresentado pelo hub do IoT Edge nesse dispositivo.

Crie uma instância ModuleClient para conectar o módulo ao Hub do IoT Edge em execução no dispositivo, de modo semelhante como as instâncias DeviceClient conectam dispositivos ao Hub IoT. Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para seu idioma preferencial do SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C and Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>Próximas etapas

[Preparar o ambiente de desenvolvimento e teste para o IoT Edge](development-environment.md)

[Usar o Visual Studio 2017 para desenvolver C# módulos do IoT Edge](how-to-visual-studio-develop-csharp-module.md)

[Usar o Visual Studio Code para desenvolver módulos do IoT Edge](how-to-vs-code-develop-module.md)

