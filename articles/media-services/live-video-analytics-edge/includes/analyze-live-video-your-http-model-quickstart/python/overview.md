---
ms.openlocfilehash: 0e800668bea2f744f8862292b21be814858b0c16
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486733"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Fluxo de sinais":::

O diagrama mostra como os sinais fluem neste guia de início rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](../../../media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de extensão HTTP](../../../media-graph-concept.md#http-extension-processor). 

O nó de extensão HTTP desempenha a função de um proxy. Ele faz uma amostragem dos quadros de vídeo de entrada definidos por você usando o campo `samplingOptions` e converte os quadros de vídeo no tipo de imagem especificado. Em seguida, ele retransmite a imagem por REST para outro módulo de borda que executa um modelo de IA por trás de um ponto de extremidade HTTP. Neste exemplo, esse módulo de borda é criado usando o modelo [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), que é capaz de detectar diversos tipos de objetos. O nó de processador de extensão HTTP reúne os resultados da detecção e publica eventos no nó do [coletor do Hub IoT](../../../media-graph-concept.md#iot-hub-message-sink). Em seguida, o nó envia esses eventos para o [Hub do IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Neste guia de início rápido, você vai:

1. Criar e implantar o grafo de mídia.
1. Interpretar os resultados.
1. Limpar os recursos.
