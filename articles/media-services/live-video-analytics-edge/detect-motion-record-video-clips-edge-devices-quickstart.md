---
title: Detectar movimento e gravar vídeo em dispositivos de borda – Azure
description: Este início rápido mostra como usar a Análise de Vídeo ao vivo no IoT Edge para analisar o feed de vídeo ao vivo de uma câmera de IP (simulada), para detectar se qualquer movimento está presente; em caso afirmativo, registre um clipe de vídeo MP4 no sistema de arquivos local no dispositivo de borda.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e4334cdd14242337a2a870f31886606020654685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498346"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Início Rápido: Detectar movimento e gravar vídeo em dispositivos de borda
 
Este início rápido mostra como usar a Análise Dinâmica de Vídeo no IoT Edge para analisar o feed de vídeo ao vivo de uma câmera IP (simulada). Ele mostra como detectar se há movimento e, caso houver, gravar um clipe de vídeo MP4 no sistema de arquivos local no dispositivo de borda. Este início rápido usa uma VM do Azure como dispositivo do IoT Edge e um fluxo de vídeo ao vivo simulado. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Visão geral

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>Examinar e editar os arquivos de exemplo

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Revisão – verificar o status dos módulos

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Revisão – preparar-se para eventos de monitoramento

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Executar o programa de exemplo

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretar os resultados 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>Reproduzir o clipe MP4

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros inícios rápidos, mantenha os recursos criados. Caso contrário, no portal do Azure, navegue até seus grupos de recursos, selecione o grupo de recursos no qual você executou este início rápido e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

* Siga o guia de início rápido [Executar a Análise de Vídeo ao vivo com seu próprio modelo](use-your-model-quickstart.md), para aplicar a IA aos feeds de vídeo ao vivo.
* Examine os desafios adicionais para usuários avançados:

    * Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera) compatível com RTSP em vez de usar o simulador RTSP. Veja as câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products). Procure dispositivos em conformidade com os perfis G, S ou T.
    * Use um dispositivo AMD64 ou x64 do Linux em vez de usar uma VM do Linux no Azure. Esse dispositivo precisa estar na mesma rede que a câmera IP. Siga as instruções em [Instalar o runtime do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Depois, siga as instruções em [Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux](../../iot-edge/quickstart-linux.md) para registrar o dispositivo no Hub IoT do Azure.