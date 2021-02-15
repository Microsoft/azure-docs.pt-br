---
title: Atualizando a análise de vídeo ao vivo no IoT Edge de 1,0 para 2,0
description: Este artigo aborda as diferenças e as diferentes questões a serem consideradas ao atualizar a LVA (análise de vídeo ao vivo) no módulo Azure IoT Edge.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955642"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Atualizando a análise de vídeo ao vivo no IoT Edge de 1,0 para 2,0

Este artigo aborda as diferenças e as diferentes questões a serem consideradas ao atualizar a LVA (análise de vídeo ao vivo) no módulo Azure IoT Edge.

## <a name="change-list"></a>Lista de alterações

> [!div class="mx-tdCol4BreakAll"]
> |Title|Análise de vídeo ao vivo 1,0|Análise de vídeo ao vivo 2,0|Descrição|
> |-------------|----------|---------|---------|
> |Imagem de contêiner|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Imagens do Docker publicadas pela Microsoft para análise de vídeo ao vivo no Azure IoT Edge|
> |**Nós MediaGraph** |    |   |   |
> |Origens|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fonte RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Origem da mensagem do Hub IoT |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fonte RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Origem da mensagem do Hub IoT | Nós MediaGraph que atuam como fontes de ingestão de mídia e mensagens.|
> |Processadores|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de detecção de movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de filtro de taxa de quadros </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador da porta do sinal |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de detecção de movimento </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Processador de filtro de taxa de quadros**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador de extensão Grpc </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processador da porta do sinal | Nós MediaGraph que permitem Formatar a mídia antes de enviar para os servidores de inferência de AI.|
> |Coletores|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Coletor de ativos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Coletor de arquivos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Coletor de mensagens do Hub IoT|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Coletor de ativos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Coletor de arquivos </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Coletor de mensagens do Hub IoT| Nós MediaGraph que permitem armazenar a mídia processada.|
> |**MediaGraphs com suporte** |    |   |   |
> |Topologias|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação de vídeo contínua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de movimento e gravação contínua de vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise externa e gravação contínua de vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em evento em movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em evento no ia</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em evento em evento externo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de movimento seguida por inferência de ia |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação de vídeo contínua </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de movimento e gravação contínua de vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise externa e gravação contínua de vídeo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em evento em movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em evento no ia</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Gravação baseada em evento em evento externo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de movimento </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Análise de movimento seguida por inferência de ia </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Composição de ia** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Gravação de áudio e vídeo** </br>  | Topologias MediaGraph que permitem definir o plano gráfico de um grafo, com parâmetros como espaços reservados para valores.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Atualizando a análise de vídeo ao vivo no módulo IoT Edge de 1,0 para 2,0
Ao atualizar a análise de vídeo ao vivo no módulo IoT Edge, certifique-se de atualizar as informações a seguir.
### <a name="container-image"></a>Imagem de contêiner
Em seu modelo de implantação, procure sua análise de vídeo ao vivo no módulo IoT Edge no `modules` nó e atualize o `image` campo como:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> Se você não tiver modificado o nome da análise de vídeo ao vivo no módulo IoT Edge, procure `lvaEdge` no nó do módulo.

### <a name="topology-file-changes"></a>Alterações no arquivo de topologia
Em seus arquivos de topologia, verifique se **`apiVersion`** está definido como 2,0

### <a name="mediagraph-node-changes"></a>Alterações de nó MediaGraph


* O áudio da sua fonte de câmera agora pode ser passado downstream junto com o vídeo. Você pode transmitir **somente áudio** ou **somente vídeo** ou **áudio e vídeo** com a ajuda de **`outputSelectors`** para qualquer nó do grafo. Por exemplo, se você quiser selecionar vídeo somente da fonte RTSP e passá-lo downstream, adicione o seguinte ao nó de origem RTSP:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>O **`outputSelectors`** é uma propriedade opcional. Se isso não for usado, o grafo de mídia passará o áudio (se habilitado) e o vídeo do downstream da câmera RTSP. 

* Em `MediaGraphHttpExtension` `MediaGraphGrpcExtension` processadores e, observe as seguintes alterações:  
    #### <a name="image-properties"></a>Propriedades da imagem
    * `MediaGraphImageFormatEncoded` não é mais suportada. 
      * Em vez disso, use **`MediaGraphImageFormatBmp`** ou **`MediaGraphImageFormatJpeg`** ou **`MediaGraphImageFormatPng`** . Por exemplo,
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Se você quiser usar imagens BRUTAs, use **`MediaGraphImageFormatRaw`** . Para usar isso, atualize o nó da imagem como:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Os valores possíveis de pixelFormat incluem:,,,,, `yuv420p` `rgb565be` `rgb565le` `rgb555be` `rgb555le` `rgb24` , `bgr24` ,, `argb` `rgba` , `abgr` , `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>extensionConfiguration para processador de extensão Grpc  
    * No `MediaGraphGrpcExtension` processador, uma nova propriedade chamada **`extensionConfiguration`** está disponível, que é uma cadeia de caracteres opcional que pode ser usada como parte do contrato gRPC. Esse campo pode ser usado para passar quaisquer dados para o servidor de inferência e você pode definir como o servidor de inferência usa esses dados.  
    Um caso de uso dessa propriedade é quando você tem vários modelos de ia empacotados em um único servidor de inferência. Com essa propriedade, você não precisará expor um nó para todos os modelos de ia. Em vez disso, para uma instância de grafo, como um provedor de extensão, você pode definir como selecionar os diferentes modelos de ia usando a **`extensionConfiguration`** propriedade e durante a execução, LVA passará essa cadeia de caracteres para o servidor inferência, que pode usar isso para invocar o modelo de ia desejado.  

    #### <a name="ai-composition"></a>Composição de ia
    * A análise de vídeo ao vivo 2,0 agora dá suporte ao uso de mais de um processador de extensão de grafo de mídia em uma topologia. Você pode passar os quadros de mídia da câmera RTSP para modelos de ia diferentes seqüencialmente, em paralelo ou em uma combinação de ambos. Consulte uma topologia de exemplo mostrando dois modelos de ia que estão sendo usados sequencialmente.

### <a name="disk-space-management-with-sink-nodes"></a>Gerenciamento de espaço em disco com nós do coletor
* No nó do **coletor de arquivos** , agora você pode especificar quanto espaço em disco a análise de vídeo ao vivo no módulo IOT Edge pode usar para armazenar as imagens processadas. Para fazer isso, adicione o **`maximumSizeMiB`** campo ao nó filesink. Um exemplo de nó de coletor de arquivo é o seguinte:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* No nó do **coletor de ativos** , você pode especificar quanto espaço em disco a análise de vídeo ao vivo no módulo IOT Edge pode usar para armazenar as imagens processadas. Para fazer isso, adicione o **`localMediaCacheMaximumSizeMiB`** campo ao nó do coletor de ativos. Um exemplo de nó de coletor de ativos é o seguinte:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  O caminho do **coletor de arquivo** é dividido no caminho do diretório base e no padrão de nome de arquivo, enquanto o caminho do **coletor de ativos** inclui o caminho do diretório base.  

### <a name="frame-rate-management"></a>Gerenciamento de taxa de quadros
* **`MediaGraphFrameRateFilterProcessor`** é preterido na **análise de vídeo ao vivo no módulo IoT Edge 2,0** .
    * Para obter amostras do vídeo de entrada para processamento, adicione a **`samplingOptions`** Propriedade aos processadores de extensão MediaGraph ( `MediaGraphHttpExtension` ou `MediaGraphGrpcExtension` )  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Métricas de módulo no formato Prometheus usando Telegraf
Com esta versão, Telegraf pode ser usado para enviar métricas para Azure Monitor. A partir daí, as métricas podem ser direcionadas para Log Analytics ou um hub de eventos.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Taxonomia de eventos":::

Você pode produzir uma imagem Telegraf com uma configuração personalizada facilmente usando o Docker. Saiba mais na página [monitoramento e registro em log](monitoring-logging.md#azure-monitor-collection-via-telegraf) .

## <a name="next-steps"></a>Próximas etapas

[Introdução à análise de vídeo ao vivo no IoT Edge](get-started-detect-motion-emit-events-quickstart.md)