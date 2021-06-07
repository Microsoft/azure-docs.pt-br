---
title: Gravação de vídeo baseada em evento-Azure
description: A EVR (Gravação de vídeo baseada em eventos) se refere ao processo de gravação de vídeo acionado por um evento. O evento em questão poderia se originar devido ao processamento do próprio sinal de vídeo (por exemplo, detecção de movimento) ou poderia ser de uma fonte independente (por exemplo, abertura de uma porta).  Alguns casos de uso relacionados à gravação de vídeo baseada em evento são descritos neste artigo.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97401587"
---
# <a name="event-based-video-recording"></a>Gravação de vídeo baseada em eventos  
 
## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

* [Gravação de vídeo contínua](continuous-video-recording-concept.md)
* [Reprodução de conteúdo gravado](video-playback-concept.md)
* [Conceito de grafo de mídia](media-graph-concept.md)

## <a name="overview"></a>Visão geral 

A EVR (Gravação de vídeo baseada em eventos) se refere ao processo de gravação de vídeo acionado por um evento. O evento em questão pode se originar devido ao processamento do próprio sinal de vídeo (por exemplo, detecção de movimento) ou poderia ser de uma fonte independente (por exemplo, abertura de uma porta). 

Você pode gravar vídeo (disparado por um evento) de uma câmera CCTV para um ativo dos serviços de mídia usando um grafo de mídia que consiste em um nó de [origem RTSP](media-graph-concept.md#rtsp-source) , um nó do [coletor de ativos](media-graph-concept.md#asset-sink) e outros nós, conforme descrito nos casos de uso abaixo. Você pode configurar o nó do [coletor de ativos](media-graph-concept.md#asset-sink) para gerar novos ativos cada vez que um evento ocorrer, para que o vídeo que corresponde a cada evento esteja em seu próprio ativo. Você também pode optar por usar um ativo para manter o vídeo de todos os eventos. 

Como alternativa ao nó do coletor de ativos, você pode usar um nó de [coletor de arquivos](media-graph-concept.md#file-sink) para capturar trechos curtos de vídeo (relacionados a um evento de interesse) para um local especificado no sistema de arquivos local no dispositivo de borda. 

Alguns casos de uso relacionados à gravação de vídeo baseada em evento são descritos neste artigo.

### <a name="video-recording-based-on-motion-detection"></a>Gravação de vídeo com base na detecção de movimento  

Nesse caso de uso, você pode gravar clipes de vídeo somente quando houver movimento detectado no vídeo e ser alertado quando tal clipe de vídeo for gerado. Isso pode ser relevante em cenários de segurança comercial que envolvem a proteção da infraestrutura crítica. Ao gerar alertas e gravar vídeo quando é detectado um movimento inesperado, você pode melhorar a eficiência do operador humano, pois a ação só é necessária quando o alerta é gerado.

O diagrama a seguir mostra uma representação gráfica de um grafo de mídia que aborda esse caso de uso. A representação JSON da topologia do grafo desse grafo de mídia pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Gravação de vídeo com base na detecção de movimento":::

No diagrama, o nó de origem RTSP captura o feed de vídeo ao vivo da câmera e a entrega a um nó de [processador de detecção de movimento](media-graph-concept.md#motion-detection-processor) . Ao detectar o movimento no vídeo ao vivo, o nó do processador de detecção de movimento gera um evento que vai para o nó do [processador da porta do sinal](media-graph-concept.md#signal-gate-processor) , bem como para o nó do coletor de mensagens do Hub IOT. O último nó envia os eventos para o Hub de IoT Edge, de onde eles podem ser roteados para outros destinos para disparar alertas. 

Um evento do nó detector de movimento irá disparar o nó processador da porta de sinal e permitirá que o feed de vídeo ao vivo do nó de origem RTSP flua para o nó do coletor de ativos. Na ausência de eventos de detecção de movimento subsequentes, o portão será fechado após um período de tempo configurado. Isso determina a duração do vídeo gravado.

### <a name="video-recording-based-on-events-from-other-sources"></a>Gravação de vídeo com base em eventos de outras fontes  

Nesse caso de uso, os sinais de outro sensor IoT podem ser usados para disparar a gravação de vídeo. O diagrama a seguir mostra uma representação gráfica de um grafo de mídia que aborda esse caso de uso. A representação JSON da topologia do grafo desse grafo de mídia pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Gravação de vídeo com base em eventos de outras fontes":::

No diagrama, o sensor externo envia eventos para o Hub de IoT Edge. Em seguida, os eventos são roteados para o nó processador da porta do sinal por meio do nó [origem da mensagem do Hub IOT](media-graph-concept.md#iot-hub-message-source) . O comportamento do nó do processador da porta de sinal é o mesmo que com o caso de uso anterior – ele será aberto e permitirá que o feed de vídeo ao vivo passe do nó de origem RTSP para o nó do coletor de arquivos (ou o nó do coletor de ativos) quando ele for disparado pelo evento externo. 

Se você usar um nó de coletor de arquivos, o vídeo será gravado no sistema de arquivos local no dispositivo de borda. Caso contrário, se você usar um nó do coletor de ativos, o vídeo será gravado em um ativo.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Gravação de vídeo com base em um módulo inferência externo 

Nesse caso de uso, você pode gravar clipes de vídeo com base em um sinal de um sistema lógico externo. Um exemplo de tal caso de uso poderia gravar um clipe de vídeo somente quando um caminhão for detectado no feed de vídeo do tráfego em uma rodovia. O diagrama a seguir mostra uma representação gráfica de um grafo de mídia que aborda esse caso de uso. A representação JSON da topologia do grafo desse grafo de mídia pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Gravação de vídeo com base em um módulo inferência externo":::

No diagrama, o nó de origem RTSP captura o feed de vídeo ao vivo da câmera e a entrega a duas ramificações: uma tem um nó de [processador da porta de sinal](media-graph-concept.md#signal-gate-processor) e a outra usa um nó de [extensão http](media-graph-concept.md) para enviar dados a um módulo lógico externo. O nó de extensão HTTP permite que o grafo de mídia envie quadros de imagem (em formatos JPEG, BMP ou PNG) para um serviço de inferência externo em relação ao REST. Esse caminho de sinal normalmente pode dar suporte apenas a baixas taxas de quadros (<5fps). Você pode usar o nó processador de extensão HTTP para reduzir a taxa de quadros do vídeo indo para o módulo inferência externo.

Os resultados do serviço de inferência externa são recuperados pelo nó de extensão HTTP e retransmitidos para o Hub de IoT Edge por meio do nó de coletor de mensagens do Hub IoT, no qual eles podem ser processados ainda mais pelo módulo de lógica externa. Se o serviço de inferência for capaz de detectar veículos, por exemplo, o módulo lógico poderá procurar um veículo específico, como um barramento ou um caminhão. Quando o módulo lógico detecta o objeto de interesse, ele pode disparar o nó do processador da porta do sinal enviando um evento por meio do hub de IoT Edge para o nó de origem da mensagem do Hub IoT no grafo. A saída da porta de sinal é mostrada para ir para um nó de coletor de arquivo ou um nó de coletor de ativos. No primeiro caso, o vídeo será gravado no sistema de arquivos local no dispositivo de borda. No último caso, o vídeo será gravado em um ativo.

Um aprimoramento deste exemplo é usar um processador de detector de movimento à frente do nó do processador de extensão HTTP. Isso reduzirá a carga no serviço de inferência, como a noitetime, quando pode haver longos períodos de tempo em que não há veículos na rodovia. 

## <a name="next-steps"></a>Próximas etapas

[Tutorial: gravação de vídeo baseada em evento](event-based-video-recording-tutorial.md)
