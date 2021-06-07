---
title: Esquemas de Grade de Eventos do Azure para eventos dos Serviços de Mídia
description: Saiba mais sobre as propriedades que são fornecidas para eventos dos serviços de mídia com a grade de eventos do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 6b502690a62410f1e8d2950074e8b479f7dc7f88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608989"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Esquemas de Grade de Eventos do Azure para eventos dos Serviços de Mídia

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Este artigo fornece as propriedades e os esquemas para eventos de Serviços de Mídia.

Para obter uma lista de scripts de exemplo e tutoriais, consulte [Origem do evento dos Serviços de Mídia](../../../event-grid/event-schema-subscriptions.md).

## <a name="job-related-event-types"></a>Tipos de eventos relacionados ao trabalho

Os Serviços de Mídia emite os tipos de eventos relacionados ao **Trabalho** descritos abaixo. Há duas categorias para os eventos relacionados ao **Trabalho**: “Monitoramento de alterações de estado de trabalho” e “Monitoramento de alterações de estado de saída de trabalho”. 

Você pode registrar-se para todos os eventos assinando o evento JobStateChange. Ou então, você pode assinar somente eventos específicos (por exemplo, estados finais como JobErrored, JobFinished e JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Monitoramento de alterações de estado de trabalho

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Obtenha um evento para todas as alterações de estado do trabalho. |
| Microsoft.Media.JobScheduled| Obtenha um evento quando o trabalho faz a transição para o estado agendado. |
| Microsoft.Media.JobProcessing| Obtenha um evento quando o trabalho faz a transição para o estado em processamento. |
| Microsoft.Media.JobCanceling| Obtenha um evento quando o trabalho faz a transição para o estado em cancelamento. |
| Microsoft.Media.JobFinished| Obtenha um evento quando o trabalho faz a transição para um estado concluído. Este é um estado final que inclui as saídas do trabalho.|
| Microsoft.Media.JobCanceled| Obtenha um evento quando o trabalho faz a transição para um estado cancelado. Este é um estado final que inclui as saídas do trabalho.|
| Microsoft.Media.JobErrored| Obtenha um evento quando o trabalho faz a transição para um estado de erro. Este é um estado final que inclui as saídas do trabalho.|

Veja os [exemplos de esquema](#event-schema-examples) a seguir.

### <a name="monitoring-job-output-state-changes"></a>Alterações de estado de saída do trabalho de monitoramento

Um trabalho pode conter várias saídas de trabalho (se você tiver configurado a transformação para ter várias saídas de trabalho). Se você quiser acompanhar os detalhes da saída do trabalho individual, ouça um evento de alteração de saída do trabalho.

Cada **trabalho** será em um nível mais alto do que **JobOutput**, assim os eventos de saída de trabalho serão acionados dentro de um trabalho correspondente. 

As mensagens de erro no `JobFinished` , `JobCanceled` , `JobError` geram os resultados agregados para cada saída de trabalho – quando todos eles são concluídos. Enquanto que os eventos de saída do trabalho são acionados conforme cada tarefa é concluída. Por exemplo, se você tiver uma saída de codificação, seguida por uma saída de análise de vídeo, você obterá dois eventos acionando como eventos de saída de trabalho antes do evento JobFinished final ser acionado com os dados agregados.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Obtenha um evento para todas as alterações de estado da saída do trabalho. |
| Microsoft.Media.JobOutputScheduled| Obtenha um evento quando a saída do trabalho faz a transição para o estado agendado. |
| Microsoft.Media.JobOutputProcessing| Obtenha um evento quando a saída do trabalho faz a transição para o estado em processamento. |
| Microsoft.Media.JobOutputCanceling| Obtenha um evento quando a saída do trabalho faz a transição para o estado em cancelamento.|
| Microsoft.Media.JobOutputFinished| Obtenha um evento quando a saída do trabalho faz a transição para o estado concluído.|
| Microsoft.Media.JobOutputCanceled| Obtenha um evento quando a saída do trabalho faz a transição para o estado cancelado.|
| Microsoft.Media.JobOutputErrored| Obtenha um evento quando a saída do trabalho faz a transição para o estado de erro.|

Veja os [exemplos de esquema](#event-schema-examples) a seguir.

### <a name="monitoring-job-output-progress"></a>Progresso da saída do trabalho de monitoramento

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Esse evento reflete o progresso do processamento do trabalho, de 0% a 100%. O serviço tenta enviar um evento se houver um amento de 5% ou mais no valor do progresso ou se tiverem passado mais de 30 segundos desde o último evento (pulsação). Não é garantido que o valor do progresso comece em 0%, ou alcance 100%, nem que aumente a uma taxa constante com o passar do tempo. Esse evento não deve ser usado para determinar se o processamento foi concluído – em vez disso, você deve usar os eventos de alteração de estado.|

Veja os [exemplos de esquema](#event-schema-examples) a seguir.

## <a name="live-event-types"></a>Tipos de evento ao vivo

Os Serviços de Mídia também emitem os tipos de eventos **Ao vivo** descritos abaixo. Há duas categorias para os eventos **Ao Vivo**: eventos em nível de fluxo e eventos de nível de faixa. 

### <a name="stream-level-events"></a>Eventos em nível de fluxo

Os eventos em nível de fluxo são acionados por conexão ou fluxo. Cada evento tem um parâmetro `StreamId` que identifica a conexão ou o fluxo. Cada conexão ou fluxo tem uma ou mais faixas de tipos diferentes. Por exemplo, uma conexão de um codificador pode ter uma faixa de áudio e quatro faixas de vídeo. Os tipos de evento de fluxo são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | A tentativa de conexão do codificador foi rejeitada. |
| Microsoft.Media.LiveEventEncoderConnected | O codificador estabelece conexão com o evento ao vivo. |
| Microsoft.Media.LiveEventEncoderDisconnected | O codificador desconecta. |

Veja os [exemplos de esquema](#event-schema-examples) a seguir.

### <a name="track-level-events"></a>Eventos no nível de rastreamento

Os eventos em nível de faixa são acionados por faixa. 

> [!NOTE]
> Todos os eventos de nível de trilha são gerados depois que um codificador ao vivo é conectado.

Os tipos de evento de nível de rastreamento são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | O servidor de mídia descarta parte dos dados porque o tempo limite deles foi atingido ou eles têm um carimbo de data/hora de sobreposição (o carimbo de data/hora da nova parte de dados é anterior à hora de término da parte de dados anterior). |
| Microsoft.Media.LiveEventIncomingStreamReceived | O servidor de mídia recebe a primeira parte de dados para cada faixa no fluxo ou na conexão. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | O servidor de mídia detecta que os fluxos de áudio e vídeo estão fora de sincronia. Use como um aviso porque a experiência do usuário pode não ser afetada. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | O servidor de mídia detecta que qualquer um dos dois fluxos de vídeo provenientes do codificador externo está fora de sincronia. Use como um aviso porque a experiência do usuário pode não ser afetada. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicado a cada 20 segundos para cada faixa quando o evento ao vivo está em execução. Fornece resumo de integridade de ingestão.<br/><br/>Depois que o codificador tiver sido inicialmente conectado, o evento de pulsação continuará a emitir a cada 20 segundos, independentemente de o codificador ainda estar conectado ou não. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | O servidor de mídia detecta descontinuidade na faixa de entrada. |

Veja os [exemplos de esquema](#event-schema-examples) a seguir.

## <a name="event-schema-examples"></a>Exemplos de esquema de evento

### <a name="jobstatechange"></a>JobStateChange

O exemplo a seguir mostra o esquema do evento **JobStateChange**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| previousState | string | O estado do trabalho antes do evento. |
| state | string | O novo estado do trabalho que está sendo notificado nesse evento. Por exemplo, "agendado: o trabalho está pronto para iniciar" ou "concluído: o trabalho foi concluído".|

Quando o estado do Trabalho puder ser um deste valores: *Na fila*, *Agendado*, *Processando*, *Concluído*, *Erro*, *Cancelado*, *Cancelando*

> [!NOTE]
> *Em fila* estará presente somente na propriedade **previousState**, mas não na propriedade **state**.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Para cada alteração de estado do Trabalho não final (como JobScheduled, JobProcessing e JobCanceling), o esquema de exemplo é semelhante ao seguinte:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Para cada alteração de estado do Trabalho final (como JobFinished, JobCanceled e JobErrored), o esquema de exemplo é semelhante ao seguinte:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| outputs | Array | Obtém as saídas do trabalho.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

O exemplo a seguir mostra o esquema do evento **JobOutputStateChange**:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Para cada alteração de estado de JobOutput, o esquema de exemplo é semelhante ao seguinte:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

O exemplo de esquema é semelhante ao descrito a seguir:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

O exemplo a seguir mostra o esquema do evento **LiveEventConnectionRejected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| streamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por adicionar essa ID na URL de ingestão. |  
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |
| resultCode | string | O motivo pelo qual a conexão foi rejeitada. Os códigos de resultado estão listadas na tabela a seguir. |

Você pode encontrar os códigos de resultado de erro em [códigos de erro de eventos ao vivo](../live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

O exemplo a seguir mostra o esquema do evento **LiveEventEncoderConnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| streamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por fornecer essa ID na URL de ingestão. |
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

O exemplo a seguir mostra o esquema do evento **LiveEventEncoderDisconnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| streamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por adicionar essa ID na URL de ingestão. |  
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |
| resultCode | string | O motivo para a desconexão do codificador. Isso se deve à desconexão normal ou a um erro. Os códigos de resultado estão listadas na tabela a seguir. |

Você pode encontrar os códigos de resultado de erro em [códigos de erro de eventos ao vivo](../live-event-error-codes.md).

Os códigos de resultado de desconexão normal são:

| Código de Resultado | Descrição |
| ----------- | ----------- |
| S_OK | Codificador desconectado com êxito. |
| MPE_CLIENT_TERMINATED_SESSION | Codificador desconectado (RTMP). |
| MPE_CLIENT_DISCONNECTED | Codificador desconectado (FMP4). |
| MPI_REST_API_CHANNEL_RESET | O comando de redefinição de canal é recebido. |
| MPI_REST_API_CHANNEL_STOP | Comando de parada de canal recebido. |
| MPI_REST_API_CHANNEL_STOP | Canal passando por manutenção. |
| MPI_STREAM_HIT_EOF | O fluxo de EOF é enviado pelo codificador. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingDataChunkDropped**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo de faixa (Áudio/Vídeo). |
| trackName | string | Nome da faixa. |
| taxa de bits | inteiro | Taxa de bits da faixa. |
|  timestamp | string | O carimbo de data/hora da parte de dados é descartado. |
| escala de tempo | string | Escala de tempo do carimbo de data/hora. |
| resultCode | string | Motivo do descarte da parte de dados. **FragmentDrop_OverlapTimestamp** ou **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingStreamReceived**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo de faixa (Áudio/Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate*). |
| taxa de bits | inteiro | Taxa de bits da faixa. |
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |
| encoderIp | string  | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |
|  timestamp | string | O primeiro carimbo de data/hora da parte de dados recebida. |
| escala de tempo | string | Escala de tempo em que o carimbo de data/hora é representado. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| minLastTimestamp | string | Mínimo dos últimos carimbos de data/hora entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMinLastTimestamp | string | Tipo de faixa (áudio ou vídeo) com o último carimbo de data/hora mínimo. |
| maxLastTimestamp | string | Máximo de todos os carimbos de data/hora entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMaxLastTimestamp | string | Tipo de faixa (áudio ou vídeo) com o último carimbo de data/hora máximo. |
| timescaleOfMinLastTimestamp| string | Obtém a escala de tempo em que o “MinLastTimestamp” é representado.|
| timescaleOfMinLastTimestamp| string | Obtém a escala de tempo em que o “MaxLastTimestamp” é representado.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingVideoStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| firstTimestamp | string | O carimbo de data/hora recebido para uma das faixas/níveis de qualidade do vídeo de tipo. |
| firstDuration | string | Duração da parte de dados com primeiro carimbo de data/hora. |
| secondTimestamp | string  | O carimbo de data/hora recebido para algum outro nível de qualidade/faixa do vídeo de tipo. |
| secondDuration | string | Duração da parte de dados com segundo carimbo de data/hora. |
| escala de tempo | string | Escala de tempo de carimbos de data/hora e duração.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

O exemplo a seguir mostra o esquema do evento **LiveEventIngestHeartbeat**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo de faixa (Áudio/Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate*). |
| taxa de bits | inteiro | Taxa de bits da faixa. |
| incomingBitrate | inteiro | Taxa de bits calculada com base nas partes de dados provenientes do codificador. |
| lastTimestamp | string | Carimbo de data/hora mais recente recebido para uma faixa nos últimos 20 segundos. |
| escala de tempo | string | Escala de tempo na qual os carimbos de data/hora são expressos. |
| overlapCount | inteiro | O número de partes de dados que sobrepôs os carimbos de data/hora nos últimos 20 segundos. |
| discontinuityCount | inteiro | Número do descontinuidades observadas nos últimos 20 segundos. |
| nonIncreasingCount | inteiro | Número de partes de dados com os carimbos de data/hora no passado recebidas nos últimos 20 segundos. |
| unexpectedBitrate | bool | Se as taxas de bits esperadas e reais apresentarem diferença superior ao limite permitido nos últimos 20 segundos. É true se e somente se, IncomingBitrate >= 2* taxa de bits OU IncomingBitrate <= taxa de bits/2 OU IncomingBitrate = 0. |
| state | string | Estado do evento ao vivo. |
| Healthy | bool | Indica se a ingestão está íntegra com base nas contagens e nos sinalizadores. Healthy será true se OverlapCount = 0 && DiscontinuityCount = 0 && NonIncreasingCount = 0 && UnexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

O exemplo a seguir mostra o esquema do evento **LiveEventTrackDiscontinuityDetected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo de faixa (Áudio/Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate*). |
| taxa de bits | inteiro | Taxa de bits da faixa. |
| PreviousTimestamp | string | Carimbo de data/hora do fragmento anterior. |
| NewTimestamp | string | Carimbo de data/hora do fragmento atual. |
| discontinuityGap | string | Lacuna entre os dois carimbos de data/hora acima. |
| escala de tempo | string | Escala de tempo na qual o carimbo de data/hora e a lacuna de descontinuidade são representados. |

### <a name="common-event-properties"></a>Propriedades comuns de evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | string | O tópico EventGrid. Essa propriedade tem a ID de recurso para a conta de Serviços de Mídia. |
| subject | string | O caminho do recurso para o canal dos Serviços de Mídia sob a conta de Serviços de Mídia. A concatenação de tópico e assunto dê a você o recurso de ID para o trabalho. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. Por exemplo, "Microsoft.Media.JobStateChange". |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos dos Serviços de Mídia. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |

## <a name="next-steps"></a>Próximas etapas

[Registre-se para eventos de alteração de estado do trabalho](../job-state-events-cli-how-to.md)

## <a name="see-also"></a>Confira também

- [SDK do .NET EventGrid que inclui eventos de serviço de mídia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Códigos de erro de evento ao vivo](../live-event-error-codes.md)
