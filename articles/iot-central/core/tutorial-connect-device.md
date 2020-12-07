---
title: Tutorial – Conectar um aplicativo cliente genérico ao Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, desenvolvedor de dispositivos, como conectar um dispositivo que executa um aplicativo cliente C, C#, Java, JavaScript ou Python ao seu aplicativo do Azure IoT Central. Modifique o modelo de dispositivo gerado automaticamente adicionando exibições que permitem que um operador interaja com um dispositivo conectado.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 9eeb8174c900b0f548144231e65643c9559f75e3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126052"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Tutorial: Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Este tutorial mostra a você, desenvolvedor de dispositivos, como conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central. O aplicativo simula o comportamento de um dispositivo de termostato. Quando o aplicativo se conecta ao IoT Central, ele envia a ID do modelo de dispositivo de termostato. O IoT Central usa a ID do modelo para recuperar o modelo de dispositivo e criar um modelo de dispositivo para você. Adicione personalizações e exibições ao modelo de dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e executar o código do dispositivo e ver se ele se conecta ao seu aplicativo do IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Adicionar exibições personalizadas a um modelo de dispositivo.
> * Publicar o modelo de dispositivo.
> * Usar uma exibição para gerenciar as propriedades do dispositivo.
> * Chamar um comando para controlar o dispositivo.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Exibir dados brutos

Como desenvolvedor de dispositivos, você pode usar a exibição de **Dados brutos** para ver os dados brutos que seu dispositivo está enviando para o IoT Central:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="A exibição de dados brutos":::

Nessa exibição, você pode selecionar as colunas a serem exibidas e definir um intervalo de tempo para exibição. A coluna **Dados não modelados** mostra dados do dispositivo que não correspondem a nenhuma propriedade ou definição de telemetria no modelo de dispositivo.

## <a name="next-steps"></a>Próximas etapas

Se você preferir continuar com o conjunto de tutoriais do IoT Central e saber mais sobre como criar uma solução de IoT Central, confira:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo de gateway](./tutorial-define-gateway-device-type.md)

Como desenvolvedor de dispositivos, agora que você aprendeu as noções básicas de como criar um dispositivo usando Java, algumas próximas etapas sugeridas são:

* Leia [O que são modelos de dispositivo?](./concepts-device-templates.md) para saber mais sobre a função dos modelos de dispositivo quando você implementa o código do dispositivo.
* Leia [Conectar-se ao Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registrar dispositivos com o IoT Central e como o IoT Central protege as conexões do dispositivo.
* Leia [Conteúdos de comando, telemetria e propriedade](concepts-telemetry-properties-commands.md) para saber mais sobre os dados trocados entre o dispositivo e o IoT Central.
