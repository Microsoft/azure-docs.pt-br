---
title: Adicionar uma forma com Mapas do Azure | Microsoft Docs
description: Como adicionar uma forma a um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769508"
---
# <a name="add-a-shape-to-a-map"></a>Adicionar uma forma a um mapa

Este artigo mostra como renderizar as geometrias no mapa usando camadas de linha e o polígono. SDK da Web de mapas do Azure também oferece suporte à criação de geometrias do círculo conforme definido na [esquema estendido do GeoJSON](extend-geojson.md#circle). Todas as geometrias de recurso também podem ser facilmente atualizadas se empacotado com o [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) classe.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Adicionar linhas ao mapa

`LineString` e `MultiLineString` recursos são usados para representar caminhos e estruturas de tópicos no mapa.

### <a name="add-a-line"></a>Adicionar uma linha

<iframe height='500' scrolling='no' title='Adicionar uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicionar uma linha a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um objeto [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) é criado e adicionado à fonte de dados.

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza objetos de linha envolvidos na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). O último bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). A fonte de dados e a camada de linha são criados e adicionados ao mapa de dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que a linha é exibida depois que o mapa totalmente carregada.

### <a name="add-symbols-along-a-line"></a>Adicionar símbolos ao longo da linha

Este exemplo mostra como adicionar ícones de seta ao longo da linha do mapa. Quando usando uma camada de símbolo, defina a opção de "posicionamento" como "linha", isso irá renderizar os símbolos ao longo da linha e girar os ícones (0 graus = right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar a seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show seta ao longo da linha</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Adicionar um gradiente de traço para uma linha

Além de poder aplicar uma cor de traço único para uma linha você também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para a próxima. Por exemplo, gradientes de linha podem ser usados para representam as alterações de tempo e distância ou temperaturas diferentes em uma linha conectados de objetos. Para aplicar esse recurso a uma linha, a fonte de dados deve ter o `lineMetrics` opção definido como true e, em seguida, uma expressão de gradiente de cor pode ser passada para o `strokeColor` opção da linha. A expressão de gradiente do traço tem a referência a `['line-progress']` expressão de dados que expõe as métricas de linha calculada como a expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com gradiente de traço" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>linha com gradiente traço</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

Várias opções de estilo de camada de linha. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada de Linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opções de Camada de Linha</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Adicionar um polígono do mapa

`Polygon` e `MultiPolygon` recursos geralmente são usados para representar uma área em um mapa. 

### <a name="use-a-polygon-layer"></a>Usar uma camada de polígono 

Uma camada de polígono renderiza a área de um polígono. 

<iframe height='500' scrolling='no' title='Adicionar um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicionar um polígono a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criado a partir de uma matriz de coordenadas e adicionado à fonte de dados. 

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígono ao mapa. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A fonte de dados e a camada de polígono são criados e adicionados ao mapa de dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o polígono é exibido depois que o mapa totalmente carregada.

### <a name="use-a-polygon-and-line-layer-together"></a>Usar uma camada de polígonos e linhas juntas

Uma camada de linha pode ser usada para renderizar o contorno de um polígono. 

<iframe height='500' scrolling='no' title='Camada de polígonos e linhas para adicionar o polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polígono e a camada de linha para adicionar o polígono</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criado a partir de uma matriz de coordenadas e adicionado à fonte de dados. 

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) é uma matriz de linhas. Consulte as propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). O terceiro bloco de código cria camadas de polígonos e linhas.

O último bloco de código adiciona as camadas de polígono e linha ao mapa. A fonte de dados e as camadas são criadas e adicionadas ao mapa de dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o polígono é exibido depois que o mapa totalmente carregada.

> [!TIP]
> Camadas de linha por padrão processará as coordenadas de polígonos, bem como as linhas em uma fonte de dados. Para limitar a camada de tal forma que ele processa somente LineString apresenta o conjunto de `filter` propriedade da camada para `['==', ['geometry-type'], 'LineString']` ou `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` se você quiser incluir MultiLineString recursos também.

### <a name="fill-a-polygon-with-a-pattern"></a>Preencher um polígono com um padrão

Além de preencher um polígono com uma cor de um padrão de imagem também pode ser usado. Carregar um padrão de imagem aos recursos de sprite mapas de imagem e, em seguida, fazer referência a esta imagem com o `fillPattern` propriedade da camada de polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de preenchimento de polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>padrão de preenchimento de polígono</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Personalizar uma camada de polígono

A camada de polígono tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo no mapa

Mapas do Azure usa uma versão estendida do esquema GeoJSON que fornece uma definição para círculos conforme observado [aqui](extend-geojson.md#circle). Um círculo pode ser renderizado no mapa com a criação de um `Point` recurso que tem um `subType` propriedade com um valor de `"Circle"` e um `radius` propriedade que tem um número que representa o raio em metros. Por exemplo: 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

O SDK Web do Azure mapas converte esses `Pooint` recursos em `Polygon` recursos nos bastidores e pode ser renderizado no mapa usando camadas de polígonos e linhas, conforme mostrado aqui.

<iframe height='500' scrolling='no' title='Adicionar um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicionar um círculo a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um círculo é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e tem um `subType` propriedade definida como `"Circle"` e um `radius` valor da propriedade em metros. Quando um recurso de ponto com um `subType` de `"Circle"` é adicionado a uma fonte de dados, ele convertido em um polígono circular dentro do mapa.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígono ao mapa. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A fonte de dados e a camada de polígono são criados e adicionados ao mapa de dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o círculo é exibido depois que o mapa totalmente carregada.

## <a name="make-a-geometry-easy-to-update"></a>Facilite a atualizar de uma geometria

Um `Shape` classe encapsula uma [geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e torna mais fácil atualizar e mantê-las.
`new Shape(data: Feature<data.Geometry, any>)` constrói um objeto de forma e o inicializa com o recurso especificado.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pena <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Atualizar propriedades de forma</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

Um ponto é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [aponte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a classe. O segundo bloco de código inicializa o valor do raio para o elemento de controle deslizante de HTML e, em seguida, constrói e envolve um objeto de ponto em um objeto de classe [Forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

O terceiro bloco de código cria uma função que pega o valor do elemento slider do intervalo HTML e altera o valor do raio usando o método [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) da classe shape.

No quarto bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). O ponto é adicionado à fonte de dados.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O terceiro bloco de código cria uma camada de polígono. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A camada de polígono, o manipulador de eventos e a fonte de dados são criados e adicionados ao mapa de dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o ponto é exibido depois que o mapa totalmente carregada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
