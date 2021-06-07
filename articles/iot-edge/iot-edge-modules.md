---
title: Saiba como os módulos executam a lógica em seus dispositivos - Azure IoT Edge | Microsoft Docs
description: Módulos do Azure IoT Edge são unidades de lógica em contêineres que podem ser implantadas e gerenciadas remotamente para que você possa executar a lógica comercial em dispositivos IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492363"
---
# <a name="understand-azure-iot-edge-modules"></a>Entenda os módulos do Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge permite implantar e gerenciar a lógica de negócios na borda na forma de *módulos*. Os módulos do Azure IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure (por exemplo, o Azure Stream Analytics) ou seu próprio código específico à solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, considere os quatro elementos conceituais de um módulo:

* Uma **imagem de módulo** é um pacote contendo o software que define um módulo.
* Uma **instância de módulo** é a unidade específica de computação que executa a imagem de módulo em um dispositivo com IoT Edge. A instância de módulo é iniciada pelo runtime do IoT Edge.
* Uma **identidade de módulo** são informações (incluindo as credenciais de segurança) armazenadas no Hub IoT, que estão associadas a cada instância de módulo.
* Um **módulo gêmeo** é um documento JSON armazenado no Hub IoT, e contém informações de estado para uma instância de módulo, incluindo metadados, configurações e condições.

## <a name="module-images-and-instances"></a>Instâncias e imagens de módulo

As imagens de módulo do IoT Edge contêm aplicativos que tiram proveito dos recursos de comunicação, segurança e gerenciamento do runtime do IoT Edge. Você pode desenvolver suas próprias imagens de módulo ou exportar uma de um serviço do Azure com suporte, como o Azure Stream Analytics.
As imagens existem na nuvem e podem ser atualizadas, alteradas e implantadas em diferentes soluções. Por exemplo, um módulo que usa aprendizado de máquina para prever a saída da linha de produção como uma imagem separada, em comparação com um módulo que usa a visão do computador para controlar um drone.

Sempre que uma imagem de módulo é implantada em um dispositivo e iniciada pelo runtime do IoT Edge, uma nova instância desse módulo é criada. Dois dispositivos em diferentes partes do mundo podem usar a mesma imagem de módulo. No entanto, cada dispositivo terá sua própria instância de módulo quando o módulo for iniciado no dispositivo.

![Diagrama: imagens de módulo na nuvem, instâncias de módulo em dispositivos](./media/iot-edge-modules/image_instance.png)

Na implementação, as imagens de módulos existem como imagens de contêiner em um repositório, e as instâncias de módulo são contêineres em dispositivos.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identidades de módulo

Quando uma nova instância de módulo é criada pelo IoT Edge Runtime, ela obtém uma identidade de módulo correspondente. A identidade do módulo é armazenada no Hub IoT e é usada como o escopo de endereçamento e segurança para todas as comunicações locais e na nuvem para essa instância do módulo.

A identidade associada a uma instância de módulo depende da identidade do dispositivo no qual a instância está em execução, e do nome fornecido para esse módulo em sua solução. Por exemplo, se você chamar `insight` um módulo que usa um Azure Stream Analytics e implantá-lo em um dispositivo chamado `Hannover01`, o runtime do IoT Edge criará uma identidade de módulo correspondente chamada `/devices/Hannover01/modules/insight`.

Claramente, em cenários nos quais você precisa implantar uma imagem de módulo várias vezes no mesmo dispositivo, é possível implantar a mesma imagem várias vezes com nomes diferentes.

![Diagrama: as identidades de módulo são exclusivas dentro de dispositivos e em todos os dispositivos](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Módulos gêmeos

Cada instância de módulo também tem um módulo gêmeo correspondente que você pode usar para configurar a instância de módulo. A instância e o gêmeo são associados entre si por meio da identidade de módulo.

O módulo gêmeo é um documento JSON que armazena as propriedades de configuração e as informações do módulo. Esse conceito é comparável ao conceito de [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md) do Hub IoT. A estrutura de um módulo gêmeo é igual a de um dispositivo gêmeo. As APIs usadas para interagir com os dois tipos de gêmeos também são as mesmas. A única diferença entre os dois é a identidade usada para instanciar o SDK do cliente.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Funcionalidades offline

Azure IoT Edge módulos podem operar offline indefinidamente após a sincronização com o Hub IoT pelo menos uma vez. IoT Edge dispositivos também podem estender esse recurso offline para outros dispositivos IoT. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge dispositivos, módulos e dispositivos filho](offline-capabilities.md).

## <a name="next-steps"></a>Próximas etapas

* [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge](module-development.md)
* [Entenda o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md)
