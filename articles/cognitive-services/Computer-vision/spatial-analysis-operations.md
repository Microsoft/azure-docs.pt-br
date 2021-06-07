---
title: Operações de Análise Espacial
titleSuffix: Azure Cognitive Services
description: As operações de Análise Espacial.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 4b4ee9d1e583241f8ec9b467ae9ddfdb1360fb52
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284695"
---
# <a name="spatial-analysis-operations"></a>Operações de Análise Espacial

A Análise Espacial permite a análise de vídeo de streaming em tempo real de dispositivos com câmera. Para cada dispositivo com câmera configurado, as operações de Análise Espacial geram um fluxo de saída de mensagens JSON enviadas à sua instância do Hub IoT do Azure. 

O contêiner de Análise Espacial implementa as seguintes operações:

| Identificador da operação| Descrição|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Conta as pessoas em uma zona designada no campo de visão da câmera. A zona deve ser coberta totalmente por uma mesma câmera para que PersonCount registre um total preciso. <br> Emite um evento _personCountEvent_ inicial e, depois, eventos _personCountEvent_ quando a contagem é alterada.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Controla quando uma pessoa cruza uma linha designada no campo de visão da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações de direção. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Emite um evento _personZoneEnterExitEvent_ quando uma pessoa entra ou sai da zona e fornece informações de direção com o lado numerado da zona que foi cruzada. Emite um _personZoneDwellTimeEvent_ quando a pessoa sai da zona e fornece informações de direção, bem como o número de milissegundos que a pessoa permaneceu dentro da zona. |
| cognitiveservices.vision.spatialanalysis-persondistance | Controla quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |
| cognitiveservices.vision.spatialanalysis | Operação genérica que pode ser usada para executar todos os cenários mencionados acima. Essa opção é mais útil quando você deseja executar vários cenários na mesma câmera ou usar recursos do sistema (por exemplo, a GPU) com mais eficiência. |

Todas as operações acima também estão disponíveis na versão `.debug` e têm a capacidade de visualizar os quadros de vídeo conforme eles são processados. É necessário executar `xhost +` no computador host para habilitar a visualização de eventos e quadros de vídeo.

| Identificador da operação| Descrição|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Conta as pessoas em uma zona designada no campo de visão da câmera. <br> Emite um evento _personCountEvent_ inicial e, depois, eventos _personCountEvent_ quando a contagem é alterada.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Controla quando uma pessoa cruza uma linha designada no campo de visão da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações de direção. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Emite um evento _personZoneEnterExitEvent_ quando uma pessoa entra ou sai da zona e fornece informações de direção com o lado numerado da zona que foi cruzada. Emite um _personZoneDwellTimeEvent_ quando a pessoa sai da zona e fornece informações de direção, bem como o número de milissegundos que a pessoa permaneceu dentro da zona. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Controla quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |
| cognitiveservices.vision.spatialanalysis.debug | Operação genérica que pode ser usada para executar todos os cenários mencionados acima. Essa opção é mais útil quando você deseja executar vários cenários na mesma câmera ou usar recursos do sistema (por exemplo, a GPU) com mais eficiência. |

A Análise Espacial também pode ser executada com a [Análise Dinâmica de Vídeo](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) como módulo de IA de vídeo. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identificador da operação| Descrição|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Conta as pessoas em uma zona designada no campo de visão da câmera. <br> Emite um evento _personCountEvent_ inicial e, depois, eventos _personCountEvent_ quando a contagem é alterada.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Controla quando uma pessoa cruza uma linha designada no campo de visão da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações de direção. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Emite um evento _personZoneEnterExitEvent_ quando uma pessoa entra ou sai da zona e fornece informações de direção com o lado numerado da zona que foi cruzada. Emite um _personZoneDwellTimeEvent_ quando a pessoa sai da zona e fornece informações de direção, bem como o número de milissegundos que a pessoa permaneceu dentro da zona.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Controla quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | Operação genérica que pode ser usada para executar todos os cenários mencionados acima. Essa opção é mais útil quando você deseja executar vários cenários na mesma câmera ou usar recursos do sistema (por exemplo, a GPU) com mais eficiência. |

As operações de Análise Dinâmica de Vídeo também estão disponíveis na versão `.debug` (por exemplo, cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug), que tem a capacidade de visualizar os quadros de vídeo como sendo processados. É necessário executar `xhost +` no computador host para habilitar a visualização de eventos e quadros de vídeo.

> [!IMPORTANT]
> Os modelos de IA da Pesquisa Visual Computacional detectam e localizam a presença humana no vídeo e na saída usando uma caixa delimitadora em torno de um corpo humano. Os modelos de IA não tentam descobrir as identidades nem os dados demográficos dos indivíduos.

Estes são os parâmetros necessários para cada uma dessas operações de Análise Espacial.

| Parâmetros de operação| Descrição|
|---------|---------|
| ID da Operação | O identificador da operação da tabela acima.|
| Habilitado | Booliano: true ou false|
| VIDEO_URL| A URL RTSP para o dispositivo com câmera (por exemplo, `rtsp://username:password@url` ). A Análise Espacial dá suporte ao fluxo codificado em H.264 por meio de RTSP, HTTP ou mp4. Video_URL pode ser fornecido como um valor de cadeia de caracteres base64 ocultado usando criptografia AES. Se a URL do vídeo estiver ocultada, `KEY_ENV` e `IV_ENV` precisarão ser fornecidas como variáveis de ambiente. Um utilitário de exemplo para gerar chaves e criptografia pode ser encontrado [aqui](/dotnet/api/system.security.cryptography.aesmanaged). |
| VIDEO_SOURCE_ID | Um nome amigável para o dispositivo com câmera ou fluxo de vídeo. Será retornado com a saída do evento JSON.|
| VIDEO_IS_LIVE| Verdadeiro para dispositivos com câmera; falso para vídeos gravados.|
| VIDEO_DECODE_GPU_INDEX| Qual GPU decodificará o quadro de vídeo. Por padrão, é 0. Deve ser igual a `gpu_index` na configuração do outro nó, por exemplo, `VICA_NODE_CONFIG`, `DETECTOR_NODE_CONFIG`.|
| INPUT_VIDEO_WIDTH | Largura do quadro do vídeo/fluxo de entrada (por exemplo, 1920). Esse é um campo opcional e, se fornecido, o quadro será escalado para essa dimensão, preservando a taxa de proporção.|
| DETECTOR_NODE_CONFIG | JSON que indica em qual GPU executar o nó do detector. Ele deve estar no seguinte formato: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Configuração de JSON para a zona e a linha, conforme descrito abaixo.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` para habilitar a detecção de pessoas usando máscaras que cobrem o rosto no fluxo de vídeo, `False` para desabilitá-la. Por padrão, fica desabilitado. A detecção de máscaras que cobrem o rosto exige que o parâmetro de largura do vídeo de entrada seja 1920 `"INPUT_VIDEO_WIDTH": 1920`. O atributo de máscara que cobre o rosto não será retornado se as pessoas detectadas não estiverem voltadas para a câmera ou se estiverem muito longe dela. Confira o guia de [posicionamento da câmera](spatial-analysis-camera-placement.md) para obter mais informações |

### <a name="detector-node-parameter-settings"></a>Configurações do parâmetro de nó do detector
Este é um exemplo dos parâmetros de DETECTOR_NODE_CONFIG para todas as operações de Análise Espacial.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `gpu_index` | string| O índice de GPU em que esta operação será executada.|
| `do_calibration` | string | Indica que a calibragem está ativada. `do_calibration` precisa ser verdadeiro para que **cognitiveservices.vision.spatialanalysis-persondistance** funcione corretamente. Por padrão, do_calibration é definido como True. |
| `enable_recalibration` | bool | Indica se a recalibragem automática está ativada. O padrão é `true`.|
| `calibration_quality_check_frequency_seconds` | INT | Número mínimo de segundos entre cada verificação de qualidade para determinar se a recalibragem é necessária. O padrão é `86400` (24 horas). Usado somente quando `enable_recalibration=True`.|
| `calibration_quality_check_sample_collect_frequency_seconds` | INT | Número mínimo de segundos entre a coleta de novas amostras de dados para recalibragem e a verificação de qualidade. O padrão é `300` (5 minutos). Usado somente quando `enable_recalibration=True`.|
| `calibration_quality_check_one_round_sample_collect_num` | INT | Número mínimo de novas amostras de dados a serem coletadas por rodada de coleta de amostra. O padrão é `10`. Usado somente quando `enable_recalibration=True`.|
| `calibration_quality_check_queue_max_size` | INT | Número máximo de amostras de dados a serem armazenadas quando o modelo da câmera é calibrado. O padrão é `1000`. Usado somente quando `enable_recalibration=True`.|
| `enable_breakpad`| bool | Indica se você deseja habilitar breakpad, que é usado para gerar o despejo de memória para uso para depuração. Por padrão, é `false`. Se defini-lo como `true`, você também precisará adicionar `"CapAdd": ["SYS_PTRACE"]` na parte `HostConfig` do contêiner `createOptions`. Por padrão, o despejo de memória é carregado no aplicativo [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) do App Center. Se quiser que os despejos de memória sejam carregados em seu aplicativo do App Center, você poderá substituir a variável de ambiente `RTPT_APPCENTER_APP_SECRET` pelo segredo do aplicativo.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Configuração e saída das operações de Análise Espacial
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Configuração de zona para cognitiveservices.vision.spatialanalysis-personcount

 Este é um exemplo de entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Você pode configurar várias zonas para esta operação.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável da zona.|
| `polygon` | list| Cada par de valor representa o x,y para os vértices de um polígono. O polígono representa as áreas em que as pessoas são rastreadas ou contadas, e os pontos do polígono são baseados em coordenadas normalizadas (0-1), em que o canto superior esquerdo é (0,0; 0,0) e o canto inferior direito é (1,0; 1,0).   
| `threshold` | FLOAT| Os eventos são gerados quando a confiança dos modelos de IA é maior ou igual ao valor. |
| `type` | string| Para **cognitiveservices.vision.spatialanalysis-personcount**, deve ser `count`.|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores com suporte são `event` para enviar eventos quando a contagem é alterada ou `interval` para enviar eventos periodicamente, tenha a contagem sido alterada ou não.
| `output_frequency` | INT | A taxa em que os eventos são gerados. Quando `output_frequency` = X, todo evento X é gerado. Por exemplo, `output_frequency` = 2 significa que um de cada dois eventos é gerado. O `output_frequency` é aplicável a `event` e `interval`. |
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usado para calcular eventos. O valor do foco pode ser `footprint` (o volume da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Configuração de linha para cognitiveservices.vision.spatialanalysis-personcrossingline

Este é um exemplo de entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma linha. Você pode configurar várias linhas de cruzamento para esta operação.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `lines` | list| Lista de linhas.|
| `name` | string| Nome amigável da linha.|
| `line` | list| A definição da linha. Essa é uma linha direcional que permite entender "entrada" versus "saída".|
| `start` | par de valores| Coordenadas x, y do ponto inicial da linha. Os valores float representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores absolutos de x, y, você multiplica esses valores pelo tamanho do quadro. |
| `end` | par de valores| Coordenadas x, y do ponto final da linha. Os valores float representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores absolutos de x, y, você multiplica esses valores pelo tamanho do quadro. |
| `threshold` | FLOAT| Os eventos são gerados quando a confiança dos modelos de IA é maior ou igual ao valor. O valor padrão é 16. Esse é o valor recomendado para obter a precisão máxima. |
| `type` | string| Para **cognitiveservices.vision.spatialanalysis-personcrossingline**, deve ser `linecrossing`.|
|`trigger`|string|O tipo de gatilho para enviar um evento.<br>Valores com suporte: "event": disparar quando alguém cruza a linha.|
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usado para calcular eventos. O valor do foco pode ser `footprint` (o volume da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa). O valor padrão é volume.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Configuração de zona para cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Este é um exemplo de entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Você pode configurar várias zonas para esta operação.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável da zona.|
| `polygon` | list| Cada par de valor representa o x,y para os vértices do polígono. O polígono representa as áreas nas quais as pessoas são rastreadas ou contadas. Os valores float representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores absolutos de x, y, você multiplica esses valores pelo tamanho do quadro. 
| `threshold` | FLOAT| Os eventos são gerados quando a confiança dos modelos de IA é maior ou igual ao valor. O valor padrão é 48 quando o tipo é zonecrossing e 16 quando o tempo é DwellTime. Esses são os valores recomendados para obter a precisão máxima.  |
| `type` | string| Para **cognitiveservices.vision.spatialanalysis-personcrossingpolygon**, deve ser `zonecrossing` ou `zonedwelltime`.|
| `trigger`|string|O tipo de gatilho para enviar um evento<br>Valores com suporte: "event": disparar quando alguém entra ou sai da zona.|
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usado para calcular eventos. O valor do foco pode ser `footprint` (o volume da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa). O valor padrão é volume.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Configuração de zona para cognitiveservices.vision.spatialanalysis-persondistance

Este é um exemplo de entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona para **cognitiveservices.vision.spatialanalysis-persondistance**. Você pode configurar várias zonas para esta operação.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável da zona.|
| `polygon` | list| Cada par de valor representa o x,y para os vértices do polígono. O polígono representa as áreas em que as pessoas são contadas e a distância entre as pessoas é medida. Os valores float representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores absolutos de x, y, você multiplica esses valores pelo tamanho do quadro. 
| `threshold` | FLOAT| Os eventos são gerados quando a confiança dos modelos de IA é maior ou igual ao valor. |
| `type` | string| Para **cognitiveservices.vision.spatialanalysis-persondistance**, deve ser `people_distance`.|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores com suporte são `event` para enviar eventos quando a contagem é alterada ou `interval` para enviar eventos periodicamente, tenha a contagem sido alterada ou não.
| `output_frequency` | INT | A taxa em que os eventos são gerados. Quando `output_frequency` = X, todo evento X é gerado. Por exemplo, `output_frequency` = 2 significa que um de cada dois eventos é gerado. O `output_frequency` é aplicável a `event` e `interval`.|
| `minimum_distance_threshold` | FLOAT| Uma distância em pés que dispara um evento "TooClose" quando as pessoas estão a uma distância inferior a esse valor.|
| `maximum_distance_threshold` | FLOAT| Uma distância em pés que dispara um evento "TooFar" quando as pessoas estão a uma distância superior a esse valor.|
| `aggregation_method` | string| O método para o resultado de persondistance agregado. O aggregation_method é aplicável a `mode` e `average`.|
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usado para calcular eventos. O valor do foco pode ser `footprint` (o volume da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa).|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>Configuração para cognitiveservices.vision.spatialanalysis
Este é um exemplo de entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma linha e uma zona para **cognitiveservices.vision.spatialanalysis**. Você pode configurar várias linhas/zonas para essa operação, e cada uma delas pode ter eventos diferentes.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Configuração da câmera

Confira as diretrizes de [posicionamento da câmera](spatial-analysis-camera-placement.md) para saber mais sobre como configurar zonas e linhas.

## <a name="spatial-analysis-operation-output"></a>Saída da operação de Análise Espacial

Os eventos de cada operação são gerados para o Hub IoT do Azure no formato JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Formato JSON para Insights da IA de cognitiveservices.vision.spatialanalysis-personcount

Exemplo de JSON para uma saída de evento por esta operação.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo do evento | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `zone` | string | O campo "name" do polígono que representa a zona que foi cruzada|
| `trigger` | string| O tipo de gatilho é 'event' ou 'interval' dependendo do valor de `trigger` em SPACEANALYTICS_CONFIG|

| Nome do campo de detecções | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID da detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `face_mask` | FLOAT | O valor de confiança do atributo, com o intervalo (0-1), indica que a pessoa detectada está usando uma máscara que cobre o rosto |
| `face_nomask` | FLOAT | O valor de confiança do atributo, com o intervalo (0-1), indica que a pessoa detectada **não** está usando uma máscara que cobre o rosto |

| Nome do campo SourceInfo | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|
| `cameraCallibrationInfo` | collection | Coleção de valores|
| `status` | string | O status da calibragem no formato de `state[;progress description]`. O estado pode ser `Calibrating`, `Recalibrating` (se a recalibragem estiver habilitada) ou `Calibrated`. A parte de descrição do progresso só é válida quando está no estado `Calibrating` e `Recalibrating`, que é usado para mostrar o progresso do processo de calibragem atual.|
| `cameraHeight` | FLOAT | A altura da câmera acima do solo em pés. É inferida com base na calibragem automática. |
| `focalLength` | FLOAT | A distância focal da câmera em pixels. É inferida com base na calibragem automática. |
| `tiltUpAngle` | FLOAT | O ângulo de inclinação da câmera na vertical. É inferida com base na calibragem automática.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Formato JSON para Insights da IA de cognitiveservices.vision.spatialanalysis-personcrossingline

Exemplo de JSON para a saída de detecções por esta operação.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nome do campo do evento | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `status` | string| Direção dos cruzamentos de linha, 'CrossLeft' ou 'CrossRight'. A direção se baseia em imaginar a posição no "início", voltado para o "fim" da linha. CrossRight significa cruzar da esquerda para a direita. CrossLeft significa cruzar da direita para a esquerda.|
| `zone` | string | O campo "name" da linha que foi cruzada|

| Nome do campo de detecções | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID da detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `face_mask` | FLOAT | O valor de confiança do atributo, com o intervalo (0-1), indica que a pessoa detectada está usando uma máscara que cobre o rosto |
| `face_nomask` | FLOAT | O valor de confiança do atributo, com o intervalo (0-1), indica que a pessoa detectada **não** está usando uma máscara que cobre o rosto |

| Nome do campo SourceInfo | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|


> [!IMPORTANT]
> O modelo de IA detecta pessoas voltadas para a câmera ou para a direção oposta. O modelo de IA não executa o reconhecimento facial nem emite informações biométricas. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Formato JSON para Insights da IA de cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Exemplo de JSON para a saída de detecções por essa operação com o tipo de `zonecrossing` de SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

Exemplo de JSON para a saída de detecções por essa operação com o tipo de `zonedwelltime` de SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo do evento | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento. O valor pode ser _personZoneDwellTimeEvent_ ou _personZoneEnterExitEvent_|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `status` | string| Direção dos cruzamentos do polígono, 'Enter' ou 'Exit'|
| `side` | INT| O número do lado do polígono que a pessoa cruzou. Cada lado é uma borda numerada entre os dois vértices do polígono que representa a zona. A borda entre os dois primeiros vértices do polígono representa o primeiro lado. 'Side' fica vazio quando o evento não está associado a um lado específico devido à oclusão. Por exemplo, uma saída ocorreu quando uma pessoa desapareceu, mas não foi vista cruzando um lado da zona, ou uma entrada ocorreu quando uma pessoa apareceu na zona, mas não foi vista atravessando um lado.|
| `durationMs` | FLOAT | O número de milissegundos que representam o tempo que a pessoa permaneceu na zona. Este campo é fornecido quando o tipo de evento é _personZoneDwellTimeEvent_|
| `zone` | string | O campo "name" do polígono que representa a zona que foi cruzada|

| Nome do campo de detecções | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID da detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `face_mask` | FLOAT | O valor de confiança do atributo, com o intervalo (0-1), indica que a pessoa detectada está usando uma máscara que cobre o rosto |
| `face_nomask` | FLOAT | O valor de confiança do atributo, com o intervalo (0-1), indica que a pessoa detectada **não** está usando uma máscara que cobre o rosto |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Formato JSON para Insights da IA de cognitiveservices.vision.spatialanalysis-persondistance

Exemplo de JSON para a saída de detecções por esta operação.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo do evento | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `personCount` | INT| Número de pessoas detectadas quando o evento foi emitido|
| `averageDistance` | FLOAT| A distância média entre todas as pessoas detectadas em pés|
| `minimumDistanceThreshold` | FLOAT| A distância em pés que dispara um evento "TooClose" quando as pessoas estão a uma distância inferior a esse valor.|
| `maximumDistanceThreshold` | FLOAT| A distância em pés que dispara um evento "TooFar" quando as pessoas estão a uma distância superior a esse valor.|
| `eventName` | string| O nome do evento é `TooClose` com o `minimumDistanceThreshold` violado, `TooFar` quando `maximumDistanceThreshold` é violado ou `unknown` quando a calibragem automática não foi concluída|
| `distanceViolationPersonCount` | INT| Número de pessoas detectadas em violação de `minimumDistanceThreshold` ou `maximumDistanceThreshold`|
| `zone` | string | O campo "name" do polígono que representa a zona que foi monitorada com relação ao distanciamento entre as pessoas|
| `trigger` | string| O tipo de gatilho é 'event' ou 'interval' dependendo do valor de `trigger` em SPACEANALYTICS_CONFIG|

| Nome do campo de detecções | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID da detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `centerGroundPoint` | 2 valores flutuantes| Valores `x`, `y` com as coordenadas do local inferido da pessoa no solo em pés. `x` e `y` são coordenadas no plano do solo, supondo que o solo seja nivelado. O local da câmera é a origem. |

Ao calcular `centerGroundPoint`, `x` é a distância da câmera à pessoa ao longo de uma linha perpendicular ao plano de imagem da câmera. `y` é a distância da câmera à pessoa ao longo de uma linha paralela ao plano de imagem da câmera. 

![Exemplo de ponto central no solo](./media/spatial-analysis/x-y-chart.png) 

Neste exemplo, `centerGroundPoint` é `{x: 4, y: 5}`. Isso significa que há uma pessoa a 4 pés de distância da câmera e 5 pés à direita, observando a sala de cima para baixo.


| Nome do campo SourceInfo | Tipo| Descrição|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|
| `cameraCallibrationInfo` | collection | Coleção de valores|
| `status` | string | O status da calibragem no formato de `state[;progress description]`. O estado pode ser `Calibrating`, `Recalibrating` (se a recalibragem estiver habilitada) ou `Calibrated`. A parte de descrição do progresso só é válida quando está no estado `Calibrating` e `Recalibrating`, que é usado para mostrar o progresso do processo de calibragem atual.|
| `cameraHeight` | FLOAT | A altura da câmera acima do solo em pés. É inferida com base na calibragem automática. |
| `focalLength` | FLOAT | A distância focal da câmera em pixels. É inferida com base na calibragem automática. |
| `tiltUpAngle` | FLOAT | O ângulo de inclinação da câmera na vertical. É inferida com base na calibragem automática.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>Formato JSON para Insights da IA de cognitiveservices.vision.spatialanalysis

A saída dessa operação depende do `events` configurado, por exemplo, se houver um evento `zonecrossing` configurado para a operação, a saída será igual a `cognitiveservices.vision.spatialanalysis-personcrossingpolygon`.

## <a name="use-the-output-generated-by-the-container"></a>Usar a saída gerada pelo contêiner

Você pode desejar integrar a detecção ou eventos de Análise Espacial em seu aplicativo. Veja algumas abordagens a serem consideradas: 

* Use o SDK do Hub de Eventos do Azure para a linguagem de programação escolhida para se conectar ao ponto de extremidade do Hub IoT do Azure e receber os eventos. Confira [Ler mensagens de dispositivo para nuvem do ponto de extremidade interno](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) para obter mais informações. 
* Configure o **Roteamento de mensagens** no Hub IoT do Azure para enviar os eventos para outros pontos de extremidade ou salve os eventos no armazenamento de dados. Confira [Usar o roteamento de mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-d2c.md) para obter mais informações. 
* Configure um trabalho do Azure Stream Analytics para processar os eventos em tempo real à medida que eles chegam e criar visualizações. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Implantando operações de Análise Espacial em escala (várias câmeras)

Para obter o melhor desempenho e utilização das GPUs, você pode implantar qualquer operação de Análise Espacial em várias câmeras usando instâncias de grafo. Abaixo está um exemplo para executar a operação `cognitiveservices.vision.spatialanalysis-personcrossingline` em quinze câmeras.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Nome | Tipo| Descrição|
|---------|---------|---------|
| `batch_size` | INT | Se todas as câmeras tiverem a mesma resolução, defina `batch_size` como o número de câmeras que serão usadas na operação. Caso contrário, defina `batch_size` como 1 ou deixe como padrão (1), o que indica que não há suporte para nenhum lote. |

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de Contagem de Pessoas](spatial-analysis-web-app.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e de linha](spatial-analysis-zone-line-placement.md)
