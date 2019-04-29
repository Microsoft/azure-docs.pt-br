---
title: Habilidade de Análise de Imagens da pesquisa cognitiva – Azure Search
description: Extraia o texto semântico por meio de análise de imagem usando a habilidade de cognitivas de análise de Imagens em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5e2c92c22f98913da0e3668ceb84b212cc48396a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341670"
---
#   <a name="image-analysis-cognitive-skill"></a>Habilidade cognitiva de Análise de Imagens

A habilidade **Análise de Imagens** extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem. Por exemplo, é possível gerar uma legenda de uma imagem, criar marcas ou identificar celebridades e pontos de referência. Essa habilidade usa os modelos de machine learning fornecidos pela [Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) nos Serviços Cognitivos. 

> [!NOTE]
> A partir de 21 de dezembro de 2018, você poderá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) a um conjunto de habilidades do Azure Search. Isso nos permitirá começar a cobrar pela execução do conjunto de habilidades. Nessa data, também passamos a cobrar pela extração de imagem como parte do estágio de decodificação de documentos. A extração de texto de documentos continua sendo oferecida sem custo adicional.
>
> A execução de [habilidades cognitivas internas](cognitive-search-predefined-skills.md) é cobrada com base no [preço de pagamento conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services), com a mesma taxa que será utilizada se você realizar a tarefa diretamente. A extração de imagem é uma cobrança do Azure Search, atualmente oferecida com o preço de versão prévia. Para obter detalhes, confira a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) ou [Como a cobrança funciona](search-sku-tier.md#how-billing-works).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | DESCRIÇÃO |
|--------------------|-------------|
| defaultLanguageCode   |  Uma cadeia de caracteres que indica o idioma para retornar. O serviço retorna resultados de reconhecimento no idioma especificado. Se este parâmetro não for especificado, o valor padrão é “en”. <br/><br/>Idiomas com suporte: <br/>*en* - inglês (padrão) <br/> *zh* - chinês Simplificado|
|visualFeatures |   Uma matriz de cadeias de caracteres que indica os tipos de recurso visuais para retornar. Tipos de recurso válido visuais:  <ul><li> *categorias* - classifica o conteúdo da imagem de acordo com uma taxonomia definida na documentação [Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *marcas* - marca a imagem com uma lista detalhada das palavras relacionadas ao conteúdo da imagem.</li><li>*Descrição* -descreve o conteúdo com uma frase em inglês completa da imagem.</li><li>*Faces* -detecta se faces estão presentes. Se estiverem, gera coordenadas de sexo e idade.</li><li> *ImageType* -detecta se a imagem é clip-art ou um desenho de linha.</li><li>  *Cor* -determina a cor de ênfase, cor dominante, e se uma imagem é preta e branco.</li><li>*Somente para adultos* -detecta se a imagem é pornográfico por natureza (ilustra nudez ou um ato de sexo). Conteúdo que sugere sexo também detectado.</li></ul> Nomes de recursos visuais diferenciam maiusculas de minúsculas.|
| detalhes   | Uma matriz de cadeias de caracteres que indica qual domínio específico de detalhes retornar. Tipos de recurso válido visuais: <ul><li>*Celebridades* -identifica celebridades se detectado na imagem.</li><li>*Marcos* -identifica os pontos de referência se detectado na imagem.</li></ul>
 |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | DESCRIÇÃO                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. Atualmente só funciona com o campo "/document/normalized_images" produzido pelo indexador de BLOBs do Microsoft Azure quando ```imageAction``` é definido como um valor diferente de ```none```. Para obter mais informações, confira [este exemplo](#sample-output).|



##  <a name="sample-definition"></a>Definição de exemplo
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500
                }
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "categories": [
                    {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                        "name": "people_",
                        "score": 0.83984375,
                        "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                        "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
            }
        }
    ]
}
```


## <a name="error-cases"></a>Casos de erro
Nos casos de erro a seguir, nenhum elemento é extraído.

| Código do Erro | DESCRIÇÃO |
|------------|-------------|
| NotSupportedLanguage | O idioma fornecido não tem suporte. |
| InvalidImageUrl | A URL da imagem está incorretamente formatada ou não está acessível.|
| InvalidImageFormat | Os dados de entrada não são uma imagem válida. |
| InvalidImageSize | A imagem de entrada é muito grande. |
| NotSupportedVisualFeature  | O tipo de destino especificado não é válido. |
| NotSupportedImage | Imagem sem suporte, por exemplo, pornografia infantil. |
| InvalidDetails | Modelo de domínio específico sem suporte. |

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
