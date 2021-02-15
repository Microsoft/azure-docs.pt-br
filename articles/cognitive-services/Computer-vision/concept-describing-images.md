---
title: Descrições de imagem-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao recurso de descrição de imagem da API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4fd328dad3544697ba1f4896f7383be857b097a5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530656"
---
# <a name="describe-images-with-human-readable-language"></a>Descrever imagens com linguagem legível por humanos

A Pesquisa Visual Computacional pode analisar uma imagem e gerar uma sentença legível por humanos que descreve seu conteúdo. O algoritmo realmente retorna várias descrições com base em diferentes recursos visuais, e cada descrição recebe uma pontuação de confiança. A saída final é uma lista de descrições ordenadas do maior para o menor nível de confiança.

## <a name="image-description-example"></a>Exemplo de descrição de imagem

A resposta JSON a seguir ilustra o que o Computer Vision retorna ao descrever a imagem de exemplo com base em seus recursos visuais.

![Uma imagem em preto e branco dos edifícios em Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Usar a API

O recurso de descrição da imagem faz parte da API de [análise de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Você pode chamar essa API por meio de um SDK nativo ou por meio de chamadas REST. Inclua `Description` no parâmetro de consulta **visualFeatures** . Em seguida, quando você obtém a resposta JSON completa, simplesmente analise a cadeia de caracteres para o conteúdo da `"description"` seção.

* [Início rápido: Pesquisa Visual Computacional bibliotecas de cliente ou API REST](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos relacionados de [marcação de imagens](concept-tagging-images.md) e [categorização de imagens](concept-categorizing-images.md).
