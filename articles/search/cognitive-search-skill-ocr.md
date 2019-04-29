---
title: Habilidade de OCR da pesquisa cognitiva – Azure Search
description: Extraia o texto de arquivos de imagem usando o OCR (reconhecimento óptico de caracteres) em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fb7fe0689ce54031880565c0c6409afeab2ff523
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126953"
---
# <a name="ocr-cognitive-skill"></a>Habilidades cognitivas OCR

A habilidade OCR (reconhecimento óptico de caracteres) reconhece textos impressos e manuscritos em arquivos de imagem. Essa habilidade usa os modelos de machine learning fornecidos pela [Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) nos Serviços Cognitivos. A habilidade **OCR** é mapeada para a seguinte funcionalidade:

+ Quando textExtractionAlgorithm é definido como "handwritten", a funcionalidade ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) é usada.
+ Quando textExtractionAlgorithm é definido como "printed", a funcionalidade ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) é usada para idiomas diferentes do inglês. Para o inglês, a nova funcionalidade ["Reconhecimento de Texto"](../cognitive-services/computer-vision/concept-recognizing-text.md) para texto impresso é usada.

A habilidade **OCR** extrai o texto de arquivos de imagem. Formatos de arquivo com suporte incluem:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> A partir de 21 de dezembro de 2018, você poderá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) a um conjunto de habilidades do Azure Search. Isso nos permitirá começar a cobrar pela execução do conjunto de habilidades. Nessa data, também passamos a cobrar pela extração de imagem como parte do estágio de decodificação de documentos. A extração de texto de documentos continua sendo oferecida sem custo adicional.
>
> A execução de [habilidades cognitivas internas](cognitive-search-predefined-skills.md) é cobrada com base no [preço de pagamento conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services), com a mesma taxa que será utilizada se você realizar a tarefa diretamente. A extração de imagem é uma cobrança do Azure Search, atualmente oferecida com o preço de versão prévia. Para obter detalhes, confira a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) ou [Como a cobrança funciona](search-sku-tier.md#how-billing-works).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | DESCRIÇÃO |
|--------------------|-------------|
| detectOrientation | Habilita a detecção automática da orientação da imagem. <br/> Valores válidos: verdadeiro / falso.|
|defaultLanguageCode | <p>  Código de idioma do texto de entrada. As linguagens com suporte incluem: <br/> zh-Hans (Chinês Simplificado) <br/> zh-Hant (Chinês Tradicional) <br/>cs (tcheco) <br/>da (dinamarquês) <br/>nl (holandês) <br/>en (em inglês) <br/>fi (finlandês)  <br/>fr (francês) <br/>  de (alemão) <br/>el (Grego) <br/> hu (húngaro) <br/> it (Italiano) <br/>  ja (Japonês) <br/> ko (Coreano) <br/> nb (Norueguês) <br/>   pl (Polonês) <br/> pt (Português) <br/>  ru (Russo) <br/>  es (Espanhol) <br/>  sv (Sueco) <br/>  tr (Turco) <br/> ar (Árabe) <br/> ro (Romeno) <br/> sr-Cyrl (Cirílico sérvio) <br/> SR-Latn (Latim sérvio) <br/>  SK (Eslovaco). <br/>  unk (desconhecido) <br/><br/> Se o código de idioma não for especificado ou for nulo, o idioma será definido como inglês. Se o idioma for definido explicitamente como "unk", o idioma será detectado automaticamente. </p> |
| textExtractionAlgorithm | "impresso" ou "manuscrito". O algoritmo de OCR de reconhecimento de texto "manuscrito" está atualmente em visualização e tem suporte apenas em inglês. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | DESCRIÇÃO                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. Atualmente só funciona com o campo "/document/normalized_images" produzido pelo indexador de BLOBs do Microsoft Azure quando ```imageAction``` é definido como um valor diferente de ```none```. Para obter mais informações, confira [este exemplo](#sample-output).|


## <a name="skill-outputs"></a>Saídas de habilidades
| Nome de saída     | DESCRIÇÃO                   |
|---------------|-------------------------------|
| text          | Texto sem formatação extraído da imagem.   |
| layoutText    | Tipo complexo que descreve o texto extraído e o local em que o texto foi encontrado.|


## <a name="sample-definition"></a>Definição de exemplo

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Exemplo de saída de texto e layoutText

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exemplo: Mesclar o texto extraído de imagens incorporadas com o conteúdo do documento.

Um caso de uso comum para o Text Merger é a capacidade de mesclar a representação textual de imagens (texto de uma habilidade OCR ou a legenda de uma imagem) no campo de conteúdo de um documento.

O conjunto de habilidades de exemplo a seguir cria um campo *merged_text*. Esse campo traz o conteúdo textual do documento e o texto processado para OCR de cada uma das imagens inseridas nesse documento.

#### <a name="request-body-syntax"></a>Sintaxe de Corpo da Solicitação
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
O exemplo de conjunto de qualificações acima presume que existe um campo de imagens normalizado. Para gerar esse campo, defina a configuração *imageAction* na definição do indexador para *generateNormalizedImages* conforme mostrado abaixo:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Consulte também
+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Habilidade de TextMerger](cognitive-search-skill-textmerger.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
