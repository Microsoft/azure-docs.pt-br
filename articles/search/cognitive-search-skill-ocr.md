---
title: Habilidades cognitivas OCR
titleSuffix: Azure Cognitive Search
description: Extraia o texto de arquivos de imagem usando OCR (reconhecimento óptico de caracteres) em um pipeline de enriquecimento no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 8b6a7c3e05b26cbda80ebf1a3fc0d4fed8255e6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950798"
---
# <a name="ocr-cognitive-skill"></a>Habilidades cognitivas OCR

A habilidade de **reconhecimento óptico de caracteres (OCR)** reconhece texto impresso e manuscrito em arquivos de imagem. Essa habilidade usa os modelos de aprendizado de máquina fornecidos pelo [Pesquisa Visual computacional](../cognitive-services/computer-vision/overview.md) API [v 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) em serviços cognitivas. A habilidade **OCR** é mapeada para a seguinte funcionalidade:

+ Para inglês, espanhol, alemão, francês, italiano, Português e holandês, a nova API de ["leitura"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) é usada.
+ Para todos os outros idiomas, a API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-api) é usada.

A habilidade **OCR** extrai o texto de arquivos de imagem. Formatos de arquivo com suporte incluem:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ . FORMATO

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `detectOrientation`   | Habilita a detecção automática da orientação da imagem. <br/> Valores válidos: verdadeiro / falso.|
| `defaultLanguageCode` | <p>   Código de idioma do texto de entrada. As linguagens com suporte incluem: <br/> zh-Hans (Chinês Simplificado) <br/> zh-Hant (Chinês Tradicional) <br/>cs (tcheco) <br/>da (dinamarquês) <br/>nl (holandês) <br/>en (em inglês) <br/>fi (finlandês)  <br/>fr (francês) <br/>  de (alemão) <br/>el (Grego) <br/> hu (húngaro) <br/> it (Italiano) <br/>  ja (Japonês) <br/> ko (Coreano) <br/> nb (Norueguês) <br/>   pl (Polonês) <br/> pt (Português) <br/>  ru (Russo) <br/>  es (Espanhol) <br/>  sv (Sueco) <br/>  tr (Turco) <br/> ar (Árabe) <br/> ro (Romeno) <br/> sr-Cyrl (Cirílico sérvio) <br/> SR-Latn (Latim sérvio) <br/>  SK (eslovaco) <br/>  unk (desconhecido) <br/><br/> Se o código de idioma não for especificado ou for nulo, o idioma será definido como inglês. Se o idioma for definido explicitamente como "unk", o idioma será detectado automaticamente. </p> |
| `lineEnding` | O valor a ser usado entre cada linha detectada. Valores possíveis: "Space", "CarriageReturn", "alimentação de espaço".  O padrão é "Space". |

Anteriormente, havia um parâmetro chamado "textExtractionAlgorithm" para especificar se a habilidade deve extrair texto "impresso" ou "manuscrito".  Esse parâmetro é preterido e não é mais necessário, pois o algoritmo mais recente da API de leitura é capaz de extrair os dois tipos de texto de uma só vez.  Se sua definição de habilidade já incluir esse parâmetro, você não precisará removê-lo, mas ele não será mais usado e os dois tipos de texto serão extraídos no futuro, independentemente do que está definido como.

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                                          |
|---------------|------------------------------------------------------|
| `image`         | Tipo complexo. Atualmente só funciona com o campo "/document/normalized_images" produzido pelo indexador de BLOBs do Microsoft Azure quando ```imageAction``` é definido como um valor diferente de ```none```. Para obter mais informações, confira [este exemplo](#sample-output).|


## <a name="skill-outputs"></a>Saídas de habilidades
| Nome de saída     | Descrição                   |
|---------------|-------------------------------|
| `text`            | Texto sem formatação extraído da imagem.   |
| `layoutText`    | Tipo complexo que descreve o texto extraído e o local em que o texto foi encontrado.|


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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exemplo: mesclar texto extraído da imagens inseridas com o conteúdo do documento.

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
          "name":"text",
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
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

## <a name="see-also"></a>Confira também
+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Habilidade de TextMerger](cognitive-search-skill-textmerger.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](/rest/api/searchservice/create-indexer)