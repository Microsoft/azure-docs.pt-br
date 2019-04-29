---
title: Provisionar o Raspberry Pi para monitoramento remoto usando C – Azure | Microsoft Docs
description: Descreve como conectar um dispositivo Raspberry Pi ao acelerador de solução de Monitoramento Remoto usando um aplicativo escrito em C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61454484"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Conectar seu dispositivo Raspberry Pi ao acelerador da solução de monitoramento remoto (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como conectar um dispositivo físico ao acelerador de solução de monitoramento remoto. Assim como acontece com a maioria dos aplicativos inseridos que são executados em dispositivos restritos, o código do cliente do aplicativo do dispositivo Raspberry Pi é escrito em C. Neste tutorial, você cria o aplicativo em um Raspberry Pi executando o sistema operacional Raspbian.

Se você preferir simular um dispositivo, consulte [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Requisitos de hardware

Um computador desktop para que você possa se conectar remotamente à linha de comando no Raspberry Pi.

[Kit de início de IoT da Microsoft para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial usa os seguintes itens do kit:

- Raspberry Pi 3
- Cartão MicroSD (com NOOBS)
- Um cabo USB Mini
- Um cabo Ethernet

### <a name="required-desktop-software"></a>Software de área de trabalho necessário

É necessário um cliente SSH em seu computador desktop para que você possa acessar remotamente a linha de comando no Raspberry Pi.

- O Windows não inclui um cliente SSH. Recomendamos o uso de [PuTTY](https://www.putty.org/).
- A maioria das distribuições do Linux e Mac OS incluem o utilitário de linha de comando do SSH. Para obter mais informações, consulte [SSH usando o Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software do Raspberry Pi necessário

Este artigo pressupõe que você tenha instalado a versão mais recente do [Raspbian SO em seu Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

As etapas a seguir mostram como preparar seu Raspberry Pi para criar um aplicativo C que se conecta ao acelerador de solução:

1. Conecte-se ao seu Raspberry Pi usando **ssh**. Para obter mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site do Raspberry Pi](https://www.raspberrypi.org/).

1. Use o seguinte comando para atualizar o Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Para concluir as etapas neste guia de instruções, siga as etapas em [configurar o ambiente de desenvolvimento Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para adicionar as bibliotecas e ferramentas de desenvolvimento necessárias para o Raspberry Pi.

## <a name="view-the-code"></a>Exibir o código

O [código de exemplo](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) usado neste guia está disponível no repositório do GitHub SDKs C do Azure IoT.

### <a name="download-the-source-code-and-prepare-the-project"></a>Baixar o código-fonte e preparar o projeto

Para preparar o projeto, clone ou baixe o [repositório de SDKs C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) do GitHub.

O exemplo está localizado na pasta **samples/solutions/remote_monitoring_client**.

Abra o arquivo **remote_monitoring.c** na pasta **samples/solutions/remote_monitoring_client** em um editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

As etapas a seguir descrevem como usar *CMake* para compilar o aplicativo cliente. O aplicativo cliente de monitoramento remoto foi criado como parte do processo de build para o SDK.

1. Edite o arquivo **remote_monitoring** para substituir `<connectionstring>` pela cadeia de conexão do dispositivo que você anotou no início deste guia de instruções, quando adicionou um dispositivo do acelerador de solução.

1. Navegue até a raiz da sua cópia clonada do [repositório de SDKs C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) e execute os seguintes comandos para compilar o aplicativo cliente:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Execute o aplicativo cliente e envie telemetria ao Hub IoT:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    O console exibe mensagens, como:

    - O aplicativo envia a telemetria de amostra para o acelerador de solução.
    - Responde a métodos invocados do painel de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
