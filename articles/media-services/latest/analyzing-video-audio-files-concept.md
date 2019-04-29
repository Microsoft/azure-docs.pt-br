---
title: Analisando arquivos de vídeo e áudio com o Azure Media Services | Microsoft Docs
description: Ao usar os Serviços de Mídia do Azure, você pode analisar seu conteúdo de áudio e vídeo usando AudioAnalyzerPreset e VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: a4c643ecff5c33ec19c607da6ef8db41cfeb90c6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63762825"
---
# <a name="analyzing-video-and-audio-files"></a>Analisando os arquivos de áudio e vídeos

O Azure Media Services v3 permite que você extraia insights de seus arquivos de vídeo e áudio com o Video Indexer por meio das predefinições do analisador do AMS v3 (descritas neste artigo). Se você quiser informações mais detalhadas, use o Video Indexer diretamente. Para entender quando você desejaria usar o Video Indexer vs. as predefinições de análise dos Serviços de Mídia, confira o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Para analisar o conteúdo usando predefinições do Serviços de Mídia v3, você cria uma **Transformação** e envia um **Trabalho** que usa uma destas predefinições: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. O artigo a seguir demonstra como usar **VideoAnalyzerPreset**: [Tutorial: Analisar vídeos com os Serviços de Mídia do Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Ao usar as predefinições do Video ou Audio Analyzer, use o portal do Azure para definir sua conta como 10 Unidades Reservada para Mídia S3. Para saber mais, confira [Processamento de mídia de escala](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Predefinições internas

O Serviços de Mídia do Microsoft Azure atualmente suporta as seguintes predefinições do analisador interno:  

|**Nome da predefinição**|**Cenário**|**Detalhes**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analisar áudio|A predefinição aplica um conjunto predefinido de operações de análise baseadas em IA, incluindo transcrição de fala. Atualmente, a predefinição dá suporta ao processamento de conteúdo com uma única faixa do áudio que contenha fala em um único idioma. É possível especificar o idioma o conteúdo de áudio na entrada usando o formato BCP-47 de 'marca de idioma-região'. Os idiomas com suporte são inglês ('en-US' e 'en-GB'), espanhol ('es-ES' e 'es-MX'), francês ('fr-FR'), italiano ('it-IT'), japonês ('ja-JP'), português ('pt-BR'), chinês ('zh-CN'), alemão ('de-DE'), árabe ('ar-EG'), russo ('ru-RU'), Hindi ('hi-IN') e coreano ('ko-KR').<br/><br/> Se o idioma não for especificado ou definido como nulo, a detecção automática de idioma escolherá o primeiro idioma detectado e processará com o idioma selecionado para a duração do arquivo. O recurso de detecção automática de idioma suporta atualmente inglês, chinês, francês, alemão, italiano, japonês, espanhol, russo e português. Ele não suporta no momento alternar dinamicamente entre os idiomas depois que o primeiro idioma for detectado. O recurso de detecção automática de idioma funciona melhor com gravações de áudio com fala claramente discernível. Se a detecção automática de idioma não conseguir encontrar o idioma, a transcrição será revertida para inglês.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analisar áudio e vídeo|Extraia insights (metadados avançados) de áudio e vídeo e gere um arquivo no formato JSON. É possível especificar se deseja extrair apenas insights de áudio ao processar um arquivo de vídeo. Para obter mais informações, consulte [Analisar vídeo](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)||Descreve as configurações a serem usadas ao analisar um vídeo para detectar todas as faces presentes.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

A predefinição permite extrair vários insights de áudio de um arquivo de áudio ou vídeo. A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do áudio. Essa predefinição aceita uma propriedade que especifica o idioma do arquivo de entrada na forma de uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47). As informações de áudio incluem:

* Transcrição de áudio: uma transcrição das palavras faladas com carimbos de hora. Há suporte para vários idiomas
* Indexação de locutores: um mapeamento dos locutores e das palavras faladas correspondentes
* Análise de sentimento da fala: a saída da análise de sentimento executada na transcrição de áudio
* Palavras-chave: palavras-chave extraídas da transcrição de áudio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A predefinição permite extrair vários insights de áudio e vídeo de um arquivo de vídeo. A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do vídeo, e uma coleção de miniaturas. Essa predefinição também aceita uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47) (que representa o idioma do vídeo) como propriedade. As informações de vídeos incluem todas as informações de áudio mencionadas acima, e os itens adicionais a seguir:

* Controle de face: o tempo durante o qual há faces presentes no vídeo. Cada face tem uma id de face e um conjunto correspondente de miniaturas
* Texto visual: o texto detectado por meio de reconhecimento óptico de caracteres. O texto recebe um carimbo de data/hora e também é usado para extrair as palavras-chave (além da transcrição de áudio)
* Quadros-chave: um conjunto de quadros-chave que são extraídos do vídeo
* Moderação de conteúdo visual: a parte dos vídeos que foi sinalizada como de natureza adulta
* Anotação: resultado da anotação dos vídeos com base em um modelo de objeto predefinido

##  <a name="insightsjson-elements"></a>Elementos insights.json

A saída inclui um arquivo JSON (insights.json) com todas as informações que foram encontradas no vídeo ou áudio. O json pode conter os seguintes elementos:

### <a name="transcript"></a>transcript

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID da linha.|
|text|A própria transcrição.|
|Linguagem|O idioma da transcrição. Tem o objetivo dar suporte à transcrição na qual cada linha pode ter um idioma diferente.|
|instances|Uma lista com os intervalos de tempo nos quais essa linha apareceu. Se a instância for transcrita, ela terá apenas 1 instância.|

Exemplo:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID da linha de OCR.|
|text|O texto de OCR.|
|confidence|A confiança do reconhecimento.|
|Linguagem|O idioma do OCR.|
|instances|Uma lista de intervalos de tempo nos quais essa OCR apareceu (o mesmo OCR pode aparecer várias vezes).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>faces

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID da face.|
|Nome|O nome da face. Pode ser "Desconhecido #0", uma celebridade identificada ou uma pessoa treinada pelo cliente.|
|confidence|A confiança de identificação da face.|
|description|Uma descrição da celebridade. |
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

### <a name="shots"></a>shots

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID da captura.|
|keyFrames|Uma lista com os quadros-chave dentro da captura (cada um tem uma ID e uma lista de intervalos de tempo de instâncias). As instâncias de frames principais têm um campo thumbnailId com o ID de miniatura da keyFrame.|
|instances|Uma lista com os intervalos de tempo desta captura (as capturas têm apenas 1 instância).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>Estatísticas

|NOME|DESCRIÇÃO|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|WordCount|O número de palavras por alto-falante.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o orador tem em um vídeo.|
|SpeakerLongestMonolog|O mais longo monólogo do orador. Se o falante tiver silêncios dentro do monólogo, ele será incluído. O silêncio no início e no final do monólogo é removido.| 
|SpeakerTalkToListenRatio|O cálculo é baseado no tempo gasto no monólogo do locutor (sem o silêncio intermediário) dividido pelo tempo total do vídeo. A hora é arredondada para o terceiro ponto decimal.|


### <a name="sentiments"></a>sentiments

Os sentimentos são agregadas de acordo com seu campo sentimentType (Positivo/Neutro/Negativo). Por exemplo, 0-0.1, 0.1-0.2.

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID do sentimento.|
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

### <a name="labels"></a>rótulos

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID do rótulo.|
|Nome|O nome do rótulo (por exemplo, "Computador", "TV").|
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

### <a name="keywords"></a>palavras-chave

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID da palavra-chave.|
|text|O texto da palavra-chave.|
|confidence|A confiança do reconhecimento da palavra-chave.|
|Linguagem|O idioma da palavra-chave (quando traduzida).|
|instances|Uma lista de intervalos de tempo nos quais essa palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

O bloco visualContentModeration contém intervalos de tempo que o Video Indexer encontrou para potencialmente ter conteúdo adulto. Se visualContentModeration estiver vazio, não haverá conteúdo adulto identificado.

Os vídeos que contêm conteúdo adulto ou atraente podem estar disponíveis apenas para visualização privada. Os usuários têm a opção de enviar uma solicitação para uma revisão humana do conteúdo. Nesse caso, o atributo IsAdult conterá o resultado da revisão humana.

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID de moderação de conteúdo visual.|
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
## <a name="next-steps"></a>Próximas etapas

[Tutorial: Analisar vídeos com os Serviços de Mídia do Azure](analyze-videos-tutorial-with-api.md)
