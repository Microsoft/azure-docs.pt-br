---
title: Adicionar uma camada de dados simples | Mapas do Microsoft Azure
description: Saiba como adicionar uma camada de dados simples usando o módulo de e/s espacial, fornecido pelo SDK da Web do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896150"
---
# <a name="add-a-simple-data-layer"></a>Adicionar uma camada de dados simples

O módulo de e/s espacial fornece uma `SimpleDataLayer` classe. Essa classe facilita a renderização de recursos com estilo no mapa. Ele pode até mesmo renderizar conjuntos de dados que têm propriedades de estilo e conjuntos de dados que contêm tipos de geometria misturados. A camada de dados simples alcança essa funcionalidade encapsulando várias camadas de renderização e usando expressões de estilo. As expressões de estilo pesquisam propriedades de estilo comuns dos recursos dentro dessas camadas encapsuladas. A `atlas.io.read` função e a `atlas.io.write` função usam essas propriedades para ler e gravar estilos em um formato de arquivo com suporte. Depois de adicionar as propriedades a um formato de arquivo com suporte, o arquivo pode ser usado para várias finalidades. Por exemplo, o arquivo pode ser usado para exibir os recursos com estilo no mapa.

Além dos recursos de estilo, o `SimpleDataLayer` fornece um recurso pop-up interno com um modelo pop-up. O pop-up é exibido quando um recurso é clicado. O recurso de pop-up padrão pode ser desabilitado, se desejado. Essa camada também dá suporte a dados clusterizados. Quando um cluster é clicado, o mapa ampliará o cluster e o expandirá em pontos individuais e subgrupos.

A `SimpleDataLayer` classe destina-se a ser usada em grandes conjuntos de dados com muitos tipos de geometria e muitos estilos aplicados nos recursos. Quando usado, essa classe adiciona uma sobrecarga de seis camadas que contêm expressões de estilo. Portanto, há casos em que é mais eficiente usar as camadas de renderização principal. Por exemplo, use uma camada principal para renderizar alguns tipos de geometria e alguns estilos em um recurso

## <a name="use-a-simple-data-layer"></a>Usar uma camada de dados simples

A `SimpleDataLayer` classe é usada como as outras camadas de renderização usadas. O código a seguir mostra como usar uma camada de dados simples em um mapa:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Adicione recursos à fonte de dados. Em seguida, a camada de dados simples descobrirá a melhor forma de renderizar os recursos. Os estilos de recursos individuais podem ser definidos como propriedades no recurso. O código a seguir mostra um recurso de ponto geojson com uma `color` propriedade definida como `red` . 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

O código a seguir processa o recurso de ponto acima usando a camada de dados simples. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Usar a camada de dados simples" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>usar a camada de dados simples</a> pelo mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

A verdadeira potência da camada de dados simples é exibida quando:

- Há vários tipos diferentes de recursos em uma fonte de dados; or
- Os recursos no conjunto de dados têm várias propriedades de estilo individualmente definidas. or
- Você não tem certeza do que o conjunto de dados contém exatamente.

Por exemplo, ao analisar feeds de dados XML, talvez você não conheça os estilos e tipos de geometria exatos dos recursos. O exemplo a seguir mostra a potência da camada de dados simples renderizando os recursos de um arquivo KML. Ele também demonstra várias opções fornecidas pela classe de camada de dados simples.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de camada de dados simples" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Consulte as <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>Opções da camada de dados simples</a> da caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Essa camada de dados simples usa a classe de [modelo Popup](map-add-popup.md#add-popup-templates-to-the-map) para exibir os balões de KML ou as propriedades de recurso como uma tabela. Por padrão, todo o conteúdo renderizado no pop-up será colocado em área restrita dentro de um iframe como um recurso de segurança. No entanto, há limitações:
>
> - Todos os scripts, formulários, bloqueio de ponteiro e funcionalidade de navegação superior estão desabilitados. Os links podem ser abertos em uma nova guia quando clicado. 
> - Os navegadores mais antigos que não suportam o `srcdoc` parâmetro em IFrames serão limitados a renderizar uma pequena quantidade de conteúdo.
> 
> Se você confiar nos dados que estão sendo carregados nos pop-ups e potencialmente desejar que esses scripts carregados em pop-ups possam acessar seu aplicativo, você pode desabilitar isso definindo a opção de modelos pop-up `sandboxContent` como false. 

## <a name="default-supported-style-properties"></a>Propriedades de estilo com suporte padrão

Como mencionado anteriormente, a camada de dados simples encapsula várias das camadas de renderização principais: bolha, símbolo, linha, polígono e polígono extrudada. Em seguida, ele usa expressões para pesquisar Propriedades de estilo válidas em recursos individuais.

As propriedades de estilo do Azure Maps e do GitHub são os dois conjuntos principais de nomes de propriedade com suporte. A maioria dos nomes de propriedade das diferentes opções de camada do Azure Maps tem suporte como propriedades de estilo de recursos na camada de dados simples. As expressões foram adicionadas a algumas opções de camada para dar suporte a nomes de propriedade de estilo que são comumente usados pelo GitHub. Esses nomes de propriedade são definidos pelo [suporte de mapa geojson do GitHub](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)e são usados para estilizar arquivos geojson que são armazenados e renderizados na plataforma. Todas as propriedades de estilo do GitHub têm suporte na camada de dados simples, exceto as `marker-symbol` Propriedades de estilo.

Se o leitor vier em uma propriedade de estilo menos comum, ele irá convertê-lo para a propriedade de estilo do Azure Maps mais próxima. Além disso, as expressões de estilo padrão podem ser substituídas usando a `getLayers` função da camada de dados simples e atualizando as opções em qualquer uma das camadas.

As seções a seguir fornecem detalhes sobre as propriedades de estilo padrão que são suportadas pela camada de dados simples. A ordem do nome da propriedade com suporte também é a prioridade da propriedade. Se duas propriedades de estilo forem definidas para a mesma opção de camada, a primeira na lista terá precedência mais alta. As cores podem ser qualquer valor de cor do CSS3; HEX, RGB, RGBA, HSL, HSLA ou valor de cor nomeada.

### <a name="bubble-layer-style-properties"></a>Propriedades de estilo da camada de bolha

Se um recurso for um `Point` ou a `MultiPoint` , e o recurso não tiver uma `image` propriedade que seria usada como um ícone personalizado para renderizar o ponto como um símbolo, o recurso será renderizado com um `BubbleLayer` .

| Opção de camada | Nome (s) de propriedade com suporte | Valor padrão |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] os `size` `scale` valores e são considerados valores escalares e serão multiplicados por `8`

\[2 \] se a `marker-size` opção GitHub for especificada, os valores a seguir serão usados para o raio.

| Tamanho do marcador | Raio |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Os clusters também são renderizados usando a camada de bolha. Por padrão, o raio de um cluster é definido como `16` . A cor do cluster varia dependendo do número de pontos no cluster, conforme definido abaixo:

| n º de pontos | Cor    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 254     | `green`  |

### <a name="symbol-style-properties"></a>Propriedades de estilo de símbolo

Se um recurso for um `Point` `MultiPoint` , e o recurso e tiver uma `image` propriedade que seria usada como um ícone personalizado para renderizar o ponto como um símbolo, o recurso será renderizado com um `SymbolLayer` .

| Opção de camada | Nome (s) de propriedade com suporte | Valor padrão |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] se a opção do GitHub `marker-size` for especificada, os valores a seguir serão usados para a opção tamanho do ícone.

| Tamanho do marcador | Tamanho do símbolo |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Se o recurso de ponto for um cluster, a `point_count_abbreviated` propriedade será renderizada como um rótulo de texto. Nenhuma imagem será renderizada.

### <a name="line-style-properties"></a>Propriedades de estilo da linha

Se o recurso for um `LineString` , `MultiLineString` , `Polygon` ou `MultiPolygon` , o recurso será renderizado com um `LineLayer` .

| Opção de camada | Nome (s) de propriedade com suporte | Valor padrão |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Propriedades de estilo do polígono

Se o recurso for um `Polygon` ou a `MultiPolygon` , e o recurso não tiver uma `height` propriedade ou a `height` propriedade for zero, o recurso será renderizado com um `PolygonLayer` .

| Opção de camada | Nome (s) de propriedade com suporte | Valor padrão |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Propriedades de estilo de polígono extrudada

Se o recurso for um `Polygon` ou a `MultiPolygon` e tiver uma `height` propriedade com um valor maior que 0, o recurso será renderizado com um `PolygonExtrusionLayer` .

| Opção de camada | Nome (s) de propriedade com suporte | Valor padrão |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Ler e gravar dados espaciais](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Conectar-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Aproveitar as principais operações](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados com suporte](spatial-io-supported-data-format-details.md)