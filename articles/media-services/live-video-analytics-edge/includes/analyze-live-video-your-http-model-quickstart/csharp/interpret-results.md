---
ms.openlocfilehash: 373b7b8dbf2ccc516edbbb3b87e284d88dfaf0c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508465"
---
Quando você executa o grafo de mídia, os resultados do nó do processador de extensão HTTP passam para o Hub IoT por meio do nó do coletor do Hub IoT. As mensagens que você vê na janela **SAÍDA** contêm uma seção `body` e uma seção `applicationProperties`. Para obter mais informações, confira [Criar e ler mensagens do Hub IoT](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Nas mensagens a seguir, o módulo da Análise Dinâmica de Vídeo define as propriedades do aplicativo e o conteúdo do corpo. 

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando a instância de um grafo de mídia é criada, o nó de origem RTSP tenta se conectar com o servidor RTSP em execução no contêiner rtspsim-live555. Se a conexão for bem-sucedida, o evento a seguir será impresso. O tipo de evento é `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Nesta mensagem, observe estes detalhes:

* A mensagem é um evento de diagnóstico. `MediaSessionEstablished` indica que o nó de origem RTSP (o assunto) se conectou com o simulador RTSP e começou a receber um feed ao vivo (simulado).
* Em `applicationProperties`, `subject` indica que a mensagem foi gerada do nó de origem RTSP no grafo de mídia.
* Em `applicationProperties`, `eventType` indica que este é um evento de diagnóstico.
* O `eventTime` indica a hora em que o evento ocorreu.
* O `body` contém dados sobre o evento de diagnóstico. Nesse caso, os dados compreendem os detalhes do [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento de inferência

O nó processador de extensão HTTP recebe resultados de inferência do módulo yolov3. Em seguida, ele emite os resultados por meio do nó do coletor do Hub IoT como eventos de inferência. 

Nesses eventos, o tipo é definido como `entity` para indicar que se trata de uma entidade, como um carro ou um caminhão. O valor de `eventTime` é a hora, em UTC, em que o objeto foi detectado. 

No exemplo a seguir, que dois carros foram detectados no mesmo quadro de vídeo com níveis de confiança diferentes.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value&quot;: &quot;car"
          }
        },
        "type&quot;: &quot;entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value&quot;: &quot;car"
          }
        },
        "type&quot;: &quot;entity"
      }
    ]
  }
}
```

Nas mensagens, observe os seguintes detalhes:

* O valor `eventTime` é a hora em que o evento ocorreu.
* A seção `body` contém dados sobre o evento de análise. Nesse caso, o evento é um evento de inferência, portanto, o corpo contém dados de `inferences`.
* A seção `inferences` indica que o `type` é `entity`. Esta seção inclui dados adicionais sobre a entidade.
