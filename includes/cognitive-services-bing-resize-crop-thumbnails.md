---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60513541"
---
Algumas respostas Bing incluem URLs para imagens em miniatura servidas pelo Bing. Você pode redimensionar e cortar as imagens em miniatura. 

> [!NOTE]
> Garanta que o tamanho e o corte da miniatura forneçam um cenário de pesquisa e respeitem os direitos de terceiros, conforme exigido pelos requisitos de uso e de exibição da API de Pesquisa do Bing.


Para redimensionar uma imagem, incluir a consulta de l (largura) e/ou na URL da miniatura de consulta de parâmetro-h (altura). Especifique a largura e altura em pixels. Por exemplo:   
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Se você especificar apenas a largura ou o parâmetro de consulta altura, o Bing mantém a taxa de proporção da imagem. Se você especificar ambos width e height e não manter a taxa de proporção original da imagem, o Bing adiciona preenchimento branco para a borda da imagem. Por exemplo, se você redimensionar uma imagem de 480 x 359 para 200 x 200 sem corte, a largura total contém a imagem, mas a altura contém 25 pixels de preenchimento branco nas partes superior e inferior da imagem. O mesmo será verdadeiro se a imagem tiver 359x480, exceto pelas bordas esquerda e direita, que terão o preenchimento branco. Se você cortar a imagem, o preenchimento branco não será adicionado.  

 
A figura a seguir mostra o tamanho original de uma imagem em miniatura (480 x 300).  
  
![Imagem original de paisagem](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
A figura a seguir mostra a imagem redimensionada para 200 x 200. A taxa de proporção é mantida e as bordas superior e inferior são preenchidas com branco (a borda preta é incluída para mostrar o preenchimento).  
  
![Imagem redimensionada de paisagem](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Se você especificar dimensões maiores que a largura e a altura originais da imagem, a imagem será preenchida com branco nas bordas esquerda e superior.  
  
Para cortar uma imagem, inclua o parâmetro de consulta c (cortar). A seguir estão os valores possíveis que você pode especificar.  
  
- 4&mdash;Taxa de proporção automática  
- 7&mdash;Taxa de proporção inteligente  
  
Se você solicitar o corte de taxa de proporção inteligente (c = 7), a imagem é cortada do centro da região da imagem de interesse para fora, mantendo a taxa de proporção da imagem. A região de interesse é a área da imagem que o Bing determina que contém a maioria das partes de importação. A seguir é mostrado um exemplo de região de interesse.  
  
![Região de interesse](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Se você redimensiona uma imagem e solicitou um corte de Taxa de proporção inteligente, a imagem é reduzida para o tamanho solicitado, mantendo a taxa de proporção. A imagem é recortada, em seguida, com base nas dimensões redimensionadas. Por exemplo, se a largura redimensionada for menor ou igual à altura, a imagem será cortada à esquerda e à direita do centro da região de interesse. Caso contrário, a imagem é cortada nas partes superior e inferior do centro da região de interesse.  
  
 
A seguir é exibida a imagem reduzida para 200 x 200 usando o corte da Taxa de proporção inteligente.  
  
![Imagem paisagem cortada para 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
A seguir é exibida a imagem reduzida para 200 x 100 usando o corte da Taxa de proporção inteligente.  
   
![Imagem paisagem cortada para 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
A seguir é exibida a imagem reduzida para 100 x 200 usando o corte da Taxa de proporção inteligente.  
  
![Imagem paisagem cortada para 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Se o Bing não puder determinar a região da imagem de interesse, ele usa o corte de Taxa de proporção automática.  
  
Se você solicitar um corte de Taxa de proporção automática (c = 4), o Bing usa as seguintes regras para cortar a imagem.  
  
- Se (Largura da imagem original / Altura da imagem original) < (Largura da imagem solicitada / Altura da imagem solicitada), a imagem é medida da parte superior esquerda e cortada na parte inferior.  
- Se (Largura da imagem original / Altura da imagem original) < (Largura da imagem solicitada / Altura da imagem solicitada), a imagem é medida do centro e cortada nas partes esquerda e direita.  



A seguir é exibida uma imagem retrato 225 x 300.  
  
![Imagem de girassol original](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
A seguir é exibida a imagem reduzida para 200 x 200 usando o corte da Taxa de proporção automática. A imagem é medida a partir do canto superior esquerdo resultante na parte inferior da imagem que está sendo cortada.  
  
![Imagem de girassol cortada para 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
A seguir é exibida a imagem reduzida para 200 x 100 usando o corte da Taxa de proporção automática. A imagem é medida a partir do canto superior esquerdo resultante na parte inferior da imagem que está sendo cortada.  
  
![Imagem de girassol cortada para 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
A seguir é exibida a imagem reduzida para 100 x 200 usando o corte da Taxa de proporção automática. A imagem é medida a partir do centro resultando no corte das partes esquerda e direita da imagem.  
  
![Imagem de girassol cortada para 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

