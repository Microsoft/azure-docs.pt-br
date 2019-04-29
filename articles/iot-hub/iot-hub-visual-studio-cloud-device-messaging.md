---
title: Gerenciar mensagens de dispositivo de nuvem do Hub IoT do Azure com o Cloud Explorer para Visual Studio | Microsoft Docs
description: Saiba como usar o Cloud Explorer para Visual Studio para monitorar o dispositivo para mensagens de nuvem e enviar mensagens da nuvem para o dispositivo no Hub IoT do Azure.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 01/07/2018
ms.date: 05/06/2019
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440572"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usar o Cloud Explorer para Visual Studio para enviar e receber mensagens entre seu dispositivo e o Hub IoT

![Diagrama de ponta a ponta](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

O [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) para é uma extensão útil para Visual Studio que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações chave do desenvolvedor de dentro do Visual Studio. Este artigo se concentra em como usar o Cloud Explorer para enviar e receber mensagens entre o dispositivo e o Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>O que você aprenderá

Você aprenderá a usar o Cloud Explorer para Visual Studio para monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo. Mensagens do dispositivo para a nuvem podem ser dados de sensor que o dispositivo coleta e envia para o Hub IoT. Mensagens da nuvem para dispositivo podem ser comandos que o Hub IoT envia para seu dispositivo. Por exemplo, pisca um LED conectado ao seu dispositivo.

## <a name="what-you-will-do"></a>O que você fará

- Use o Cloud Explorer para Visual Studio para monitorar mensagens do dispositivo para a nuvem.
- Use o Cloud Explorer para Visual Studio para enviar mensagens da nuvem para dispositivo.

## <a name="what-you-need"></a>O que você precisa

- Uma assinatura ativa do Azure.
- Um Hub IoT do Azure em sua assinatura.
- Microsoft Visual Studio 2017 Atualização 8 ou posterior
- Componente de Cloud Explorer do Instalador do Visual Studio (selecionado por padrão com a Carga de Trabalho do Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Atualize o Cloud Explorer para a versão mais recente

O componente Cloud Explorer do Instalador do Visual Studio é compatível apenas com monitoramento de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo. Para enviar mensagens para o dispositivo ou para a nuvem, você pode baixar e instalar a versão mais recente do [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

1. Na janela **Cloud Explorer** do Visual Studio, clique no ícone de Gerenciamento de Conta. Você pode abrir a janela do Cloud Explorer usando o menu **Exibir** > **Cloud Explorer**.

    ![Clique em Gerenciamento de Conta](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Clique em **Gerenciar Contas** no Cloud Explorer.
1. Clique em **Adicionar uma conta…** na nova janela para entrar no Azure pela primeira vez.
1. Depois de entrar, sua lista de assinaturas do Azure será mostrada. Selecione as assinaturas do Azure que você deseja exibir e clique em **Aplicar**.
1. Expanda **Sua assinatura** > **Hubs IoT** > **Seu Hub IoT**; a lista de dispositivos será mostrada sob o nó do seu Hub IoT.

    ![Lista de dispositivos](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorar mensagens do dispositivo para a nuvem

Para monitorar as mensagens enviadas do seu dispositivo ao seu Hub IoT, siga estas etapas:

1. Clique com o botão direito do mouse no Hub IoT ou no dispositivo e selecione **Iniciar Monitoramento de Mensagem de D2C**.

    ![Iniciar o monitoramento de mensagem D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

1. As mensagens monitoradas aparecerão no painel de saída do **Hub IoT**.

    ![Monitorando o resultado da mensagem D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

1. Para deixar de monitorar, clique com o botão direito do mouse em qualquer dispositivo ou o Hub IoT e selecione **Parar de monitorar mensagem de D2C**.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

Para enviar uma mensagem do Hub IoT para o dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no seu dispositivo e selecione **Enviar Mensagem de C2D**.

    ![Enviar mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

1. Insira a mensagem na caixa de entrada.
1. Os resultados serão mostrados no painel de saída do **Hub IoT**.

    ![Enviar resultado de uma mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]