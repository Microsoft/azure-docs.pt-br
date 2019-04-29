---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 40a5416f15b0e2d66d6ce4b4787573560ee4af00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346078"
---
## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, você usa a CLI do Azure para criar a identidade do dispositivo para este tutorial. A CLI do Azure é pré-instalada no [Azure Cloud Shell](~/articles/cloud-shell/overview.md), mas você também pode [instalá-la localmente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Execute o comando a seguir no ambiente de linha de comando em que você está usando a CLI do Azure para instalar a extensão de IoT:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Se você estiver executando a CLI do Azure localmente, use o seguinte comando para entrar sua conta do Azure (se estiver usando o Cloud Shell, você será conectado automaticamente e não precisará executar esse comando):

    ```cmd/sh
    az login
    ```

1. Por fim, crie uma nova identidade do dispositivo chamada `myDeviceId` e recupere a cadeia de conexão do dispositivo com estes comandos:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anote a cadeia de conexão do dispositivo no resultado. Essa cadeia de conexão é usada pelo aplicativo do dispositivo para se conectar ao seu Hub IoT como um dispositivo.

<!-- images and links -->
