---
title: Solucionar problemas com os módulos visão e visão do Azure Percept
description: Obtenha dicas para solucionar problemas para alguns dos problemas mais comuns encontrados nas experiências de protótipo da visão de AI
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661908"
---
# <a name="vision-solution-troubleshooting"></a>Solução de problemas da solução de visão

Consulte as diretrizes a seguir para obter informações sobre como solucionar problemas de soluções de visão sem código no Azure Percept Studio.

## <a name="delete-a-vision-project"></a>Excluir um projeto de visão

1. Ir para https://www.customvision.ai/projects.

1. Passe o mouse sobre o projeto que você deseja excluir e clique no ícone de lixeira para excluir o projeto.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Página projetos no Visão Personalizada com o ícone Excluir realçado.":::

## <a name="check-which-modules-are-on-a-device"></a>Verificar quais módulos estão em um dispositivo

1. Acesse o [portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Clique no ícone do **Hub IOT** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Home Page do portal do Azure com o ícone do Hub IOT realçado." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Selecione o Hub IoT ao qual seu dispositivo de destino está conectado.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Lista de hubs IoT.":::

1. Selecione **IOT Edge** e clique em seu dispositivo na guia **ID do dispositivo** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="Página inicial do IoT Edge.":::

1. Os módulos de dispositivo serão listados na guia **módulos** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Página do IOT Edge para o dispositivo selecionado mostrando o conteúdo da guia módulos." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Excluir um dispositivo

1. Acesse o [portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Clique no ícone do **Hub IOT** .

1. Selecione o Hub IoT ao qual seu dispositivo de destino está conectado.

1. Selecione **IOT Edge** e marque a caixa ao lado de sua ID do dispositivo de destino. Clique no ícone de lixeira para excluir o dispositivo.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Ícone de exclusão realçado na home page do IOT Edge.":::

## <a name="eye-module-troubleshooting-tips"></a>Dicas de solução de problemas do módulo de olho

Se houver um problema com o **WebStreamModule**, verifique se **azureeyemodule**, que faz o modelo de visão inferência, está em execução. Para verificar o status do tempo de execução, vá para a [portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e navegue até **todos os recursos**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Clique na guia **módulos** para ver o status de tempo de execução de todos os módulos instalados.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Tela de status do tempo de execução do módulo do dispositivo." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Se o status de tempo de execução de **azureeyemodule** não estiver listado como **em execução**, clique em **definir módulos**  ->  **azureeyemodule**. Na página **configurações do módulo** , defina **o status desejado** como **em execução** e clique em **Atualizar**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Tela de configuração de configuração de módulo.":::

## <a name="view-device-rtsp-video-stream"></a>Exibir fluxo de vídeo RTSP do dispositivo

Exiba o fluxo de vídeo RTSP do dispositivo no [Azure Percept Studio](./how-to-view-video-stream.md) ou no [VLC Media Player](https://www.videolan.org/vlc/index.html).

Para abrir o fluxo RTSP no VLC Media Player, vá para **mídia**  ->  **abrir fluxo de rede**  ->  **RTSP://[endereço IP do dispositivo]/Result**.

## <a name="next-steps"></a>Próximas etapas

Consulte o [Guia de solução de problemas geral](./troubleshoot-dev-kit.md) para obter mais informações sobre como solucionar problemas do Azure Percept DK.