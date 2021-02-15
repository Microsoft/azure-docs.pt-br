---
title: Adicionar uma camada de linhas a um mapa | Microsoft Azure Mapas
description: Saiba como adicionar linhas a mapas. Veja exemplos que usam o SDK da Web do Azure Maps para adicionar camadas de linha a mapas e personalizar linhas com símbolos e gradientes de cor.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9cf2f4876d62572ace118562fe5760f2b4010fa5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891152"
---
# <a name="add-a-line-layer-to-the-map"></a>Adicionar uma camada de linhas ao mapa

Uma camada de linhas pode ser usada para renderizar os recursos `LineString` e `MultiLineString` como caminhos ou rotas no mapa. Uma camada de linhas também pode ser usada para renderizar a estrutura de tópicos dos recursos `Polygon` e `MultiPolygon`. Uma fonte de dados é conectada a uma camada de linhas para fornecer a ela dados a serem renderizados. 

> [!TIP]
> Por padrão, as camadas de linhas renderizarão as coordenadas dos polígonos e das linhas em uma fonte de dados. Para limitar a camada de modo que apenas ela renderize os recursos de LineString, defina a propriedade `filter` da camada como `['==', ['geometry-type'], 'LineString']` ou `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`, se desejar incluir recursos MultiLineString também.

O código a seguir mostra como criar uma linha. Adicione a linha a uma fonte de dados e, em seguida, renderize-a com uma camada de linhas usando a classe [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer).

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicionar uma linha a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Camadas de linha podem ser estilizadas usando [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) e [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Adicionar símbolos ao longo de uma linha

Este exemplo demonstra como adicionar ícones de seta ao longo de uma linha no mapa. Ao usar uma camada de símbolo, defina a opção "posicionamento" como "linha". Essa opção renderizará os símbolos ao longo da linha e vai girar os ícones (0 grau = direita).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar a seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Mostrar a seta ao longo da linha</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK da Web do Azure Mapas fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, confira o documento [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md).

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Adicionar um gradiente de traço a uma linha

Você pode aplicar uma cor de traço único a uma linha. Você também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para o outro. Por exemplo, gradientes de linha podem ser usados para representar alterações ao longo do tempo e de acordo com a distância ou temperaturas diferentes em uma linha de objetos conectada. Para aplicar esse recurso a uma linha, a fonte de dados precisa ter a opção `lineMetrics` definida como true e, em seguida, uma expressão de gradiente de cor pode ser passada para a opção `strokeColor` da linha. A expressão de gradiente de traço precisa fazer referência à expressão de dados `['line-progress']` que expõe as métricas de linha calculada para a expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com Gradiente de Traço" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Linha com Gradiente de Traço</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

A Camada de linhas tem várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada de Linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opções de Camada de Linha</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)