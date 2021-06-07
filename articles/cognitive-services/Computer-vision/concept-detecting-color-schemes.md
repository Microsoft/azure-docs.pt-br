---
title: Detecção de esquema de cores-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção do esquema de cores em imagens usando a API do Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e7774d2cd100931f92ff80066ebea4463c6f65c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532645"
---
# <a name="detect-color-schemes-in-images"></a>Detectar esquemas de cores em imagens

A Pesquisa Visual Computacional analisa as cores em uma imagem para fornecer três atributos diferentes: a cor de primeiro plano predominante, a cor da tela de fundo predominante e o conjunto de cores predominantes da imagem inteira. As cores retornadas pertencem ao conjunto: preto, azul, marrom, cinza, verde, laranja, rosa, roxo, vermelho, azul-petróleo, branco e amarelo. 

A Pesquisa Visual Computacional extrai também uma cor de destaque, que representa a cor mais vibrante na imagem, com base em uma combinação de cores predominantes e saturação. A cor de destaque é retornada como um código hexadecimal de cor HTML. 

A Pesquisa Visual Computacional também retorna um valor booliano que indica se uma imagem é em preto e branco.

## <a name="color-scheme-detection-examples"></a>Exemplos de detecção do esquema de cores

O exemplo a seguir ilustra a resposta JSON retornada pela visão do computador ao detectar o esquema de cores da imagem de exemplo. Nesse caso, a imagem de exemplo não é em preto e branco, mas as cores de primeiro plano e da tela de fundo predominantes são pretas e as cores predominantes da imagem inteira são em preto e branco.

![Montanhas de ar no pôr do sol, com a silhueta de uma pessoa](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Exemplos de cor predominante

A tabela a seguir mostra o primeiro plano, a tela de fundo e as cores da imagem retornados para cada imagem de exemplo.

| Imagem | Cores predominantes |
|-------|-----------------|
|![Uma flor branca com um fundo verde](./Images/flower.png)| Em primeiro plano: preto<br/>Em segundo plano: em branco<br/>Cores: Preto, branco, verde|
![Um treinamento em execução por meio de uma estação](./Images/train_station.png) | Em primeiro plano: preto<br/>Em segundo plano: preto<br/>Cores: preto |

### <a name="accent-color-examples"></a>Exemplos de cor de ênfase

 A tabela a seguir mostra a cor de ênfase retornada, como um valor hexadecimal de cor HTML, para cada imagem de exemplo.

| Imagem | Cor de ênfase |
|-------|--------------|
|![Uma pessoa que está em uma montanha ao pôr do sol](./Images/mountain_vista.png) | #BB6D10 |
|![Uma flor branca com um fundo verde](./Images/flower.png) | #C6A205 |
|![Um treinamento em execução por meio de uma estação](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exemplos de detecção de preto e branco

A tabela a seguir mostra a avaliação de preto e branco da Pesquisa Visual Computacional nas imagens de exemplo.

| Imagem | Preto e branco? |
|-------|----------------|
|![Uma imagem em preto e branco dos edifícios em Manhattan](./Images/bw_buildings.png) | true |
|![Uma casa azul e o jardim da frente](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>Usar a API

O recurso de detecção de esquema de cores faz parte da API de [análise de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Você pode chamar essa API por meio de um SDK nativo ou por meio de chamadas REST. Inclua `Color` no parâmetro de consulta **visualFeatures** . Em seguida, quando você obtém a resposta JSON completa, simplesmente analise a cadeia de caracteres para o conteúdo da `"color"` seção.

* [Início Rápido: Bibliotecas de clientes ou API REST da Pesquisa Visual Computacional](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
