---
title: Adicionar uma camada de bolha para o Azure Mapas | Microsoft Docs
description: Como adicionar uma camada de bolha ao mapa em Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769678"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Adicionar uma camada de bolha a um mapa

Este artigo mostra como você pode renderizar os dados de ponto de uma fonte de dados como uma camada de bolha em um mapa. As camadas de bolhas renderizam pontos como círculos no mapa com raio de pixel fixo. 

> [!TIP]
> As camadas de bolha por padrão processarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de tal forma que ele só processa geometria de ponto de conjunto de recursos do `filter` propriedade da camada para `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se você quiser incluir recursos MultiPoint também.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

<iframe height='500' scrolling='no' title='DataSource BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>DataSource BubbleLayer</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, uma matriz de objetos de [Ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) é definida e adicionada ao objeto do [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest).

Uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderiza dados baseados em ponto encapsulados na [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como círculos no mapa. O último bloco de código cria uma camada de bolha e adiciona-a ao mapa. Confira as propriedades de uma camada de bolha em [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A matriz de objetos de Ponto, a fonte de dados e a camada de bolha são criados e adicionados ao mapa dentro da função [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o círculo seja exibido depois de o mapa ser completamente carregado.

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos de uma camada de bolha

<iframe height='500' scrolling='no' title='DataSource MultiLayer' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>DataSource MultiLayer</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima mostra como visualizar e rotular dados no mapa. O primeiro bloco de código acima constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um objeto [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Em seguida, ele cria um objeto de fonte de dados usando a classe [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona o ponto à fonte de dados.

Uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderiza dados baseados em ponto encapsulados na [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como círculos no mapa. O terceiro bloco de código cria uma camada de bolha e adiciona-a ao mapa. Confira as propriedades de uma camada de bolha em [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O último bloco de código cria e adiciona uma camada de símbolo ao mapa que renderiza o rótulo de texto para a bolha. Veja as propriedades de uma camada de símbolo em [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

A fonte de dados e as camadas são criadas e adicionados ao mapa dentro da função [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os dados sejam exibidos após o mapa ser totalmente carregado.

## <a name="customize-a-bubble-layer"></a>Personalizar uma camada de bolha

A camada de bolha tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bolha' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opções de Camada de Bolhas</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)