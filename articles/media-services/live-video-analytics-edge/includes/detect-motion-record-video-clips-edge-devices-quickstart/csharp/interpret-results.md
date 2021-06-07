---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88682055"
---
Quando você executa o grafo de mídia, os resultados do nó do processador de detecção de movimento passam para o Hub IoT por meio do nó do coletor do Hub IoT. As mensagens que você vê na janela **SAÍDA** do Visual Studio Code contêm uma seção `body` e uma seção `applicationProperties`. Para obter mais informações, confira [Criar e ler mensagens do Hub IoT](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, o módulo da Análise Dinâmica de Vídeo define as propriedades do aplicativo e o conteúdo do corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se a conexão for bem-sucedida, o evento a seguir será impresso.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

Na saída anterior: 

* A mensagem é um evento de diagnóstico, `MediaSessionEstablished`. Ela indica que o nó de origem RTSP (o assunto) estabeleceu uma conexão com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties`, `subject` faz referência ao nó na topologia do grafo do qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó de origem RTSP.
* Em `applicationProperties`, `eventType` indica que este é um evento de diagnóstico.
* O valor `eventTime` é a hora em que o evento ocorreu.
* A seção `body` contém dados sobre o evento de diagnóstico. Nesse caso, os dados compreendem os detalhes do [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quando o movimento é detectado, o nó do processador da porta do sinal é ativado e o nó do coletor de arquivos no grafo de mídia começa a gravar um arquivo MP4. O nó do coletor do arquivo envia um evento operacional. O `type` é definido como `motion` para indicar que se trata de um resultado do processador de detecção de movimento. O valor de `eventTime` é a hora, em UTC, em que o movimento ocorreu. Para obter mais informações sobre esse processo, confira a seção [Visão geral](#overview) deste início rápido.

Veja um exemplo dessa mensagem:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation&quot;: &quot;/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Na mensagem anterior: 

* Em `applicationProperties`, `subject` faz referência ao nó do grafo de mídia no qual a mensagem foi gerada. Nesse caso, a mensagem é originada do nó do coletor do arquivo.
* Em `applicationProperties`, `eventType` indica que este é um evento operacional.
* O valor `eventTime` é a hora em que o evento ocorreu. Essa hora é de 5 a 6 segundos após `MediaSessionEstablished`, e após o início do fluxo de vídeo. Isso corresponde à marca de 5 a 6 segundos quando [carro começou entrar](#review-the-sample-video) no estacionamento.
* A seção `body` contém dados sobre o evento operacional. Nesse caso, os dados são compostos por `outputType` e `outputLocation`.
* A variável `outputType` indica que essas informações são sobre o caminho do arquivo.
* O valor `outputLocation` é o local do arquivo MP4 no módulo de borda.

### <a name="recordingstopped-and-recordingavailable-events"></a>Eventos RecordingStopped e RecordingAvailable

Se você examinar as propriedades do nó processador da porta de sinal na [topologia do grafo](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), verá que os tempos de ativação foram definidos como 5 segundos. Portanto, cerca de 5 segundos após o evento `RecordingStarted` ser recebido, você tem:

* Um evento `RecordingStopped`, indicando que a gravação foi interrompida.
* Um evento `RecordingAvailable`, indicando que o arquivo MP4 agora pode ser usado para exibição.

Os dois eventos geralmente são emitidos em um intervalo de segundos entre si.
