---
title: Enviar trabalhos de recorte do Azure Media Clipper | Microsoft Docs
description: Etapas para enviar trabalhos de recorte do Azure Media Clipper
services: media-services
keywords: clipe;subclipe;codificação;mídia
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f0dc6879ccbb22dbebd57de98e4610cd593318db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61242830"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Enviar trabalhos de recorte do Azure Media Clipper 

O Azure Media Clipper exige que um método **submitSubclipCallback** seja implementado para manipular o envio de trabalhos de recorte. Essa função serve para implementar um HTTP POST da saída do Clipper para um serviço Web. Esse serviço Web é o local em que você pode enviar o trabalho de codificação. A saída do Clipper é uma predefinição de codificação do Media Encoder Standard dos trabalhos renderizados ou o conteúdo da API REST para chamadas de filtro de manifesto dinâmico. Esse modelo de passagem é necessário porque as credenciais da conta dos serviços de mídia não são seguras no navegador do cliente.

O seguinte diagrama de sequência ilustra o fluxo de trabalho entre o cliente do navegador, o seu serviço Web e os Serviços de Mídia do Azure: ![Diagrama de sequência do Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

No diagrama anterior, as quatro entidades são: o navegador do usuário final, o seu serviço Web, o ponto de extremidade CDN hospedando os recursos Clipper e os Serviços de Mídia do Azure. Quando o usuário final navega para a sua página da Web, a página obtém os recursos do Clipper JavaScript e do CSS no ponto de extremidade CDN de hospedagem. O usuário final configura o trabalho de recorte ou a chamada de criação de filtro de manifesto dinâmico em seu navegador. Quando o usuário final envia a chamada de criação de trabalho ou filtro, o navegador coloca a carga de trabalho em um serviço da Web que você deve implantar. Esse serviço da Web, por fim, envia o trabalho de recorte ou a ligação de criação de filtro para o Serviço de Mídia do Azure usando suas credenciais de conta dos serviços de mídia.

O exemplo de código a seguir ilustra um método **submitSubclipCallback** de exemplo. Nesse método, você implementa o HTTP POST da predefinição de codificação do Media Encoder Standard. Se o POST foi bem-sucedido (**resultado**), a **Promessa** será resolvida; caso contrário, ela será rejeitada com detalhes do erro.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
A saída do envio do trabalho é a predefinição de codificação do Media Encoder Standard para o trabalho renderizado ou a carga de API REST para filtros de manifesto dinâmico.

## <a name="submitting-encoding-job-to-create-video"></a>Enviar trabalho de codificação para criar um vídeo
Você pode enviar um trabalho de codificação do Media Encoder Standard para criar um clipe de vídeo com precisão de quadro. Um trabalho de codificação produz vídeos renderizados, um novo arquivo MP4 fragmentado.

O contrato de saída do trabalho para um recorte renderizado é um objeto JSON com as seguintes propriedades:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Para executar o trabalho de codificação, envie o trabalho de codificação do Media Encoder Standard com a predefinição associada. Consulte este artigo para obter detalhes sobre como enviar codificação trabalhos usando o [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) ou [API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Criação rápida de clipes de vídeos sem codificação
Como alternativa para criar um trabalho de codificação, você pode usar o Azure Media Clipper para criar filtros de manifestos dinâmicos. Os filtros não exigem codificação e podem ser criados rapidamente já que um novo ativo não é criado. O contrato de saída para um recorte de filtro é um objeto JSON com as seguintes propriedades:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Para enviar a chamada REST para criar o filtro de manifesto dinâmico, envie a carga de filtro associada usando o [API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
