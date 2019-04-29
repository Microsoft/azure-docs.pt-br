---
title: Provisionar dispositivos Windows para monitoramento remoto em C – Azure | Microsoft Docs
description: Descreve como conectar um dispositivo ao acelerador de solução de Monitoramento Remoto usando um aplicativo escrito em C em execução no Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450204"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Conectar seu dispositivo ao acelerador da solução de monitoramento remoto (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como conectar um dispositivo real ao acelerador de solução de Monitoramento Remoto.

Assim como acontece com a maioria dos aplicativos inseridos que são executados em dispositivos restritos, o código do cliente do aplicativo do dispositivo é escrito em C. Neste tutorial, você cria o aplicativo cliente do dispositivo em um computador com Windows.

Se você preferir simular um dispositivo, consulte [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, siga as etapas em [configurar o ambiente de desenvolvimento Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) para adicionar as bibliotecas e ferramentas de desenvolvimento necessárias para seu computador Windows.

## <a name="view-the-code"></a>Exibir o código

O [código de exemplo](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) usado neste guia está disponível no repositório do GitHub SDKs C do Azure IoT.

### <a name="download-the-source-code-and-prepare-the-project"></a>Baixar o código-fonte e preparar o projeto

Para preparar o projeto, [clone o repositório de SDKs C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) do GitHub.

O exemplo está localizado na pasta **samples/solutions/remote_monitoring_client**.

Abra o arquivo **remote_monitoring.c** na pasta **samples/solutions/remote_monitoring_client** em um editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Edite o arquivo **remote_monitoring** para substituir `<connectionstring>` pela cadeia de conexão do dispositivo que você anotou no início deste guia de instruções, quando adicionou um dispositivo do acelerador de solução.

1. Siga as etapas em [Criar o SDK C no Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) para compilar o SDK e o aplicativo cliente de monitoramento remoto.

1. No prompt de comando usado para criar a solução, execute:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    O console exibe mensagens, como:

    - O aplicativo envia a telemetria de amostra para o acelerador de solução.
    - Responde a métodos invocados do painel de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
