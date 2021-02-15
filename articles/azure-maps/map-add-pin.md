---
title: Adicionar uma camada de símbolo a um mapa | Mapas do Microsoft Azure
description: Saiba como adicionar símbolos personalizados, como texto ou ícones, a mapas. Consulte como usar fontes de dados e camadas de símbolo no SDK para Web do Azure Maps para essa finalidade.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891047"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicionar uma camada de símbolo a um mapa

Conecte um símbolo a uma fonte de dados e use-o para renderizar um ícone ou um texto em um determinado ponto. 

As camadas de símbolo são renderizadas usando o WebGL. Use uma camada de símbolo para renderizar grandes coleções de pontos no mapa. Em comparação com o marcador HTML, a camada de símbolo renderiza um grande número de dados de ponto no mapa, com melhor desempenho. No entanto, a camada de símbolo não dá suporte a elementos CSS tradicionais e HTML para estilização.  

> [!TIP]
> Por padrão, as camadas de Símbolo renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de modo que ela só processe os recursos de geometria de ponto, defina a `filter` propriedade da camada como `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` , se desejar, você também pode incluir recursos do MultiPoint.

A imagem de mapas do Gerenciador de Sprite carrega imagens personalizadas usadas pela camada de símbolo. Ele dá suporte aos seguintes formatos de imagem:

- JPEG
- PNG
- SVG
- BMP
- GIF (sem animações)

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Para poder adicionar uma camada de símbolo ao mapa, você precisa executar algumas etapas. Primeiro, crie uma fonte de dados e adicione-a ao mapa. Crie uma camada de símbolo. Em seguida, passe a fonte de dados para a camada de símbolo para recuperar os dados da fonte de dados. Por fim, adicione dados à fonte de dados para que haja algo a ser renderizado. 

O código a seguir demonstra o que deve ser adicionado ao mapa depois que ele é carregado. Este exemplo renderiza um único ponto no mapa usando uma camada de símbolo. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Há quatro tipos diferentes de dados de ponto que podem ser adicionados ao mapa:

- Geometria de ponto geojson-esse objeto contém apenas uma coordenada de um ponto e nada mais. A `atlas.data.Point` classe auxiliar pode ser usada para criar esses objetos com facilidade.
- Geojson MultiPoint Geometry-este objeto contém as coordenadas de vários pontos e nada mais. A `atlas.data.MultiPoint` classe auxiliar pode ser usada para criar esses objetos com facilidade.
- Recurso geojson – esse objeto consiste em qualquer geometria geojson e um conjunto de propriedades que contêm metadados associados à geometria. A `atlas.data.Feature` classe auxiliar pode ser usada para criar esses objetos com facilidade.
- `atlas.Shape` a classe é semelhante ao recurso geojson. Ambos consistem em uma geometria geojson e um conjunto de propriedades que contêm metadados associados à geometria. Se um objeto geojson for adicionado a uma fonte de dados, ele poderá ser facilmente renderizado em uma camada. No entanto, se a propriedade coordenadas desse objeto geojson for atualizada, a fonte de dados e o mapa não serão alterados. Isso porque não há nenhum mecanismo no objeto JSON para disparar uma atualização. A classe Shape fornece funções para atualizar os dados que ela contém. Quando uma alteração é feita, a fonte de dados e o mapa são automaticamente notificados e atualizados. 

O exemplo de código a seguir cria uma geometria de ponto geojson e a passa para a `atlas.Shape` classe para facilitar a atualização. O centro do mapa é usado inicialmente para renderizar um símbolo. Um evento de clique é adicionado ao mapa de tal forma que, quando ele é acionado, as coordenadas do mouse são usadas com a função Shapes `setCoordinates` . As coordenadas do mouse são registradas no momento do evento de clique. Em seguida, o `setCoordinates` atualiza o local do símbolo no mapa.

<br/>

<iframe height='500' scrolling='no' title='Mudar localização de fixação' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Mudar localização de fixação</a> por Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, as camadas de símbolo otimizam a renderização de símbolos, ocultando os símbolos que se sobrepõem. À medida que você amplia, os símbolos ocultos se tornam visíveis. Para desabilitar esse recurso e renderizar todos os símbolos em todos os momentos, defina a `allowOverlap` propriedade das `iconOptions` opções como `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicionar um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas usando o WebGL. Assim, todos os recursos, como imagens de ícone, precisam ser carregados no contexto do WebGL. Este exemplo mostra como adicionar um ícone personalizado aos recursos de mapa. Esse ícone é usado para renderizar dados de ponto com um símbolo personalizado no mapa. A propriedade `textField` da camada de símbolo exige a especificação de uma expressão. Nesse caso, queremos renderizar a propriedade de temperatura. Como a temperatura é um número, ela precisa ser convertida em uma cadeia de caracteres. Além disso, queremos acrescentar "° f" a ele. Uma expressão pode ser usada para fazer essa concatenação; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ícone de Imagem de Símbolo Personalizado' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Ícone de Imagem do Símbolo Personalizado</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK da Web do Azure Mapas fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, confira o documento [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-symbol-layer"></a>Personalizar uma camada de símbolo 

A camada de símbolo tem muitas opções de estilo disponíveis. Veja a seguir uma ferramenta para testar essas várias opções de estilo.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada de símbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opções de Camada de Símbolo</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando você quiser renderizar apenas o texto com uma camada de símbolo, poderá ocultar o ícone definindo a `image` propriedade das opções de ícone como `'none'` .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

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
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar marcadores HTML](map-add-bubble-layer.md)