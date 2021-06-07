---
title: Examinar a saída de Video Indexer produzida pela API v2 – Azure
titleSuffix: Azure Media Services
description: Este tópico examina os serviços de mídia do Azure Video Indexer saída produzida pela API v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 2ac7c3c2149ce43c860c7726381733ef377de8d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530732"
---
# <a name="examine-the-video-indexer-output"></a>Examinar a saída de Video Indexer

Quando um vídeo é indexado, Video Indexer produz o conteúdo JSON que contém detalhes das informações de vídeo especificadas. As informações incluem: transcrições, OCRs, rostos, tópicos, blocos, etc. Cada tipo de insight inclui instâncias de intervalos de tempo que mostram quando a Insight é exibida no vídeo. 

Você pode examinar visualmente as ideias resumidas do vídeo pressionando o botão **reproduzir** no vídeo no site [Video indexer](https://www.videoindexer.ai/) . 

Você também pode usar a API chamando a API **obter índice de vídeo** e o status de resposta é ok. você obtém uma saída JSON detalhada como o conteúdo da resposta.

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Este artigo examina a saída de Video Indexer (conteúdo JSON). <br/>Para obter informações sobre quais recursos e informações estão disponíveis para você, consulte [Video indexer insights](video-indexer-overview.md#video-insights).

> [!NOTE]
> Expiração de todos os tokens de acesso no indexador de vídeo é uma hora.

## <a name="get-the-insights"></a>Obtenha as informações

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Informações/saída produzidas no site/Portal

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
1. Localize um vídeo da saída que você deseja examinar.
1. Pressione **Reproduzir**.
1. Selecione a guia **insights** (informações resumidas) ou a guia **linha do tempo** (permite filtrar as informações relevantes).
1. Baixe artefatos e o que estão neles.

Para obter mais informações, consulte [insights de vídeo de exibir e editar](video-indexer-view-edit.md).

## <a name="insightsoutput-produced-by-api"></a>Informações/saída produzidas pela API

1. Para recuperar o arquivo JSON, chame [obter API de índice de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Se você também estiver interessado em artefatos específicos, chame [obter a API da URL de download do artefato de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Na chamada à API, especifique o tipo de artefato solicitado (OCR, rostos, quadros-chave, etc.)

## <a name="root-elements-of-the-insights"></a>Elementos raiz das informações

|Nome|Descrição|
|---|---|
|accountId|ID da conta VI da lista de reprodução.|
|id|ID. da lista de reprodução.|
|name|Nome da lista de reprodução.|
|descrição|Descrição da lista de reprodução.|
|userName|O nome do usuário que criou a lista de reprodução.|
|criado|Hora de criação da lista de reprodução.|
|privacyMode|Modo de privacidade da lista de reprodução (pública/privada).|
|state|A playlist (upload, processamento, processamento, falha, quarentena).|
|isOwned|Indica se a lista de reprodução foi criada pelo usuário atual.|
|isEditable|Indica se o usuário atual está autorizado a editar a lista de reprodução.|
|isBase|Indica se a lista de reprodução é uma lista de reprodução básica (um vídeo) ou uma lista de reprodução de outros vídeos (derivados).|
|durationInSeconds|A duração total da lista de reprodução.|
|summarizedInsights|Contém um [summarizedInsights](#summarizedinsights).
|Vídeos|Uma lista dos [vídeos](#videos) construir a lista de reprodução.<br/>Se esta lista de reprodução de intervalos de tempo de outros vídeos (derivados), os vídeos nesta lista conterão apenas dados dos intervalos de tempo incluídos.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

Esta seção mostra o resumo das informações.

|Atributo | Descrição|
|---|---|
|name|O nome do vídeo. Por exemplo, o Azure Monitor.|
|id|A ID do vídeo. Por exemplo, 63c6d532ff.|
|privacyMode|Seu detalhamento pode ter um dos seguintes modos: **Particular**, **Público**. **Público**: o vídeo é visível para todos na sua conta e para qualquer pessoa que tenha um link para o vídeo. **Privada** -o vídeo é visível para todos em sua conta.|
|duration|Contém uma duração que descreve o tempo que uma percepção ocorreu. Duração é em segundos.|
|thumbnailVideoId|A ID do vídeo da qual a miniatura foi tirada.
|thumbnailId|A ID da miniatura do vídeo. Para obter a miniatura real, chame [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passe-o thumbnailVideoId e thumbnailid.|
|rostos/animatedCharacters|Pode conter zero ou mais faces. Para obter informações mais detalhadas, consulte [faces/animatedCharacters](#facesanimatedcharacters).|
|palavras-chave|Pode conter zero ou mais palavras-chave. Para obter mais informações, consulte [palavras-chave](#keywords).|
|sentiments|Pode conter zero ou mais sentimentos. Para obter mais informações, consulte [sentimentos](#sentiments).|
|audioEffects| Pode conter zero ou mais audioEffects. Para informações mais detalhadas, consulte [audioEffects](#audioeffects-public-preview).|
|rótulos| Pode conter zero ou mais rótulos. Para obter mais informações, consulte [rótulos](#labels).|
|marcas| Pode conter zero ou mais marcas. Para informações mais detalhadas, consulte [marcas](#brands).|
|estatísticas | Para obter mais informações, consulte [estatísticas](#statistics).|
|emotions| Pode conter zero ou mais emoções. Para obter informações mais detalhadas, confira [emotions](#emotions).|
|topics|Pode conter zero ou mais tópicos. Os [Tópicos](#topics) insights.|

## <a name="videos"></a>Vídeos

|Nome|Descrição|
|---|---|
|accountId|A ID da conta VI do vídeo.|
|id|ID do vídeo.|
|name|Nome do vídeo.
|state|Estado do vídeo (carregado, processado, processado, com falha, em quarentena).|
|processingProgress|O progresso do processamento durante o processamento (por exemplo, 20%).|
|failureCode|O código de falha se não for processado (por exemplo, 'UnsupportedFileType').|
|failureMessage|A mensagem de falha se não for processada.|
|externalId|O ID externo do vídeo (se especificado pelo usuário).|
|externalUrl|O URL externo do vídeo (se especificado pelo usuário).|
|metadata|Os metadados externos do vídeo (se especificado pelo usuário).|
|isAdult|Indica se o vídeo foi revisado manualmente e identificado como um vídeo adulto.|
|Insights|O objeto de insights. Para obter mais informações, consulte [insights](#insights).|
|thumbnailId|A ID da miniatura do vídeo. Para obter a chamada de miniatura real [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passá-la para a ID de vídeo e thumbnailid.|
|publishedUrl|Uma URL para transmitir o vídeo.|
|publishedUrlProxy|Uma URL para transmitir o vídeo (para dispositivos Apple).|
|viewToken|Um símbolo de visualização de curta duração para transmitir o vídeo.|
|sourceLanguage|O idioma de origem do vídeo.|
|Linguagem|O idioma real do vídeo (tradução).|
|indexingPreset|A predefinição usada para indexar o vídeo.|
|streamingPreset|A predefinição usada para publicar o vídeo.|
|linguisticModelId|O modelo CRIS usado para transcrever o vídeo.|
|estatísticas | Para obter mais informações, consulte [estatísticas](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Insights

Cada Insight (por exemplo, linhas de transcrição, rostos, marcas etc.) contém uma lista de elementos exclusivos (por exemplo, face1, face2, face3), e cada elemento tem seus próprios metadados e uma lista de suas instâncias (que são intervalos de tempo com metadados opcionais adicionais).

Uma face pode ter uma ID, um nome, uma miniatura, outros metadados e uma lista de suas instâncias temporais (por exemplo: 00:00:05 – 00:00:10, 00:01:00-00:02:30 e 00:41:21 – 00:41:49). Cada instância temporal pode ter metadados adicionais. Por exemplo, o retângulo da face coordena (20,230,60,60).

|Versão|A versão do código|
|---|---|
|sourceLanguage|O idioma de origem do vídeo (assumindo um idioma mestre). Na forma de um [BCP-47](https://tools.ietf.org/html/bcp47) cadeia de caracteres.|
|Linguagem|A linguagem de insights (traduzida do idioma de origem). Na forma de um [BCP-47](https://tools.ietf.org/html/bcp47) cadeia de caracteres.|
|transcript|O insight da [transcrição](#transcript) .|
|ocr|A percepção do [OCR](#ocr) .|
|palavras-chave|As [palavras-chave](#keywords) Insight.|
|Blocos|Pode conter um ou mais [blocos](#blocks)|
|rostos/animatedCharacters|O [rostos/animatedCharacters](#facesanimatedcharacters) Insight.|
|rótulos|Os [Rótulos](#labels) se insights.|
|shots|O insights de [capturas](#shots) .|
|marcas|As [marcas](#brands) insights.|
|audioEffects|O [audioEffects](#audioeffects-public-preview) Insight.|
|sentiments|As [opiniões](#sentiments) insights.|
|visualContentModeration|O [visualContentModeration](#visualcontentmoderation) Insight.|
|textualContentModeration|O [textualContentModeration](#textualcontentmoderation) Insight.|
|emotions| As [emoções](#emotions) insights.|
|topics|Os [Tópicos](#topics) insights.|
|alto-falantes|Os [oradores](#speakers) insights.|

Exemplo:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>Blocos

Atributo | Descrição
---|---
id|ID do bloco.|
instances|Uma lista de intervalos de tempo deste bloco.|

#### <a name="transcript"></a>transcript

|Nome|Descrição|
|---|---|
|id|A ID da linha.|
|texto|A própria transcrição.|
|confidence|A confiança de precisão da transcrição.|
|palestraid|A ID do orador.|
|Linguagem|O idioma da transcrição. Tem o objetivo dar suporte à transcrição na qual cada linha pode ter um idioma diferente.|
|instances|Uma lista com os intervalos de tempo nos quais essa linha apareceu. Se a instância for transcrita, ela terá apenas 1 instância.|

Exemplo:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>ocr

|Nome|Descrição|
|---|---|
|id|A ID da linha de OCR.|
|texto|O texto de OCR.|
|confidence|A confiança do reconhecimento.|
|Linguagem|O idioma do OCR.|
|instances|Uma lista de intervalos de tempo nos quais essa OCR apareceu (o mesmo OCR pode aparecer várias vezes).|
|altura|A altura do retângulo OCR|
|top|O local principal em px|
|esquerda| O local à esquerda em px|
|width|A largura do retângulo OCR|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>palavras-chave

|Nome|Descrição|
|---|---|
|id|A ID da palavra-chave.|
|texto|O texto da palavra-chave.|
|confidence|A confiança do reconhecimento da palavra-chave.|
|Linguagem|O idioma da palavra-chave (quando traduzida).|
|instances|Uma lista de intervalos de tempo nos quais essa palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>rostos/animatedCharacters

`animatedCharacters` o elemento substitui `faces` caso o vídeo tenha sido indexado com um modelo de caracteres animados. Isso é feito usando um modelo personalizado no Visão Personalizada, Video Indexer executa-o em quadros-chave.

Se os rostos (não os caracteres animados) estiverem presentes, Video Indexer usará API de Detecção Facial em todos os quadros do vídeo para detectar rostos e celebridades.

|Nome|Descrição|
|---|---|
|id|A ID da face.|
|name|O nome da face. Pode ser “Desconhecido #0”, uma celebridade identificada ou uma pessoa treinada pelo cliente.|
|confidence|A confiança de identificação da face.|
|descrição|Uma descrição da celebridade. |
|thumbnailId|O ID da miniatura dessa face.|
|knownPersonId|Se é uma pessoa conhecida, o seu ID interno.|
|referenceId|Se for uma celebridade do Bing, o seu ID do Bing.|
|referenceType|No momento, apenas Bing.|
|título|Se é uma celebridade, seu título (por exemplo, "CEO da Microsoft").|
|imageUrl|Se é uma celebridade, o seu URL de imagem.|
|instances|Essas são as ocorrências do aparecimento da face no intervalo de tempo determinado. Cada ocorrência também tem uma thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>rótulos

|Nome|Descrição|
|---|---|
|id|A ID do rótulo.|
|name|O nome do rótulo (por exemplo, "Computador", "TV").|
|Linguagem|O idioma do nome do rótulo (quando traduzido). BCP-47|
|instances|Uma lista de intervalos de tempo nos quais esse rótulo apareceu (um rótulo pode aparecer várias vezes). Cada instância tem um campo de confiança. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>scenes

|Nome|Descrição|
|---|---|
|id|A ID da cena.|
|instances|Uma lista de intervalos de tempo desta cena (uma cena só pode ter 1 instância).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>shots

|Nome|Descrição|
|---|---|
|id|A ID da captura.|
|keyFrames|Uma lista de quadros-chave dentro da captura (cada um tem uma ID e uma lista de instâncias de intervalos de tempo). Cada instância de quadro-chave tem um campo de miniaturaid, que contém a ID de miniatura do quadro-chave.|
|instances|Uma lista de intervalos de tempo desta captura (uma captura pode ter apenas 1 instância).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>marcas

Nomes de marcas comerciais e de produtos detectados na fala para transcrição de texto e / ou Vídeo OCR. Isso não inclui reconhecimento visual de marcas ou detecção de logotipo.

|Nome|Descrição|
|---|---|
|id|A ID de marca.|
|name|O nome de marcas.|
|referenceId | O sufixo do URL da Wikipédia da marca. Por exemplo, "Target_Corporation" é o sufixo de [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | A marca da url da Wikipedia, se existir. Por exemplo, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|descrição|A descrição de marcas.|
|marcas|Uma lista de tags predefinidas associadas a essa marca.|
|confidence|O valor de confiança do detector de marca indexador de vídeo (0-1).|
|instances|Uma lista de intervalos de tempo desta marca. Cada instância tem um brandType, que indica se essa marca apareceu na transcrição ou no OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>estatísticas

|Nome|Descrição|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|SpeakerWordCount|O número de palavras por alto-falante.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o orador tem em um vídeo.|
|SpeakerLongestMonolog|O mais longo monólogo do orador. Se o falante tiver silêncios dentro do monólogo, ele será incluído. O silêncio no início e no final do monólogo é removido.| 
|SpeakerTalkToListenRatio|O cálculo é baseado no tempo gasto no monólogo do locutor (sem o silêncio intermediário) dividido pelo tempo total do vídeo. A hora é arredondada para o terceiro ponto decimal.|

#### <a name="audioeffects-public-preview"></a>audioEffects (visualização pública)

|Nome|Descrição
|---|---|
|id|A ID do efeito de áudio|
|type|O tipo de efeito de áudio|
|instances|Uma lista com os intervalos de tempo nos quais esse efeito de áudio apareceu. Cada instância tem um campo de confiança.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>sentiments

Os sentimentos são agregadas de acordo com seu campo sentimentType (Positivo/Neutro/Negativo). Por exemplo, 0-0.1, 0.1-0.2.

|Nome|Descrição|
|---|---|
|id|A ID do sentimento.|
|averageScore |A média de todas as pontuações de todas as instâncias desse tipo de sentimento - Neutral/positivo/negativo|
|instances|Uma lista com os intervalos de tempo nos quais esse sentimento apareceu.|
|sentimentType |O tipo pode ser 'Positivo', 'Neutro' ou 'Negativo'.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

O bloco visualContentModeration contém intervalos de tempo que o Video Indexer encontrou para potencialmente ter conteúdo adulto. Se visualContentModeration estiver vazio, não haverá conteúdo adulto identificado.

Os vídeos que contêm conteúdo adulto ou atraente podem estar disponíveis apenas para visualização privada. Os usuários têm a opção de enviar uma solicitação para uma revisão humana do conteúdo. Nesse caso, o atributo IsAdult conterá o resultado da revisão humana.

|Nome|Descrição|
|---|---|
|id|A ID de moderação de conteúdo visual.|
|adultScore|A pontuação de conteúdo adulta (do moderador de conteúdo).|
|racyScore|A pontuação racista (de moderação de conteúdo).|
|instances|Uma lista de intervalos de tempo em que apareceu esse visual moderação de conteúdo.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Nome|Descrição|
|---|---|
|id|O ID de moderação do conteúdo textual.|
|bannedWordsCount |O número de palavras proibidas.|
|bannedWordsRatio |A proporção do número total de palavras.|

#### <a name="emotions"></a>emotions

Video Indexer identifica emoções com base em indicações de fala e áudio. A emoção identificada poderia ser: Joy, tristeza, raiva ou medo.

|Nome|Descrição|
|---|---|
|id|A ID da emoção.|
|type|O momento de emoção que foi identificado com base nas indicações de fala e áudio. A emoção poderia ser: alegria, tristeza, raiva ou medo.|
|instances|Uma lista de intervalos de tempo nos quais essa emoção apareceu.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>topics

O Video Indexer faz inferências dos principais tópicos das transcrições. Quando possível, a taxonomia [IPTC](https://iptc.org/standards/media-topics/) de 2º nível é incluída. 

|Nome|Descrição|
|---|---|
|id|A ID do tópico.|
|name|O nome do tópico, por exemplo: "Produtos farmacêuticos".|
|referenceId|Trilhas refletindo a hierarquia de tópicos. Por exemplo: "Saúde e bem-estar/Medicina e atendimento à saúde /Produtos farmacêuticos".|
|confidence|A pontuação de confiança no intervalo [0,1]. Um valor mais alto indica maior confiança.|
|Linguagem|O idioma usado no tópico.|
|iptcName|O nome do código de mídia IPTC se detectado.|
|instances |Atualmente, o Video Indexer não indexa um tópico segundo intervalos de tempo, portanto, o vídeo inteiro é usado como o intervalo.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>alto-falantes

|Nome|Descrição|
|---|---|
|id|A ID do orador.|
|name|O nome do orador no formato "palestrante # *<number>* ", por exemplo: "viva-voz #1".|
|instances |Uma lista de intervalos de tempo onde este orador apareceu.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Próximas etapas

[Portal do Desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai)

Para obter informações sobre como incorporar widgets em seu aplicativo, consulte [Incorporar widgets do Video Indexer aos seus aplicativos](video-indexer-embed-widgets.md). 

