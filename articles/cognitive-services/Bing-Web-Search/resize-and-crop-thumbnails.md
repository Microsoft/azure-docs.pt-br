---
title: Redimensionar e recortar miniaturas de imagens - API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como redimensionar e cortar miniaturas fornecidas pelas APIs de pesquisa do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868006"
---
# <a name="resize-and-crop-thumbnail-images"></a>Redimensionar e recortar miniaturas de imagens

Algumas respostas das APIs de pesquisa do Bing incluem URLs para imagens em miniatura servidas pelo Bing, que você pode redimensionar e cortar e podem conter parâmetros de consulta. Por exemplo:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se você exibir um subconjunto dessas miniaturas, fornecem uma opção para exibir as imagens restantes.

> [!NOTE]
> Certifique-se de que o corte e redimensionamento de imagens em miniatura fornecerá um cenário de pesquisa que respeite os direitos de terceiros, conforme exigido pela API de pesquisa do Bing [usar e aos requisitos de exibição](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Redimensionar uma miniatura 

Para redimensionar uma miniatura, Bing recomenda que você especificar apenas um de `w` (largura) ou `h` parâmetros de consulta (altura) na URL da miniatura. Especificar somente a altura ou largura permite que o Bing manter os aspectos da imagem original. Especifique a largura e altura em pixels. 

Por exemplo, se a miniatura do original é 480 x 620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E você quiser diminuir seu tamanho, defina as `w` parâmetro para um novo valor (por exemplo `336`) e remova o `h` parâmetro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se você especificar apenas a altura ou largura de uma miniatura, taxa de proporção original da imagem será mantida. Se você especificar ambos os parâmetros e a taxa de proporção não é mantida, o Bing adicionará preenchimento em branco para a borda da imagem.

Por exemplo, se você redimensionar uma imagem de 480 x 359 para 200 x 200 sem corte, a largura total contém a imagem, mas a altura contém 25 pixels de preenchimento branco nas partes superior e inferior da imagem. Se a imagem fosse de 359 x 480, as bordas esquerdas e direita conteria o preenchimento branco. Se você cortar a imagem, o preenchimento branco não será adicionado.  

A figura a seguir mostra o tamanho original de uma imagem em miniatura (480 x 300).  
  
![Imagem original de paisagem](./media/resize-crop/bing-resize-crop-landscape.png)  
  
A figura a seguir mostra a imagem redimensionada para 200 x 200. A taxa de proporção é mantida e as bordas superior e inferior são preenchidas com branco (borda preta é incluída para mostrar o preenchimento).  
  
![Imagem redimensionada de paisagem](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se você especificar dimensões maiores que a largura e altura originais da imagem, o Bing adicionará preenchimento em branco para as bordas esquerdas e superior.  

## <a name="request-different-thumbnail-sizes"></a>Solicitação de diferentes tamanhos de miniatura

Para solicitar um tamanho diferente de imagem em miniatura, remova todos os parâmetros de consulta da URL da miniatura, exceto o `id` e `pid` parâmetros. Em seguida, adicione a `&w` (largura) ou `&h` parâmetro de consulta (altura) com o tamanho da imagem desejada em pixels, mas não ambos. Bing manterá a taxa de proporção original da imagem. 

Para aumentar a largura da imagem especificada pela URL acima para 165 pixels, você usaria a seguinte URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se você solicitar uma imagem que é maior do que o tamanho da imagem original, o Bing adiciona preenchimento branco ao redor da imagem conforme necessário. Por exemplo, se a imagem original é de tamanho 474 x 316 e você definir `&w` para 500, o Bing retornará uma imagem de 500 x 333. Essa imagem terá 8,5 pixels de preenchimento branco ao longo das bordas superior e inferior e 13 pixels de preenchimento nas bordas esquerdas e direita.

Para impedir que o Bing adicionar preenchimento branco se o tamanho solicitado for maior que o tamanho da imagem original, defina o `&p` consultar parâmetro como 0. Por exemplo, se você incluir o `&p=0` parâmetro na URL anterior, o Bing retorna uma imagem de 474 x 316 em vez de uma imagem de 500 x 333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se você especificar ambos `&w` e `&h` parâmetros de consulta, o Bing manterá a taxa de proporção da imagem e adiciona o preenchimento branco conforme necessário. Por exemplo, se a imagem original é de tamanho 474 x 316 e você definir os parâmetros de largura e altura para 200 x 200 (`&w=200&h=200`), o Bing retorna uma imagem que contém a 33 pixels de preenchimento branco na parte superior e inferior. Se você incluir o `&p` parâmetro de consulta, o Bing retorna uma imagem de 200 x 134.

## <a name="crop-a-thumbnail"></a>Cortar uma miniatura 

Para cortar uma imagem, inclua o `c` parâmetro de consulta (Cortar). Você pode usar os seguintes valores:
  
- `4` &mdash; Taxa de proporção  
- `7` &mdash; Taxa de proporção inteligente  

### <a name="smart-ratio-cropping"></a>Inteligente de corte de taxa

Se você solicitar o corte da taxa de proporção inteligente (definindo o `c` parâmetro para `7`), Bing cortará uma imagem do Centro de sua região de interesse para fora, mantendo a taxa de proporção da imagem. A região de interesse é a área da imagem que o Bing determina que contém a maioria das partes de importação. A seguir é mostrado um exemplo de região de interesse.  
  
![Região de interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se você redimensiona uma imagem e solicitar o corte da taxa de proporção inteligente, o Bing reduz a imagem para o tamanho solicitado, mantendo a taxa de proporção. Bing, em seguida, corta a imagem com base nas dimensões redimensionadas. Por exemplo, se a largura redimensionada for menor ou igual à altura, o Bing cortará a imagem à esquerda e à direita do centro da região de interesse. Caso contrário, o Bing será cortá-la à parte superior e inferior do centro da região de interesse.  
  
 
A seguir é exibida a imagem reduzida para 200 x 200 usando o corte da Taxa de proporção inteligente. Como o Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é recortada. 
  
![Imagem paisagem cortada para 200 x 200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
A seguir é exibida a imagem reduzida para 200 x 100 usando o corte da Taxa de proporção inteligente. Como o Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é recortada. 
   
![Imagem paisagem cortada para 200 x 100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
A seguir é exibida a imagem reduzida para 100 x 200 usando o corte da Taxa de proporção inteligente. Como o Bing mede a imagem a partir do centro, as partes esquerdas e direita da imagem são cortadas.
  
![Imagem paisagem cortada para 100 x 200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se o Bing não puder determinar a região da imagem de interesse, o serviço usará o corte de taxa de proporção.  

### <a name="blind-ratio-cropping"></a>Corte de taxa cegos

Se você solicitar o corte da taxa de proporção (definindo o `c` parâmetro para `4`), Bing usa as seguintes regras para cortar a imagem.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, a imagem é medida a partir do canto superior esquerdo e cortada na parte inferior.  
- Se `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, a imagem é medida do centro e cortada nas partes esquerda e direita.  

A seguir é exibida uma imagem retrato 225 x 300.  
  
![Imagem de girassol original](./media/resize-crop/bing-resize-crop-sunflower.png)
  
A seguir é exibida a imagem reduzida para 200 x 200 usando o corte da Taxa de proporção automática. A imagem é medida do canto superior esquerdo resultante na parte inferior da imagem que está sendo cortada.  
  
![Imagem de girassol cortada para 200 x 200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
A seguir é exibida a imagem reduzida para 200 x 100 usando o corte da Taxa de proporção automática. A imagem é medida do canto superior esquerdo resultante na parte inferior da imagem que está sendo cortada.  
  
![Imagem de girassol cortada para 200 x 100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
A seguir é exibida a imagem reduzida para 100 x 200 usando o corte da Taxa de proporção automática. A imagem é medida a partir do centro resultando no corte das partes esquerda e direita da imagem.  
  
![Imagem de girassol cortada para 100 x 200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Próximas etapas

* [Quais são as APIs de pesquisa do Bing?](bing-api-comparison.md)
* [API de pesquisa do Bing, usar e aos requisitos de exibição](use-display-requirements.md)
