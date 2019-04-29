---
title: Redimensionar e recortar miniaturas de imagens – API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Redimensionar e recortar miniaturas de imagens incluídas em respostas da API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c53b84f31100ab72458ab6fb79b2009450eda15c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914578"
---
# <a name="resize-and-crop-thumbnail-images"></a>Redimensionar e recortar miniaturas de imagens

Após o processamento de uma consulta de pesquisa, o Bing gerará as informações em miniatura para todas as imagens na sua [resposta](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Essas informações podem ser usadas para exibir todas as miniaturas retornadas ou um subconjunto delas. Se você exibir um subconjunto, fornecem uma opção para exibir as imagens restantes.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se o usuário clicar na miniatura, você pode usar [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) para exibir a imagem em tamanho máximo para o usuário. Verifique se a imagem foi atribuída.

Se `shoppingSourcesCount` ou `recipeSourcesCount` forem maiores que zero, adicione selos, como um carrinho de compras, para a miniatura indicar que existem compras ou receitas para o item na imagem.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Para obter informações sobre a imagem, como páginas da Web que incluam a imagem ou pessoas que foram reconhecidas na imagem, use [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Para obter detalhes, consulte [Insights de imagem](image-insights.md).

## <a name="resizing-and-cropping-thumbnails"></a>Redimensionamento e recorte de miniaturas

Você também pode redimensionar e expandir miniaturas, por exemplo, ao passar o cursor sobre elas.
> [!NOTE]
> Se expandir a imagem, verifique se ela foi atribuída. Por exemplo, extraindo o host de [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) e exibindo-o abaixo da imagem.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
