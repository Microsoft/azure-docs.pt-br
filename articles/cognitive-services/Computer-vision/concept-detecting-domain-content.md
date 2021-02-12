---
title: Conteúdo específico do domínio-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Saiba como especificar um domínio de categorização de imagem para retornar informações mais detalhadas sobre uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cd872d66088e165bfc8356ab6d96a0a6135a0e0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538301"
---
# <a name="detect-domain-specific-content"></a>Detectar conteúdo específico de um domínio

Além de marcação e categorização de alto nível, a Pesquisa Visual Computacional também suporta uma análise específica de domínio mais detalhada usando modelos treinados em dados especializados.

Há duas maneiras de usar os modelos de domínio específico: por si só (análise com escopo) ou como um aprimoramento do recurso de categorização.

### <a name="scoped-analysis"></a>Análise de escopo

Você pode analisar uma imagem usando apenas o modelo específico de domínio escolhido chamando a API [Models/ \<model\> /Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) .

A seguir está um exemplo de resposta JSON retornada pela API **modelos/celebridades/analisar** para determinada imagem:

![Satya Nadella pé, sorrindo](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Análise aprimorada de categorização

Você também pode usar modelos de domínio específico para complementar a análise de imagem geral. Isso é feito como parte da [categorização de alto nível](concept-categorizing-images.md) através da especificação de modelos de domínio específico no parâmetro *details* da chamada à API [Analisar](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b).

Neste caso, o classificador da taxonomia de 86 categorias é chamado primeiro. Se qualquer uma das categorias detectadas tiver um modelo de domínio específico correspondente, a imagem é passada por meio deste modelo também e os resultados são adicionados.

A resposta JSON a seguir mostra como a análise de domínio específico pode ser incluída como o nó `detail` em uma análise mais abrangente de categorização.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Lista de modelos de domínio específico

Atualmente, a Pesquisa Visual Computacional suporta os seguintes modelos de domínio específico para detectar conteúdo de domínio específico:

| Name | Descrição |
|------|-------------|
| Celebridades | Reconhecimento de celebridades, compatível com imagens classificadas na categoria `people_` |
| Pontos de referência | Reconhecimento de pontos de referência, com suporte para imagens classificado na `outdoor_` ou `building_` categorias |

Chamar a API [Modelos](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20e) retornará essa informação junto com as categorias para as quais cada modelo é aplicável:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos [categorizar imagens](concept-categorizing-images.md).
