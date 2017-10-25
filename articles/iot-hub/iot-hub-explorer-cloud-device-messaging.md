---
title: Gerenciar sistema de mensagens de dispositivos de nuvem do Hub IoT do Azure com o iothub-explorer | Microsoft Docs
description: Saiba como usar a ferramenta iothub-explorer da CLI para monitorar mensagens do dispositivo para a nuvem (D2C) e enviar mensagens da nuvem para o dispositivo (C2D) no Hub IoT do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: iothub explorer, sistema de mensagens de dispositivo de nuvem, nuvem ao dispositivo do Hub IoT, sistema de mensagens da nuvem para o dispositivo
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 30151b7bdc544bc36e959cc3528d37897198fc7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Use o iothub-explorer para enviar e receber mensagens entre o dispositivo e o Hub IoT

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

O [iothub-explorer](https://github.com/azure/iothub-explorer) tem alguns comandos que facilitam o gerenciamento do Hub IoT. Este tutorial se concentra em como usar o iothub-explorer para enviar e receber mensagens entre o dispositivo e o Hub IoT.

## <a name="what-you-will-learn"></a>O que você aprenderá

Saiba como usar o iothub-explorer para monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo. Mensagens do dispositivo para a nuvem podem ser dados de sensor que o dispositivo coleta e envia para o Hub IoT. Mensagens da nuvem para o dispositivo podem ser comandos que o Hub IoT envia para o dispositivo para que um LED conectado a ele pisque.

## <a name="what-you-will-do"></a>O que você fará

- Use o iothub-explorer para monitorar mensagens do dispositivo para a nuvem.
- Use o iothub-explorer para enviar mensagens da nuvem para o dispositivo.

## <a name="what-you-need"></a>O que você precisa

- Tutorial [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluído que aborda os seguintes requisitos:
  - Uma assinatura ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - O aplicativo cliente que envia mensagens para o Hub IoT do Azure.
- iothub-explorer. ([Instalar o iothub-explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Monitorar mensagens do dispositivo para a nuvem

Para monitorar as mensagens enviadas do seu dispositivo ao seu Hub IoT, siga estas etapas:

1. Abra uma janela do console.
1. Execute o comando a seguir:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Obtenha `<device-id>` e `<IoTHubConnectionString>` do seu Hub IoT. Certifique-se de ter concluído o tutorial anterior. Ou então, você pode tentar usar `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` se você tiver `HostName`, `SharedAccessKeyName` e `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

Para enviar uma mensagem do Hub IoT para o dispositivo, siga estas etapas:

1. Abra uma janela do console.
1. Inicie uma sessão no Hub IoT, executando o seguinte comando:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Envie uma mensagem para o dispositivo, executando o seguinte comando:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

O comando envia a mensagem ao dispositivo e pisca o LED conectado a ele.

> [!Note]
> Não é necessário que o dispositivo envie um comando de confirmação separado para o Hub IoT ao receber a mensagem.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
