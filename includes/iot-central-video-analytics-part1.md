---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763377"
---
O aplicativo de exemplo inclui dois dispositivos simulados e um gateway do IoT Edge. Os seguintes tutoriais mostram duas abordagens para experimentar e entender as funcionalidades do gateway:

* Crie o gateway do IoT Edge em uma VM do Azure e conecte uma câmera simulada.
* Crie o gateway do IoT Edge em um dispositivo real, como um Intel NUC, e conecte uma câmera real.

Neste tutorial, você aprende a:
> [!div class="checklist"]
> * Usar o modelo de aplicativo de análise de vídeo do Azure IoT Central para criar um aplicativo de loja de varejo
> * Personalizar as configurações de aplicativo
> * Criar um modelo de dispositivo para um dispositivo de gateway do IoT Edge
> * Adicionar um dispositivo de gateway ao seu aplicativo do IoT Central

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).
* Se você estiver usando uma câmera real, precisará obter conectividade entre o dispositivo do IoT Edge e a câmera e ter acesso ao canal **Real Time Streaming Protocol**.

## <a name="initial-setup"></a>Instalação inicial

Nestes tutoriais, você atualizará e usará vários arquivos de configuração. As versões iniciais desses arquivos estão disponíveis no repositório GitHub do [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). O repositório inclui um arquivo de texto de [bloco de anotações](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) para você baixar e usar para registrar os valores das configurações dos serviços implantados. Esse arquivo ajuda a concluir etapas posteriores nos tutoriais.

Crie uma pasta chamada *lva-configuration* no computador local para salvar cópias desses arquivos. Em seguida, clique com o botão direito do mouse em cada um dos seguintes links e escolha **Salvar como** para salvar o arquivo na pasta *lva-configuration*:
