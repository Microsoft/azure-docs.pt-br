---
title: Guia do desenvolvedor de serviços – IoT Plug and Play | Microsoft Docs
description: Descrição de Plug and Play de IoT para desenvolvedores de serviço
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582724"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guia do desenvolvedor do serviço de Plug and Play de IoT

O Plug and Play IoT permite criar dispositivos inteligentes que anunciam seus recursos para os aplicativos IoT do Azure. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta aos aplicativos habilitados para IoT Plug and Play.

O Plug and Play IoT permite que você use dispositivos que anunciaram sua ID de modelo com o Hub IoT. Por exemplo, você pode acessar as propriedades e os comandos de um dispositivo diretamente.

Para usar um dispositivo IoT Plug and Play conectado ao seu hub IoT, um dos SDKs do serviço IoT:

## <a name="service-sdks"></a>SDKs do Serviço

Use os SDKs do serviço IoT do Azure em sua solução para interagir com dispositivos e módulos. Por exemplo, você pode usar os SDKs de serviço para ler e atualizar as propriedades de entrelaçamento e invocar comandos. Os idiomas com suporte incluem C#, Java, Node.js e Python.

Os SDKs de serviço permitem que você acesse informações de dispositivo de uma solução, como um desktop ou aplicativo Web. Os SDKs de serviço incluem dois namespaces e modelos de objeto que você pode usar para recuperar a ID do modelo:

- Cliente de serviço do Hub IOT. Esse serviço expõe a ID do modelo como uma propriedade de dispositivo.

- Cliente digital gêmeos. A nova API digital gêmeos opera em construções de modelo [DTDL (digital gêmeos Definition Language)](concepts-digital-twin.md) , como componentes, propriedades e comandos. As APIs de teledigital fotodigitais facilitam para os criadores de solução criar soluções de Plug and Play de IoT.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a modelagem de dispositivo, aqui estão alguns recursos adicionais:

- [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK do dispositivo C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](/rest/api/iothub/device)
- [Guia de modelagem de Plug and Play de IoT](concepts-modeling-guide.md)
- [Instalar e usar as ferramentas de criação do DTDL](howto-use-dtdl-authoring-tools.md)