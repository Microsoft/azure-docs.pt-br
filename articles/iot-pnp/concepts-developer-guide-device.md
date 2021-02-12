---
title: Guia do desenvolvedor do dispositivo (C)-IoT Plug and Play | Microsoft Docs
description: Descrição do Plug and Play IoT para desenvolvedores de dispositivos C
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 339ae9a0a799a0a03f1fcf37dadf2e1ffa724243
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028675"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Guia do desenvolvedor do dispositivo IoT Plug and Play

O Plug and Play IoT permite criar dispositivos inteligentes que anunciam seus recursos para os aplicativos IoT do Azure. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta aos aplicativos habilitados para IoT Plug and Play.

Um dispositivo inteligente pode ser implementado diretamente, usar [módulos](../iot-hub/iot-hub-devguide-module-twins.md)ou usar [módulos IOT Edge](../iot-edge/about-iot-edge.md).

Este guia descreve as etapas básicas necessárias para criar um dispositivo, módulo ou IoT Edge módulo que segue as [convenções de plug and Play de IOT](../iot-pnp/concepts-convention.md).

Para criar um dispositivo Plug and Play de IoT, módulo ou módulo de IoT Edge, siga estas etapas:

1. Verifique se o dispositivo está usando o protocolo MQTT ou MQTT sobre Websockets para se conectar ao Hub IoT do Azure.
1. Crie um modelo de [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) para descrever seu dispositivo. Para saber mais, confira [entender os componentes em modelos de plug and Play IOT](concepts-components.md).
1. Atualize seu dispositivo ou módulo para anunciar o `model-id` como parte da conexão do dispositivo.
1. Implementar telemetria, propriedades e comandos usando as [convenções de plug and Play IOT](concepts-convention.md)

Depois que a implementação do dispositivo ou do módulo estiver pronta, use o [Azure IOT Explorer](howto-use-iot-explorer.md) para validar que o dispositivo segue as convenções de plug and Play de IOT.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre o desenvolvimento de dispositivos IoT Plug and Play, aqui estão alguns recursos adicionais:

- [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK do Dispositivo C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](/rest/api/iothub/device)
- [Componentes do modelo](concepts-components.md)
- [Instalar e usar as ferramentas de criação do DTDL](howto-use-dtdl-authoring-tools.md)
- [Guia do desenvolvedor do serviço de Plug and Play de IoT](concepts-developer-guide-service.md)