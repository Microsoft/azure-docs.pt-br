---
title: Usar as ferramentas do Azure IoT para VSCode para gerente mensagens do hub de ti
description: Saiba como usar as Ferramentas do Azure IoT para Visual Studio Code para monitorar mensagens de dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo no Hub IoT do Azure.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: b9d8f0c2a573ba1749b414a389cd7181ddd5f95a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321676"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Use as Ferramentas do Azure IoT para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o Hub IoT

![Diagrama de ponta a ponta](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

As [Ferramentas IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) são uma extensão útil do Visual Studio Code que facilitam o gerenciamento do Hub IoT e o desenvolvimento de aplicativos para IoT. Este artigo se concentra em como usar as Ferramentas do Azure IoT para Visual Studio Code para enviar e receber mensagens entre o dispositivo e o Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>O que você aprenderá

Você aprende a usar as Ferramentas do Azure IoT para Visual Studio Code para monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo. Mensagens do dispositivo para a nuvem podem ser dados de sensor que o dispositivo coleta e envia para o Hub IoT. Mensagens da nuvem para o dispositivo podem ser comandos que o Hub IoT envia para o dispositivo para que um LED conectado a ele pisque.

## <a name="what-you-will-do"></a>O que você fará

* Use as Ferramentas do Azure IoT para Visual Studio Code para monitorar mensagens do dispositivo para a nuvem.

* Use as Ferramentas do Azure IoT para Visual Studio Code para enviar mensagens da nuvem para dispositivo.

## <a name="what-you-need"></a>O que você precisa

* Uma assinatura ativa do Azure.

* Um hub IoT do Azure em sua assinatura.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Ferramentas do Azure IOT para vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou copiar e colar esta URL em uma janela do navegador: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

1. Na exibição do **Explorer** do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure** no canto inferior esquerdo.

2. Clique no menu de contexto **Selecionar Hub IoT**.

3. Um pop-up aparecerá no canto inferior direito para permitir que você entre no Azure pela primeira vez.

4. Depois de entrar, sua lista de Assinatura do Azure será mostrada, então selecione a Assinatura do Azure e o Hub IoT.

5. A lista de dispositivos será mostrada na guia **Dispositivos do Hub IoT do Azure** em poucos segundos.

   > [!Note]
   > Você também pode concluir a configuração escolhendo **Definir cadeia de conexão do Hub IoT**. Insira a cadeia de conexão da política **iothubowner** para o Hub IOT ao qual seu dispositivo IOT se conecta na janela pop-up.

## <a name="monitor-device-to-cloud-messages"></a>Monitorar mensagens do dispositivo para a nuvem

Para monitorar as mensagens enviadas do seu dispositivo ao seu Hub IoT, siga estas etapas:

1. Clique com o botão direito do mouse no dispositivo e selecione **Iniciar Monitoramento do ponto de extremidade do evento interno**.

2. As mensagens monitoradas serão mostradas na exibição de **saída**do  >  **Hub IOT do Azure** .

3. Para interromper o monitoramento, clique com o botão direito do mouse na exibição de **saída** e selecione **parar monitoramento de ponto de extremidade de evento interno**.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

Para enviar uma mensagem do Hub IoT para o dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no seu dispositivo e selecione **Enviar Mensagem de C2D para Dispositivo**.

2. Insira a mensagem na caixa de entrada.

3. Os resultados serão mostrados na exibição de **saída**  >  **do Hub IOT do Azure** .

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]