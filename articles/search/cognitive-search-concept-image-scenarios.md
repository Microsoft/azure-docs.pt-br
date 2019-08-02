---
title: Processar e extrair o texto de imagens na pesquisa cognitiva – Azure Search
description: Processar e extrair texto e outras informações de imagens nos pipelines da pesquisa cognitiva no Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 589f8c8f11138b4fb5c3c3096229e28c633efb0d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423013"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Como processar e extrair informações de imagens em cenários da pesquisa cognitiva

A pesquisa cognitiva tem várias funcionalidades para trabalhar com imagens e arquivos de imagem. Durante a quebra de documento, você pode usar o parâmetro *imageAction* extrair texto de fotos ou imagens que contêm texto alfanumérico, como a palavra "PARE" em um sinal de parada. Outros cenários incluem gerar uma representação de texto de uma imagem, como "dente-de-leão" para uma foto de um dente-de-leão ou a cor "amarela". Você também pode extrair metadados sobre a imagem, como seu tamanho.

Este artigo aborda o processamento de imagens em mais detalhes e fornece diretrizes para trabalhar com imagens em um pipeline de pesquisa cognitivas.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Obter imagens normalizadas

Como parte da quebra do documento, há um novo conjunto de parâmetros de configuração do indexador para lidar com arquivos de imagem ou imagens incorporadas em arquivos. Esses parâmetros são usados para normalizar imagens para processamento downstream maior. Normalizar imagens as torna mais uniformes. Imagens grandes são redimensionadas para uma altura e largura máximas, para torná-las consumíveis. Para imagens que fornecem metadados na orientação, a rotação da imagem será ajustada para carregamento vertical. Ajustes de metadados são capturados em um tipo complexo criado para cada imagem. 

Você não pode desativar a normalização de imagem. Habilidades que iteram sobre imagens esperam imagens normalizadas. Habilitar a normalização de imagem em um indexador requer que um conconhecimento seja anexado a esse indexador.

| Parâmetro de configuração | Descrição |
|--------------------|-------------|
| imageAction   | Definido como "none" se nenhuma ação puder ser tomada quando os arquivos de imagem ou imagens incorporadas forem encontrados. <br/>Defina como "generateNormalizedImages" para gerar uma matriz de imagens normalizadas como parte da quebra de documento.<br/>Defina como "generateNormalizedImagePerPage" para gerar uma matriz de imagens normalizadas na qual, para PDFs na fonte de dados, cada página é renderizada para uma imagem de saída.  A funcionalidade é a mesmo que "generateNormalizedImages" para tipos de arquivos que não são PDF.<br/>Para qualquer opção que não seja "none", essas imagens serão expostas no campo *normalized_images*. <br/>O padrão é "none". Essa configuração só é pertinente a fontes de dados de blob, quando "dataToExtract" é definido como "contentAndMetadata". <br/>Um máximo de 1000 imagens será extraído de um determinado documento. Se houver mais de 1000 imagens em um documento, o primeiro 1000 será extraído e um aviso será gerado. |
|  normalizedImageMaxWidth | A largura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000.|
|  normalizedImageMaxHeight | A altura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000.|

> [!NOTE]
> Se você definir a propriedade *imageAction* como algo diferente de "none", não será possível definir a propriedade *parsingMode* como algo diferente de "default".  Você só pode definir uma dessas duas propriedades como um valor não padrão na configuração do indexador.

Defina o parâmetro **parsingMode** `json`(para indexar cada blob como um único documento) ou `jsonArray` (se seus blobs contêm matrizes JSON e você precisa que cada elemento da matriz seja tratado como um documento separado).

O padrão de 2000 pixels para a largura e altura máximas das imagens normalizadas se baseia nos tamanhos máximos compatíveis com a [habilidade de OCR](cognitive-search-skill-ocr.md) e a [habilidade de análise de imagem](cognitive-search-skill-image-analysis.md). Se você aumentar os limites máximos, o processamento poderá falhar em imagens maiores.


Especifique o imageAction na [definição do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) da seguinte maneira:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Quando o campo *imageAction* for definido para qualquer valor diferente de "none", o novo campo *normalized_images* conterá uma matriz de imagens. Cada imagem é um tipo complexo que tem os seguintes membros:

| Membro de imagem       | Descrição                             |
|--------------------|-----------------------------------------|
| dados               | Cadeia codificada em Base64 da imagem normalizada no formato JPEG.   |
| width              | Largura da imagem normalizada em pixels. |
| height             | Altura da imagem normalizada em pixels. |
| originalWidth      | A largura original da imagem antes da normalização. |
| originalHeight      | A altura original da imagem antes da normalização. |
| rotationFromOriginal |  Rotação no sentido anti-horário em graus para criar a imagem normalizada. Um valor entre 0 graus e 360 graus. Esta etapa lê os metadados da imagem gerada por uma câmera ou scanner. Geralmente, é um múltiplo de 90 graus. |
| contentOffset |O deslocamento de caractere dentro do campo de conteúdo do qual a imagem foi extraída. Este campo só é aplicável a arquivos com imagens incorporadas. |

 Exemplo de valor de *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Habilidades relacionadas com a imagem

Existem duas habilidades cognitivas internas que usam imagens como entrada: [OCR](cognitive-search-skill-ocr.md) e [Análise de Imagens](cognitive-search-skill-image-analysis.md). 

Atualmente, essas habilidades só funcionam com imagens geradas na etapa de quebra de documento. Como tal, a única entrada com suporte é `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Habilidade Análise de Imagens

A [habilidade Análise de Imagens](cognitive-search-skill-image-analysis.md) extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem. Por exemplo, é possível gerar uma legenda de uma imagem, criar marcas ou identificar celebridades e pontos de referência.

### <a name="ocr-skill"></a>Habilidade OCR

O [habilidade OCR](cognitive-search-skill-ocr.md) extrai o texto de arquivos de imagem como JPGs, PNGs e bitmaps. Pode extrair texto, bem como informações de layout. As informações de layout fornecem caixas delimitadoras para cada uma das cadeias de caracteres identificadas.

## <a name="embedded-image-scenario"></a>Cenário de imagem incorporada

Um cenário comum envolve a criação de uma única cadeia de caracteres que contém todos os conteúdos do arquivo, texto e texto de origem da imagem, executando as seguintes etapas:  

1. [Extrair normalized_images](#get-normalized-images)
1. Executar a habilidade OCR usando `"/document/normalized_images"` como entrada
1. Mesclar a representação de texto dessas imagens com o texto bruto extraído do arquivo. Use a habilidade [Mesclar Texto](cognitive-search-skill-textmerger.md) para consolidar as duas partes de texto em uma única cadeia de caracteres grande.

O conjunto de habilidades de exemplo a seguir cria um campo *merged_text* que contém o conteúdo textual do documento. Também inclui o texto processado em OCR de cada uma das imagens incorporadas. 

#### <a name="request-body-syntax"></a>Sintaxe do corpo da solicitação
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

Agora que você tem um campo merged_text, é possível mapeá-lo como um campo pesquisável na definição do indexador. Todo o conteúdo dos arquivos, incluindo o texto das imagens, serão pesquisáveis.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualizar caixas delimitadoras do texto extraído

Outro cenário comum é visualizar informações de layout dos resultados de pesquisa. Por exemplo, talvez você queira realçar o local em que um trecho de texto foi encontrado em uma imagem como parte dos resultados da pesquisa.

Como a etapa de OCR é executada nas imagens normalizadas, as coordenadas de layout estão no espaço da imagem normalizada. Ao exibir a imagem normalizada, a presença de coordenadas geralmente não é um problema, mas em algumas situações, você deseja exibir a imagem original. Nesse caso, converta cada um dos pontos de coordenadas no layout no sistema de coordenadas da imagem original. 

Como um auxiliar, se você precisar transformar coordenadas normalizadas no espaço de coordenadas original, use o seguinte algoritmo:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Consulte também
+ [Criar indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Habilidade Analisar Imagem](cognitive-search-skill-image-analysis.md)
+ [Habilidade OCR](cognitive-search-skill-ocr.md)
+ [Habilidade Mesclar Texto](cognitive-search-skill-textmerger.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
