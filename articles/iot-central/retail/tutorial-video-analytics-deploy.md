---
title: 'Tutorial: Como implantar o modelo de aplicativo do Azure IoT Central de detecção de objetos e movimentos e análise de vídeo'
description: Tutorial – este guia resume as etapas para implantar um aplicativo do Azure IoT Central usando o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727457"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Tutorial: Como implantar um aplicativo do IoT Central usando o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo

Para obter uma visão geral dos principais componentes do aplicativo de *detecção de objetos e movimentos e análise de vídeo*, confira a [arquitetura do aplicativo de detecção de objetos e movimentos e análise de vídeo](architecture-video-analytics.md).

O seguinte vídeo fornece uma explicação sobre como usar o _modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo_ para implantar uma solução do IoT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

Neste conjunto de tutoriais, você aprenderá como:

> [!div class="checklist"]
> * Implantar o aplicativo
> * Implantar uma instância do IoT Edge que se conecte ao aplicativo
> * Monitorar e gerenciar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure é recomendada. Como alternativa, você pode usar uma avaliação gratuita de sete dias. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).

## <a name="deploy-the-application"></a>Implantar o aplicativo

Conclua as seguintes etapas para implantar um aplicativo do IoT Central usando o modelo de aplicativo de análise de vídeo:

1. Conclua o tutorial [Criar um aplicativo de análise de vídeo no Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou o [Criar uma análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) para:
    - Criar uma conta de Serviços de Mídia do Azure.
    - Criar o aplicativo do IoT Central com o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo.
    - Configurar um dispositivo de gateway no aplicativo do IoT Central. O gateway permite que os dispositivos de câmera se conectem ao aplicativo.

1. Conclua o tutorial [Criar uma instância do IoT Edge para análise de vídeo (VM do Linux)](tutorial-video-analytics-iot-edge-vm.md) ou o [Tutorial: Criar uma instância do IoT Edge para análise de vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) para:
    - Crie uma VM do Azure com o runtime do Azure IoT Edge instalado. Prepare a instalação do IoT Edge para hospedar o módulo de análise de vídeo.
    - Conecte o dispositivo do IoT Edge ao aplicativo do IoT Central.

1. Conclua o tutorial [Monitorar e gerenciar um aplicativo de análise de vídeo](tutorial-video-analytics-manage.md) para:
    - Adicionar câmeras de detecção de objetos e movimentos ao gateway no aplicativo do IoT Central.
    - Iniciar o processamento da câmera.
    - Instalar um player de mídia local para exibir o vídeo capturado no AMS.
    - Exibir o vídeo capturado que mostra objetos detectados.
    - Organizar.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você tiver concluído o aplicativo, remova todos os recursos criados da seguinte maneira:

1. No aplicativo do IoT Central, procure a página **Seu aplicativo** na seção **Administração**. Em seguida, selecione **Excluir**.
1. No portal do Azure, exclua o grupo de recursos **lva-rg**.
1. No computador local, pare o contêiner **amp-viewer** do Docker.

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral das etapas para implantar e usar o modelo de aplicativo de análise de vídeo, consulte

> [!div class="nextstepaction"]
> [Criar um aplicativo de análise de vídeo no Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou

> [!div class="nextstepaction"]
> [Criar uma análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) para começar.