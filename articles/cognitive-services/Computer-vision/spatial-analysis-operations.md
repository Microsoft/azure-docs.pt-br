---
title: Operações de análise espacial
titleSuffix: Azure Cognitive Services
description: As operações de análise espacial.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: af220106c415165a0dbe7cda64a31a6068f53164
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981775"
---
# <a name="spatial-analysis-operations"></a>Operações de análise espacial

A análise espacial permite a análise do vídeo de streaming em tempo real de dispositivos com câmera. Para cada dispositivo com câmera configurado, as operações para análise espacial gerarão um fluxo de saída de mensagens JSON enviadas à sua instância do Hub IoT do Azure. 

O contêiner análise espacial implementa as seguintes operações:

| Identificador da operação| Description|
|---------|---------|
| cognitivaservices. Vision. spatialanalysis-personcount | Conta as pessoas em uma zona designada no campo de exibição da câmera. A zona deve ser totalmente coberta por uma única câmera para que o PersonCount registre um total preciso. <br> Emite um evento _personCountEvent_ inicial e, em seguida, _personCountEvent_ eventos quando a contagem é alterada.  |
| cognitivaservices. Vision. spatialanalysis-personcrossingline | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitivaservices. Vision. spatialanalysis-personcrossingpolygon | Emite um evento _personZoneEnterExitEvent_ quando uma pessoa entra ou sai da zona e fornece informações direcionais com o lado numerado da zona que foi cruzada. Emite um _personZoneDwellTimeEvent_ quando a pessoa sai da zona e fornece informações direcionais, bem como o número de milissegundos que a pessoa gastou dentro da zona. |
| cognitivaservices. Vision. spatialanalysis-persondistance | Rastreia quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |

Todas as operações acima também estão disponíveis na `.debug` versão, que têm a capacidade de visualizar os quadros de vídeo conforme eles estão sendo processados. Você precisará executar o `xhost +` no computador host para habilitar a visualização de quadros de vídeo e eventos.

| Identificador da operação| Description|
|---------|---------|
| cognitivaservices. Vision. spatialanalysis-personcount. Debug | Conta as pessoas em uma zona designada no campo de exibição da câmera. <br> Emite um evento _personCountEvent_ inicial e, em seguida, _personCountEvent_ eventos quando a contagem é alterada.  |
| cognitivaservices. Vision. spatialanalysis-personcrossingline. Debug | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitivaservices. Vision. spatialanalysis-personcrossingpolygon. Debug | Emite um evento _personZoneEnterExitEvent_ quando uma pessoa entra ou sai da zona e fornece informações direcionais com o lado numerado da zona que foi cruzada. Emite um _personZoneDwellTimeEvent_ quando a pessoa sai da zona e fornece informações direcionais, bem como o número de milissegundos que a pessoa gastou dentro da zona. |
| cognitivaservices. Vision. spatialanalysis-persondistance. Debug | Rastreia quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |

A análise espacial também pode ser executada com a [análise de vídeo ao vivo](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) como seu módulo de ia de vídeo. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identificador da operação| Description|
|---------|---------|
| cognitivaservices. Vision. spatialanalysis-personcount. livevideoanalytics | Conta as pessoas em uma zona designada no campo de exibição da câmera. <br> Emite um evento _personCountEvent_ inicial e, em seguida, _personCountEvent_ eventos quando a contagem é alterada.  |
| cognitivaservices. Vision. spatialanalysis-personcrossingline. livevideoanalytics | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitivaservices. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics | Emite um evento _personZoneEnterExitEvent_ quando uma pessoa entra ou sai da zona e fornece informações direcionais com o lado numerado da zona que foi cruzada. Emite um _personZoneDwellTimeEvent_ quando a pessoa sai da zona e fornece informações direcionais, bem como o número de milissegundos que a pessoa gastou dentro da zona.  |
| cognitivaservices. Vision. spatialanalysis-persondistance. livevideoanalytics | Rastreia quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |

As operações de análise de vídeo ao vivo também estão disponíveis na `.debug` versão (por exemplo, cognitivaservices. Vision. spatialanalysis-personcount. livevideoanalytics. Debug) que tem a capacidade de visualizar os quadros de vídeo como sendo processados. Você precisará executar `xhost +` no computador host para habilitar a visualização dos quadros e eventos de vídeo

> [!IMPORTANT]
> Os modelos de ia da pesquisa Visual computacional detectam e localizam a presença humana na seqüência de vídeo e na saída usando uma caixa delimitadora em um corpo humano. Os modelos de ia não tentam descobrir as identidades ou os dados demográficos de indivíduos.

Esses são os parâmetros necessários para cada uma dessas operações de análise espacial.

| Parâmetros de operação| Description|
|---------|---------|
| ID da Operação | O identificador da operação da tabela acima.|
| Habilitado | Booliano: verdadeiro ou falso|
| VIDEO_URL| A URL RTSP para o dispositivo de câmera (exemplo: `rtsp://username:password@url` ). A análise espacial dá suporte ao fluxo codificado H. 264 por meio de RTSP, http ou MP4. Video_URL pode ser fornecido como um valor de cadeia de caracteres Base64 ofuscado usando a criptografia AES, e se a URL de vídeo for ofuscada `KEY_ENV` e `IV_ENV` precisar ser fornecida como variáveis de ambiente. O utilitário de exemplo para gerar chaves e criptografia pode ser encontrado [aqui](/dotnet/api/system.security.cryptography.aesmanaged). |
| VIDEO_SOURCE_ID | Um nome amigável para o dispositivo de câmera ou fluxo de vídeo. Isso será retornado com a saída do evento JSON.|
| VIDEO_IS_LIVE| Verdadeiro para dispositivos de câmera; false para vídeos gravados.|
| VIDEO_DECODE_GPU_INDEX| Qual GPU decodificará o quadro de vídeo. Por padrão, é 0. Deve ser o mesmo que o `gpu_index` em outras configurações de nó como `VICA_NODE_CONFIG` , `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Largura do quadro do vídeo/fluxo de entrada (por exemplo, 1920). Seu campo opcional e se o quadro fornecido será dimensionado para essa dimensão, mas ainda preservará a taxa de proporção.|
| DETECTOR_NODE_CONFIG | JSON que indica em qual GPU executar o nó do detector. Deve estar no seguinte formato: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Configuração de JSON para a zona e a linha, conforme descrito abaixo.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` para habilitar a detecção de pessoas que precisam de máscaras de face no fluxo de vídeo, `False` para desabilitá-las. Por padrão, isso é desabilitado. A detecção de máscara facial exige que o parâmetro de largura de vídeo de entrada seja 1920 `"INPUT_VIDEO_WIDTH": 1920` . O atributo de máscara facial não será retornado se as pessoas detectadas não estiverem voltadas para a câmera ou estiverem muito longe dela. Consulte o guia de [posicionamento da câmera](spatial-analysis-camera-placement.md) para obter mais informações |

Este é um exemplo de parâmetros de DETECTOR_NODE_CONFIG para todas as operações de análise espacial.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sampling_num": 80,
"calibration_quality_check_sampling_times": 5,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000,
"recalibration_score": 75
}
```

| Nome | Type| Description|
|---------|---------|---------|
| `gpu_index` | string| O índice de GPU no qual esta operação será executada.|
| `do_calibration` | string | Indica que a calibragem está ativada. `do_calibration` deve ser verdadeiro para **cognitivaservices. Vision. spatialanalysis-persondistance** para funcionar corretamente. do_calibration é definido por padrão como true. |
| `enable_recalibration` | bool | Indica se a recalibração automática está ativada. O padrão é `true`.|
| `calibration_quality_check_frequency_seconds` | INT | Número mínimo de segundos entre cada verificação de qualidade para determinar se a recalibração é necessária ou não. O padrão é `86400` (24 horas). Usado somente quando `enable_recalibration=True` .|
| `calibration_quality_check_sampling_num` | INT | Número de amostras de dados armazenados aleatoriamente selecionadas a serem usadas por medição de erro de verificação de qualidade. O padrão é `80`. Usado somente quando `enable_recalibration=True` .|
| `calibration_quality_check_sampling_times` | INT | Número de vezes que as medições de erro serão executadas em diferentes conjuntos de amostras de dados selecionadas aleatoriamente por verificação de qualidade. O padrão é `5`. Usado somente quando `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | INT | Número mínimo de segundos entre coletar novos exemplos de dados para recalibragem e verificação de qualidade. O padrão é `300` (5 minutos). Usado somente quando `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | INT | Número mínimo de novos exemplos de dados a serem coletados por rodada de amostra de coleção. O padrão é `10`. Usado somente quando `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | INT | Número máximo de amostras de dados a serem armazenadas quando o modelo de câmera é calibrado. O padrão é `1000`. Usado somente quando `enable_recalibration=True` .|
| `recalibration_score` | INT | Limite de qualidade máximo para iniciar a recalibração. O padrão é `75`. Usado somente quando `enable_recalibration=True` . A qualidade da calibragem é calculada com base em uma relação inversa com erro de Reprojeção de destino de imagem. Determinados destinos detectados em quadros de imagem 2D, os destinos são projetados em espaço 3D e reformulados de volta para o quadro de imagem 2D usando parâmetros de calibragem de câmera existentes. O erro de Reprojeção é medido pela média de distâncias entre os destinos detectados e os destinos reprojetados.|
| `enable_breakpad`| bool | Indica se você deseja habilitar Breakpad, que é usado para gerar despejo de memória para uso de depuração. Ele é `false` por padrão. Se você defini-lo como `true` , também precisará adicionar `"CapAdd": ["SYS_PTRACE"]` na `HostConfig` parte do contêiner `createOptions` . Por padrão, o despejo de memória é carregado no aplicativo [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter, se você quiser que os despejos de memória sejam carregados em seu próprio aplicativo AppCenter, poderá substituir a variável de ambiente `RTPT_APPCENTER_APP_SECRET` pelo segredo do aplicativo do aplicativo.


### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Configuração de zona para cognitivaservices. Vision. spatialanalysis-personcount

 Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Você pode configurar várias zonas para esta operação.

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

| Nome | Type| Description|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | list| Cada par de valor representa o x, y para vértices de um polígono. O polígono representa as áreas nas quais as pessoas são controladas ou contadas e os pontos do polígono são baseados em coordenadas normalizadas (0-1), em que o canto superior esquerdo é (0,0, 0,0) e o canto inferior direito é (1,0, 1,0).   
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-personcount** deve ser `count` .|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores com suporte são `event` para enviar eventos quando a contagem `interval` é alterada ou para enviar eventos periodicamente, independentemente de a contagem ter sido alterada ou não.
| `output_frequency` | INT | A taxa em que os eventos são reegressos. Quando `output_frequency` = x, cada evento X é egresso, por exemplo, `output_frequency` = 2 significa que todos os outros eventos são gerados. O `output_frequency` é aplicável ao `event` e ao `interval` . |
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usada para calcular eventos. O valor do foco pode ser `footprint` (a superfície da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Configuração de linha para cognitivaservices. Vision. spatialanalysis-personcrossingline

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma linha. Você pode configurar várias linhas de cruzamento para esta operação.

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

| Nome | Type| Description|
|---------|---------|---------|
| `lines` | list| Lista de linhas.|
| `name` | string| Nome amigável para esta linha.|
| `line` | list| A definição da linha. Essa é uma linha direcional que permite que você entenda "entry" vs. "Exit".|
| `start` | par de valor| coordenadas x, y para o ponto inicial da linha. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. |
| `end` | par de valor| coordenadas x, y para o ponto final da linha. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. |
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. O valor padrão é 16. Esse é o valor recomendado para obter precisão máxima. |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-personcrossingline** deve ser `linecrossing` .|
|`trigger`|string|O tipo de gatilho para enviar um evento.<br>Valores com suporte: "Event": Fire quando alguém cruza a linha.|
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usada para calcular eventos. O valor do foco pode ser `footprint` (a superfície da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa). O valor padrão é superfície.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Configuração de zona para cognitivaservices. Vision. spatialanalysis-personcrossingpolygon

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Você pode configurar várias zonas para esta operação.

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

| Nome | Type| Description|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | list| Cada par de valor representa o x, y para vértices de polígono. O polígono representa as áreas nas quais as pessoas são rastreadas ou contadas. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. 
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. O valor padrão é 48 quando o tipo é zonecrossing e 16 quando time é acessetime. Esses são os valores recomendados para obter precisão máxima.  |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-personcrossingpolygon,** isso deve ser `zonecrossing` ou `zonedwelltime` .|
| `trigger`|string|O tipo de gatilho para enviar um evento<br>Valores com suporte: "Event": Fire quando alguém entra ou sai da zona.|
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usada para calcular eventos. O valor do foco pode ser `footprint` (a superfície da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa). O valor padrão é superfície.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Configuração de zona para cognitivaservices. Vision. spatialanalysis-persondistance

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona para **cognitivaservices. Vision. spatialanalysis-persondistance**. Você pode configurar várias zonas para esta operação.

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
           "threshold": 16.00,
           "focus": "footprint"
            }
    }]
   }]
}
```

| Nome | Type| Description|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | list| Cada par de valor representa o x, y para vértices de polígono. O polígono representa as áreas nas quais as pessoas são contadas e a distância entre as pessoas é medida. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. 
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-persondistance** deve ser `people_distance` .|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores com suporte são `event` para enviar eventos quando a contagem `interval` é alterada ou para enviar eventos periodicamente, independentemente de a contagem ter sido alterada ou não.
| `output_frequency` | INT | A taxa em que os eventos são reegressos. Quando `output_frequency` = x, cada evento X é egresso, por exemplo, `output_frequency` = 2 significa que todos os outros eventos são gerados. O `output_frequency` é aplicável ao `event` e ao `interval` .|
| `minimum_distance_threshold` | FLOAT| Uma distância em pés que disparará um evento "TooClose" quando as pessoas forem menores do que essa distância.|
| `maximum_distance_threshold` | FLOAT| Uma distância em pés que irá disparar um evento "TooFar" quando as pessoas forem maiores que essa distância.|
| `focus` | string| A localização do ponto na caixa delimitadora da pessoa usada para calcular eventos. O valor do foco pode ser `footprint` (a superfície da pessoa), `bottom_center` (a parte inferior central da caixa delimitadora da pessoa), `center` (o centro da caixa delimitadora da pessoa).|

Consulte as diretrizes de [posicionamento da câmera](spatial-analysis-camera-placement.md) para saber mais sobre as configurações de zona e linha.

## <a name="spatial-analysis-operation-output"></a>Saída da operação de análise espacial

Os eventos de cada operação são saída para o Hub IoT do Azure no formato JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-personcount ia insights

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
                "face_Mask": 0.99
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
                "face_noMask": 0.99
            }
            }
    }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo de evento | Type| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `zone` | string | O campo "Name" do polígono que representa a zona que foi cruzada|
| `trigger` | string| O tipo de gatilho é ' Event ' ou ' interval ' dependendo do valor de `trigger` no SPACEANALYTICS_CONFIG|

| Nome do campo de detecções | Type| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `face_Mask` | FLOAT | O valor de confiança do atributo com intervalo (0-1) indica que a pessoa detectada está usando uma máscara de face |
| `face_noMask` | FLOAT | O valor de confiança do atributo com intervalo (0-1) indica que a pessoa detectada **não** está desgastando uma máscara de face |

| Nome do campo SourceInfo | Type| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|
| `cameraCallibrationInfo` | collection | Coleção de valores|
| `status` | string | O status da calibragem no formato de `state[;progress description]` . O estado pode ser `Calibrating` , `Recalibrating` (se a recalibração estiver habilitada) ou `Calibrated` . A parte de descrição do progresso só é válida quando está `Calibrating` no `Recalibrating` estado e, que é usada para mostrar o progresso do processo de calibragem atual.|
| `cameraHeight` | FLOAT | A altura da câmera acima do início em pés. Isso é inferido da calibragem automática. |
| `focalLength` | FLOAT | O comprimento focal da câmera em pixels. Isso é inferido da calibragem automática. |
| `tiltUpAngle` | FLOAT | O ângulo de inclinação da câmera da vertical. Isso é inferido da calibragem automática.|

| Nome do campo SourceInfo | Type| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-personcrossingline ia insights

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
                "face_Mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nome do campo de evento | Type| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `status` | string| Direção de cruzamentos de linha, ' CrossLeft ' ou ' CrossRight '|
| `zone` | string | O campo "nome" da linha que foi cruzada|

| Nome do campo de detecções | Type| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `face_Mask` | FLOAT | O valor de confiança do atributo com intervalo (0-1) indica que a pessoa detectada está usando uma máscara de face |
| `face_noMask` | FLOAT | O valor de confiança do atributo com intervalo (0-1) indica que a pessoa detectada **não** está desgastando uma máscara de face |

| Nome do campo SourceInfo | Type| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|


> [!IMPORTANT]
> O modelo de ia detecta uma pessoa independentemente de se a pessoa está voltada para longe ou afastada da câmera. O modelo de ia não executa o reconhecimento facial e não emite nenhuma informação biométrica. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-personcrossingpolygon ia insights

Exemplo de JSON para a saída de detecções por essa operação com o `zonecrossing` tipo SPACEANALYTICS_CONFIG.

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
        "face_Mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

Exemplo de JSON para a saída de detecções por essa operação com o `zonedwelltime` tipo SPACEANALYTICS_CONFIG.

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

| Nome do campo de evento | Type| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento. O valor pode ser _personZoneDwellTimeEvent_ ou _personZoneEnterExitEvent_|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `status` | string| Direção de cruzamentos de polígono, ' Enter ' ou ' Exit '|
| `side` | INT| O número do lado do polígono que a pessoa cruzou. Cada lado é uma borda numerada entre os dois vértices do polígono que representa a zona. A borda entre os dois primeiros vértices do polígono representa o primeiro lado|
| `durationMs` | FLOAT | O número de milissegundos que representam o tempo que a pessoa gastou na zona. Este campo é fornecido quando o tipo de evento é _personZoneDwellTimeEvent_|
| `zone` | string | O campo "Name" do polígono que representa a zona que foi cruzada|

| Nome do campo de detecções | Type| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `face_Mask` | FLOAT | O valor de confiança do atributo com intervalo (0-1) indica que a pessoa detectada está usando uma máscara de face |
| `face_noMask` | FLOAT | O valor de confiança do atributo com intervalo (0-1) indica que a pessoa detectada **não** está desgastando uma máscara de face |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-persondistance ia insights

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

| Nome do campo de evento | Type| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | array| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `personCount` | INT| Número de pessoas detectadas quando o evento foi emitido|
| `averageDistance` | FLOAT| A distância média entre todas as pessoas detectadas em pés|
| `minimumDistanceThreshold` | FLOAT| A distância, em pés, que disparará um evento "TooClose" quando as pessoas forem menores do que essa distância.|
| `maximumDistanceThreshold` | FLOAT| A distância, em pés, que disparará um evento "TooFar" quando as pessoas forem maiores do que distância.|
| `eventName` | string| `TooClose`O nome do evento está `minimumDistanceThreshold` violado, `TooFar` quando `maximumDistanceThreshold` é violado ou `unknown` quando a calibragem automática não foi concluída|
| `distanceViolationPersonCount` | INT| Número de pessoas detectadas na violação `minimumDistanceThreshold` ou `maximumDistanceThreshold`|
| `zone` | string | O campo "Name" do polígono que representa a zona que foi monitorada para distancing entre as pessoas|
| `trigger` | string| O tipo de gatilho é ' Event ' ou ' interval ' dependendo do valor de `trigger` no SPACEANALYTICS_CONFIG|

| Nome do campo de detecções | Type| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `centerGroundPoint` | 2 valores float| `x`, os `y` valores com as coordenadas do local inferido da pessoa no chão em pés. `x` e `y` são coordenadas no plano de chão, supondo que o piso seja o nível. O local da câmera é a origem. |

Ao calcular `centerGroundPoint` , `x` é a distância da câmera para a pessoa ao longo de uma linha perpendicular ao plano de imagem da câmera. `y` é a distância da câmera para a pessoa ao longo de uma linha paralela ao plano de imagem da câmera. 

![Exemplo de ponto de aterramento central](./media/spatial-analysis/x-y-chart.png) 

Neste exemplo, `centerGroundPoint` é `{x: 4, y: 5}`. Isso significa que há uma pessoa 4 pés de distância da câmera e 5 pés à direita, observando a sala de cima para baixo.


| Nome do campo SourceInfo | Type| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|
| `cameraCallibrationInfo` | collection | Coleção de valores|
| `status` | string | O status da calibragem no formato de `state[;progress description]` . O estado pode ser `Calibrating` , `Recalibrating` (se a recalibração estiver habilitada) ou `Calibrated` . A parte de descrição do progresso só é válida quando está `Calibrating` no `Recalibrating` estado e, que é usada para mostrar o progresso do processo de calibragem atual.|
| `cameraHeight` | FLOAT | A altura da câmera acima do início em pés. Isso é inferido da calibragem automática. |
| `focalLength` | FLOAT | O comprimento focal da câmera em pixels. Isso é inferido da calibragem automática. |
| `tiltUpAngle` | FLOAT | O ângulo de inclinação da câmera da vertical. Isso é inferido da calibragem automática.|


## <a name="use-the-output-generated-by-the-container"></a>Usar a saída gerada pelo contêiner

Você pode desejar integrar a detecção de análise espacial ou eventos em seu aplicativo. Aqui estão algumas abordagens a serem consideradas: 

* Use o SDK do hub de eventos do Azure para a linguagem de programação escolhida para se conectar ao ponto de extremidade do Hub IoT do Azure e receber os eventos. Consulte [ler mensagens do dispositivo para a nuvem do ponto de extremidade interno](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) para obter mais informações. 
* Configure o **Roteamento de mensagens** no Hub IOT do Azure para enviar os eventos para outros pontos de extremidade ou salve os eventos no armazenamento de dados. Consulte [Roteamento de mensagens do Hub IOT](../../iot-hub/iot-hub-devguide-messages-d2c.md) para obter mais informações. 
* Configure um trabalho de Azure Stream Analytics para processar os eventos em tempo real à medida que eles chegam e criar visualizações. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Implantando operações de análise espacial em escala (várias câmeras)

Para obter o melhor desempenho e utilização das GPUs, você pode implantar qualquer operação de análise espacial em várias câmeras usando instâncias de grafo. Abaixo está um exemplo para executar a `cognitiveservices.vision.spatialanalysis-personcrossingline` operação em quinze câmeras.

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
| Nome | Type| Description|
|---------|---------|---------|
| `batch_size` | INT | Se todas as câmeras tiverem a mesma resolução, defina `batch_size` como o número de câmeras que serão usadas na operação, caso contrário, defina `batch_size` como 1 ou deixe como padrão (1), o que indica que não há suporte para nenhum lote. |

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e linha](spatial-analysis-zone-line-placement.md)
