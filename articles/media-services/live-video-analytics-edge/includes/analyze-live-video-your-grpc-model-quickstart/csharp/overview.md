---
ms.openlocfilehash: 8d18b2e72a2a9e787f69d6adaeae8ebc3ca162b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582689"
---
![Visão geral](../../../media/quickstarts/gRPC-extension.svg)

O diagrama mostra como os sinais fluem neste guia de início rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmera IP que hospeda um servidor RTSP (Real-Time Streaming Protocol). Um nó de [origem RTSP](../../../media-graph-concept.md#rtsp-source) efetua pull do feed de vídeo desse servidor e envia quadros de vídeo para o nó do [processador de detecção de movimento](../../../media-graph-concept.md#motion-detection-processor). Esse processador detectará o movimento e, após a detecção, enviará por push quadros de vídeo para o nó do [processador de extensão gRPC](../../../media-graph-concept.md#grpc-extension-processor).

O nó de extensão gRPC desempenha a função de um proxy. Ele converte os quadros de vídeo no tipo de imagem especificado. Em seguida, ele retransmite a imagem gRPC para outro módulo de borda que executa um modelo de IA por trás de um ponto de extremidade gRPC em uma [memória compartilhada](https://en.wikipedia.org/wiki/Shared_memory). Neste exemplo, esse módulo de borda é criado usando o modelo [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), que é capaz de detectar diversos tipos de objetos. O nó do processador de extensão gRPC coleta os resultados da detecção e publica os eventos no nó do [coletor do Hub IoT](../../../media-graph-concept.md#iot-hub-message-sink). Em seguida, o nó envia esses eventos para o [Hub do IoT Edge](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

Neste guia de início rápido, você vai:

1. Criar e implantar o grafo de mídia.
1. Interpretar os resultados.
1. Limpar os recursos.