---
title: Análise Dinâmica de Vídeo usando o próprio modelo HTTP – Azure
description: Neste guia de início rápido, você aplicará a Pesquisa Visual Computacional para analisar o feed de vídeo ao vivo de uma câmera IP (simulada) usando seu modelo HTTP.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: d3ba937abcc7bbfd9bb2afe7b15aec28ebb57446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508467"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Início Rápido: Análise Dinâmica de Vídeo usando o próprio modelo HTTP

Este início rápido mostra como usar a Análise Dinâmica de Vídeo no IoT Edge para analisar um feed de vídeo ao vivo de uma câmera IP (simulada). Você verá como aplicar um modelo pesquisa visual computacional para detectar objetos. Um subconjunto dos quadros no feed de vídeo ao vivo é enviado para um serviço de inferência. Os resultados são enviados ao Hub do IoT Edge. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Visão geral

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Criar e implantar o grafo de mídia

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretar os resultados

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros inícios rápidos, mantenha os recursos criados. Caso contrário, vá até o portal do Azure, navegue até seus grupos de recursos, selecione o grupo de recursos no qual você executou este início rápido e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

* Experimente uma [versão protegida do modelo YoloV3](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) e implante-a no dispositivo do IoT Edge. 

Examine os desafios adicionais para usuários avançados:

* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Pesquise pelas câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products/). Procure dispositivos em conformidade com os perfis G, S ou T.
* Use um dispositivo Linux AMD64 ou x64 em vez de usar uma VM Linux do Azure. Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Depois, registre o dispositivo no Hub IoT do Azure seguindo as instruções em [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md).