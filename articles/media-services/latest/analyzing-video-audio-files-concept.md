---
title: Analisar arquivos de áudio e vídeo
description: Saiba como analisar o conteúdo de áudio e vídeo usando o AudioAnalyzerPreset e o VideoAnalyzerPreset nos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 25b5c3163b657633ca78215468f4c2a2e40949b7
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897217"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analisar arquivos de vídeo e áudio com os serviços de mídia do Azure

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os serviços de mídia do Azure v3 permitem que você extraia informações de seus arquivos de vídeo e áudio com Video Indexer. Este artigo descreve as predefinições do analisador de serviços de mídia v3 usadas para extrair essas informações. Se você quiser informações mais detalhadas, use o Video Indexer diretamente. Para entender quando usar as predefinições do Video Indexer vs. do Media Services Analyzer, confira o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Há dois modos de predefinição, básico e padrão do analisador de áudio. Consulte a descrição das diferenças na tabela a seguir.

Para analisar seu conteúdo usando as predefinições dos serviços de mídia v3, você cria uma **transformação** e envia um **trabalho** que usa uma dessas predefinições: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. Para obter um tutorial que demonstra como usar o **VideoAnalyzerPreset**, consulte [analisar vídeos com os serviços de mídia do Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Ao usar uma predefinições de vídeo ou analisador de áudio, use o portal do Azure para definir sua conta para ter 10 unidades reservadas de mídia S3, embora isso não seja necessário. Você pode usar o S1 ou S2 para predefinições de áudio. Para saber mais, confira [Processamento de mídia de escala](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança

Como um lembrete importante, você precisará respeitar todas as leis aplicáveis em seu uso do Video Indexer e não poderá usar o Video Indexer ou qualquer outro serviço do Azure de uma maneira que viole os direitos de outras pessoas ou que possa ser prejudicial a outras pessoas. Antes de carregar qualquer vídeo, incluindo dados biométricos, no serviço do Video Indexer para processamento e armazenamento, você precisará ter todos os direitos apropriados, incluindo todos os consentimentos apropriados, dos indivíduos mostrados no vídeo. Para saber mais sobre a conformidade, a privacidade e a segurança do Video Indexer, leia os [Termos dos Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) da Microsoft. Para saber mais sobre as obrigações de privacidade e o tratamento de seus dados pela Microsoft, examine a [política de privacidade](https://privacy.microsoft.com/PrivacyStatement), o “OST” [(Termos do Online Services)](https://www.microsoft.com/licensing/product-licensing/products) e o “DPA” [(Adendo de Processamento de Dados)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) da Microsoft. Informações de privacidade adicionais, incluindo retenção de dados, exclusão/destruição, estão disponíveis no OST e [aqui](../video-indexer/faq.md). Ao usar o Video Indexer, você concorda em vincular-se aos Termos dos Serviços Cognitivos, ao OST, ao DPA e à política de privacidade.

## <a name="built-in-presets"></a>Predefinições internas

O Serviços de Mídia do Microsoft Azure atualmente suporta as seguintes predefinições do analisador interno:  

|**Nome da predefinição**|**Cenário**|**Detalhes**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analisando o padrão de áudio|A predefinição aplica um conjunto predefinido de operações de análise baseadas em IA, incluindo transcrição de fala. Atualmente, a predefinição dá suporta ao processamento de conteúdo com uma única faixa do áudio que contenha fala em um único idioma. É possível especificar o idioma o conteúdo de áudio na entrada usando o formato BCP-47 de 'marca de idioma-região'. Os idiomas com suporte são Inglês (' en-US ' e ' en-GB '), espanhol (' es-ES ' e ' es-MX '), francês (' fr-FR '), italiano (' it-IT '), japonês (' ja-JP '), Português (' pt-BR '), chinês (' ZH-CN '), alemão (' de-DE '), árabe (' ar-ex ' e ' ar-SY '), russo (' ru-RU '), híndi (' hi-IN ') e coreano (' ko-KR ').<br/><br/> Se o idioma não for especificado ou definido como nulo, a detecção automática de idioma escolherá o primeiro idioma detectado e continuará com o idioma selecionado durante o arquivo. O recurso de detecção automática de idioma suporta atualmente inglês, chinês, francês, alemão, italiano, japonês, espanhol, russo e português. Ele não dá suporte à alternância dinâmica entre os idiomas após a detecção do primeiro idioma. O recurso de detecção automática de idioma funciona melhor com gravações de áudio com fala claramente discernível. Se a detecção automática de idioma falhar ao localizar o idioma, a transcrição retornará para o inglês.|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analisando o áudio básico|Esse modo executa a transcrição de fala em texto e a geração de um arquivo de subtítulo/legenda do VTT. A saída desse modo inclui um arquivo JSON do insights, incluindo apenas palavras-chave, transcrição e informações de tempo. A detecção automática de idioma e os diarization de orador não estão incluídos nesse modo. A lista de idiomas com suporte está disponível [aqui](#built-in-presets)|
|[VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analisar áudio e vídeo|Extraia insights (metadados avançados) de áudio e vídeo e gere um arquivo no formato JSON. É possível especificar se deseja extrair apenas insights de áudio ao processar um arquivo de vídeo. Para obter mais informações, consulte [Analisar vídeo](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|Detectando rostos presentes em vídeo|Descreve as configurações a serem usadas ao analisar um vídeo para detectar todas as faces presentes.|

### <a name="audioanalyzerpreset-standard-mode"></a>Modo padrão AudioAnalyzerPreset

A predefinição permite extrair vários insights de áudio de um arquivo de áudio ou vídeo.

A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do áudio. Essa predefinição aceita uma propriedade que especifica o idioma do arquivo de entrada na forma de uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47). As informações de áudio incluem:

* **Transcrição de áudio**: uma transcrição das palavras faladas com carimbos de data/hora. Há suporte para vários idiomas.
* **Indexação do viva-voz**: um mapeamento dos alto-falantes e as palavras faladas correspondentes.
* **Análise de sentimentos de fala**: a saída da análise de sentimentos realizada na transcrição de áudio.
* **Palavras-** chave: palavras-chave que são extraídas da transcrição de áudio.

### <a name="audioanalyzerpreset-basic-mode"></a>Modo básico do AudioAnalyzerPreset

A predefinição permite extrair vários insights de áudio de um arquivo de áudio ou vídeo.

A saída inclui um arquivo JSON e um arquivo VTT para a transcrição de áudio. Essa predefinição aceita uma propriedade que especifica o idioma do arquivo de entrada na forma de uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47). A saída inclui:

* **Transcrição de áudio**: uma transcrição das palavras faladas com carimbos de data/hora. Há suporte para vários idiomas, mas a detecção automática de idioma e os diarization de orador não estão incluídos.
* **Palavras-** chave: palavras-chave que são extraídas da transcrição de áudio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A predefinição permite extrair vários insights de áudio e vídeo de um arquivo de vídeo. A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do vídeo, e uma coleção de miniaturas. Essa predefinição também aceita uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47) (que representa o idioma do vídeo) como propriedade. As informações de vídeos incluem todas as informações de áudio mencionadas acima, e os itens adicionais a seguir:

* **Acompanhamento de face**: o tempo durante o qual as faces estão presentes no vídeo. Cada face tem uma ID facial e uma coleção correspondente de miniaturas.
* **Texto visual**: o texto que é detectado por meio do reconhecimento óptico de caracteres. O texto é um carimbo de data/hora e também é usado para extrair palavras-chave (além da transcrição de áudio).
* **Quadros-chave**: uma coleção de quadros-chave extraídos do vídeo.
* **Moderação de conteúdo Visual**: a parte dos vídeos sinalizados como adulto ou erótico por natureza.
* **Anotação**: um resultado da anotação dos vídeos com base em um modelo de objeto predefinido

## <a name="insightsjson-elements"></a>Elementos insights.json

A saída inclui um arquivo JSON (insights.jsem) com todas as informações encontradas no vídeo ou áudio. O JSON pode conter os seguintes elementos:

### <a name="transcript"></a>transcript

|Nome|Descrição|
|---|---|
|id|A ID da linha.|
|texto|A própria transcrição.|
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

|Nome|Descrição|
|---|---|
|id|A ID da linha de OCR.|
|texto|O texto de OCR.|
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

|Nome|Descrição|
|---|---|
|id|A ID da face.|
|name|O nome da face. Pode ser ' desconhecido #0 ', um celebridade identificado ou uma pessoa treinada para o cliente.|
|confidence|A confiança de identificação da face.|
|descrição|Uma descrição da celebridade. |
|thumbnailId|O ID da miniatura dessa face.|
|knownPersonId|A ID interna (se for uma pessoa conhecida).|
|referenceId|A ID do Bing (se for um celebridade do Bing).|
|referenceType|No momento, apenas Bing.|
|título|O título (se for um celebridade — por exemplo, "CEO da Microsoft").|
|imageUrl|A URL da imagem, se for um celebridade.|
|instances|Instâncias em que a face apareceu no intervalo de tempo determinado. Cada ocorrência também tem uma thumbnailsId. |

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

|Nome|Descrição|
|---|---|
|id|A ID da captura.|
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

### <a name="statistics"></a>estatísticas

|Nome|Descrição|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|WordCount|O número de palavras por alto-falante.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o orador tem em um vídeo.|
|SpeakerLongestMonolog|O mais longo monólogo do orador. Se o palestrante tiver silêncios dentro do monolog, ele será incluído. O silêncio no início e no final do monólogo é removido.|
|SpeakerTalkToListenRatio|O cálculo é baseado no tempo gasto no monólogo do locutor (sem o silêncio intermediário) dividido pelo tempo total do vídeo. A hora é arredondada para o terceiro ponto decimal.|


### <a name="sentiments"></a>sentiments

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

### <a name="labels"></a>rótulos

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

### <a name="keywords"></a>palavras-chave

|Nome|Descrição|
|---|---|
|id|A ID da palavra-chave.|
|texto|O texto da palavra-chave.|
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

O bloco visualContentModeration contém intervalos de tempo que o Video Indexer encontrou para potencialmente ter conteúdo adulto. Se visualContentModeration estiver vazio, não haverá nenhum conteúdo adulto identificado.

Os vídeos que contêm conteúdo adulto ou atraente podem estar disponíveis apenas para visualização privada. Os usuários podem enviar uma solicitação para uma revisão humana do conteúdo; nesse caso, o `IsAdult` atributo conterá o resultado da revisão humana.

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
## <a name="next-steps"></a>Próximas etapas

[Tutorial: Analisar vídeos com os Serviços de Mídia do Azure](analyze-videos-tutorial-with-api.md)