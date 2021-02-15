---
title: Adicionar uma camada do mapa de calor a um mapa | Mapas do Microsoft Azure
description: Saiba como criar um mapa de calor. Consulte como usar o SDK da Web do Azure Maps para adicionar uma camada do mapa de calor a um mapa. Descubra como personalizar as camadas do mapa de calor.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b15ee7091a68f7fcc79c71877c4af28b511b84de
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680145"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Mapas de calor, também conhecidos como mapas de densidade de ponto, são um tipo de visualização de dados. Eles são usados para representar a densidade de dados usando um intervalo de cores e mostrar os dados "pontos de acesso" em um mapa. Mapas de calor são uma ótima maneira de renderizar conjuntos de valores com um grande número de pontos. 

A renderização de dezenas de milhares de pontos como símbolos pode abranger a maior parte da área do mapa. Esse caso provavelmente resultará em muitos símbolos sobrepostos. Dificultando uma compreensão melhor dos dados. No entanto, a visualização desse mesmo conjunto de dados como um mapa de calor facilita a visualização da densidade e da densidade relativa de cada ponto de dados.

Você pode usar mapas de calor em vários cenários diferentes, incluindo:

- **Dados de temperatura**: fornece aproximações para o que é a temperatura entre dois pontos de dados.
- **Dados para sensores de ruído**: mostra não apenas a intensidade do ruído em que o sensor está, mas também pode fornecer informações sobre a dissipação ao longo de uma distância. O nível de ruído em um site pode não estar alto. Se a área de cobertura de ruído de vários sensores se sobrepuser, é possível que essa área sobreposta possa ter níveis de ruído maiores. Dessa forma, a área sobreposta estaria visível no mapa de calor.
- **Rastreamento de GPS**: inclui a velocidade como um mapa de altura ponderado, em que a intensidade de cada ponto de dados é baseada na velocidade. Por exemplo, essa funcionalidade fornece uma maneira de ver onde um veículo estava acelerando.

> [!TIP]
> Por padrão, as camadas do mapa de calor renderizam as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada para que ela só processe recursos de geometria de ponto, defina a `filter` propriedade da camada como `['==', ['geometry-type'], 'Point']` . Se você também quiser incluir recursos do MultiPoint, defina a `filter` propriedade da camada como `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` .

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Para renderizar uma fonte de dados de pontos como um mapa de calor, passe sua fonte de dados para uma instância da `HeatMapLayer` classe e adicione-a ao mapa.

No código a seguir, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Para garantir uma melhor experiência do usuário, o mapa de calor está abaixo da camada de rótulo. Os rótulos ficam visíveis claramente. Os dados neste exemplo são do programa de [riscos de terremoto USGS](https://earthquake.usgs.gov/). É para terremotos significativos que ocorreram nos últimos 30 dias.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Aqui está o exemplo de código completo em execução do código anterior.

<br/>

<iframe height='500' scrolling='no' title='Camada do Mapa de calor simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Camada do Mapa de calor Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Personalizar a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor, definindo as opções de raio e opacidade. A camada do mapa de calor fornece várias opções de personalização, incluindo:

* `radius`: Define um raio de pixel no qual processar cada ponto de dados. Você pode definir o raio como um número fixo ou como uma expressão. Usando uma expressão, você pode dimensionar o raio com base no nível de zoom e representar uma área espacial consistente no mapa (por exemplo, um raio de 5 km).
* `color`: Especifica como o mapa de calor é colorido. Um gradiente de cor é um recurso comum dos mapas de calor. Você pode obter o efeito com uma `interpolate` expressão. Você também pode usar uma `step` expressão para colorir o mapa de calor, dividindo a densidade visualmente em intervalos que se assemelham a uma contorno ou mapa de estilo de radar. Essas paletas de cores definem as cores do valor de densidade mínimo ao máximo. 

  Você especifica valores de cor para mapas de calor como uma expressão no `heatmap-density` valor. A cor da área em que não há dados definidos no índice 0 da expressão de "interpolação" ou a cor padrão de uma expressão "de nível". Você pode usar esse valor para definir uma cor de plano de fundo. Geralmente, esse valor é definido como transparente ou um preto semitransparente. 
   
  Aqui estão exemplos de expressões de cor:

  | Expressão de cor de interpolação | Expressão de cor de nível | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, ' verde ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, ' amarelo ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ' vermelho '<br/>\] |   

- `opacity`: Especifica o quão opaco ou transparente a camada do mapa de calor é.
- `intensity`: Aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade geral do calor. Isso causa uma diferença no peso dos pontos de dados, facilitando a visualização.
- `weight`: Por padrão, todos os pontos de dados têm um peso de 1 e são ponderados igualmente. A opção Weight atua como um multiplicador e você pode defini-la como um número ou uma expressão. Se um número for definido como o peso, será a equivalência de colocar cada ponto de dados no mapa duas vezes. Por exemplo, se o peso for 2, a densidade dobrará. Definir a opção de peso para um número renderiza o mapa de calor de maneira semelhante a usar a opção de intensidade. 

  No entanto, se você usar uma expressão, o peso de cada ponto de dados poderá ser baseado nas propriedades de cada ponto de dados. Por exemplo, suponha que cada ponto de dados representa um terremoto. O valor de magnitude tem sido uma métrica importante para cada ponto de dados de terremoto. Os terremotos acontecem o tempo todo, mas têm uma magnitude menor e não são percebidos. Use o valor de magnitude em uma expressão para atribuir o peso a cada ponto de dados. Usando o valor de magnitude para atribuir o peso, você obtém uma representação melhor da significância dos terremotos dentro do mapa de calor.
- `source` e `source-layer` : permite que você atualize a fonte de dados.

Aqui está uma ferramenta para testar as diferentes opções de camada do mapa de calor.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada do Mapa de Calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor com zoom consistente

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que você aplica zoom no mapa, os dados são agregados e a camada do mapa de calor é diferente. 

Use uma `zoom` expressão para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Essa expressão faz com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior. 

Dimensionar o raio para que ele fique duplo com cada nível de zoom cria um mapa de calor que se parece consistente em todos os níveis de zoom. Para aplicar esse dimensionamento, use `zoom` com uma expressão base 2 `exponential interpolation` , com o conjunto de raio de pixel definido para o nível de zoom mínimo e um raio dimensionado para o nível máximo de zoom calculado como `2 * Math.pow(2, minZoom - maxZoom)` mostrado no exemplo a seguir. Aplique zoom no mapa para ver como o mapa de calor é dimensionado com o nível de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor com zoom consistente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapa de calor com zoom consistente</a> com a caneta pelo mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando você habilita o clustering na fonte de dados, os pontos próximos uns dos outros são agrupados em conjunto como um ponto clusterizado. Você pode usar a contagem de pontos de cada cluster como a expressão de peso para o mapa de calor. Isso pode reduzir significativamente o número de pontos a serem renderizados. A contagem de pontos de um cluster é armazenada em uma `point_count` Propriedade do recurso de ponto: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de clustering for apenas alguns pixels, haveria uma pequena diferença visual na renderização. Um raio maior agrupa mais pontos em cada cluster e melhora o desempenho do calor.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HeatMapLayer](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)