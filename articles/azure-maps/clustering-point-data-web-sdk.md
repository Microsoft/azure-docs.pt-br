---
title: Clustering de ponto de dados em mapas do Azure | Microsoft Docs
description: Como os dados de ponto de cluster no SDK da Web
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795938"
---
# <a name="clustering-point-data"></a>Clustering de ponto de dados

Ao visualizar o muitos pontos de dados no mapa, pontos se sobrepõem uns aos outros, o mapa parece desorganizado e torna difícil ver e usar. Clustering de ponto de dados pode ser usado para melhorar a experiência do usuário. Clustering de ponto de dados é o processo de combinação de ponto de dados que estão próximos uns dos outros e que representa-los no mapa como um ponto de dados em cluster único. Como o usuário aplica zoom no mapa, os clusters dividir em seus pontos de dados individuais.

## <a name="enabling-clustering-on-a-data-source"></a>Habilitando o clustering em uma fonte de dados

Clustering facilmente pode ser habilitado na `DataSource` classe, definindo o `cluster` opção como true. Além disso, o raio de pixel para selecionar pontos próximos para combinar em um cluster pode ser definido usando o `clusterRadius` e um nível de zoom pode ser especificado em que se desabilitam a lógica de clustering usando o `clusterMaxZoom` opção. Aqui está um exemplo de como habilitar o clustering em uma fonte de dados.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Se dois pontos de dados estiverem próximos uns dos outros no chão, é possível que o cluster será nunca separar, não importa quão próximo do usuário mais zoom. Para resolver isso, você pode definir o `clusterMaxZoom` opção da fonte de dados que especifica o nível de zoom para desabilitar a lógica de clustering e simplesmente exibir tudo.

O `DataSource` classe também tem os seguintes métodos relacionados ao cluster:

| Método | Tipo de retorno | DESCRIÇÃO |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promessa&lt;recurso&lt;geometria, qualquer&gt; \| forma&gt; | Recupera os filhos de um determinado cluster no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters será recursos com propriedades que correspondem a ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | Calcula um nível de zoom no qual o cluster comece expandindo ou separar. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promessa&lt;recurso&lt;geometria, qualquer&gt; \| forma&gt; | Recupera todos os pontos em um cluster. Definir a `limit` para retornar um subconjunto dos pontos e, em seguida, usar o `offset` à página por meio de pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Clusters de exibição usando uma camada de bolha

Uma camada de bolha é uma ótima maneira de renderizar pontos em cluster, como você pode facilmente dimensionar o raio e alterar a cor com base no número de pontos no cluster usando uma expressão. Ao exibir os clusters usando uma camada de bolha, você também deve usar uma camada separada para a renderização de pontos de dados não clusterizado. Geralmente é bom também é possível exibir o tamanho do cluster na parte superior de bolhas. Uma camada de símbolo com texto e nenhum ícone pode ser usada para obter esse comportamento. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de bolhas básico de clustering" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering de camada de bolhas básico</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Clusters de exibição usando uma camada de símbolo

Ao visualizar os dados de ponto usando a camada de símbolo, por padrão, que ela ocultará automaticamente símbolos que se sobrepõem uns aos outros para criar uma experiência mais limpa, porém isso pode não ser a experiência desejada se você quiser ver a densidade de dados aponta no mapa. Definindo o `allowOverlap` opção das camadas de símbolo `iconOptions` propriedade `true` desabilita essa experiência, mas resultará em todos os símbolos que está sendo exibidos. Usando o clustering permite que você veja a densidade de todos os dados durante a criação de uma experiência de usuário limpa interessante. Neste exemplo, símbolos personalizados serão usados para representar os pontos de dados individuais e clusters.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo em cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>camada clusterizado símbolo</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e o calor mapeia camada

Mapas de calor são uma ótima maneira de exibir a densidade de dados no mapa. Essa visualização pode lidar com um grande número de pontos de dados por conta própria, mas ele pode manipular dados ainda mais se os pontos de dados estiverem em cluster e o tamanho do cluster é usado como o peso do mapa de calor. Defina as `weight` opção da camada do mapa de calor para `['get', 'point_count']` para fazer isso. Quando o raio do cluster é pequeno, o mapa de calor será quase idêntico a um mapa de calor usando os pontos de dados não clusterizado, mas terá um desempenho muito melhor. No entanto, quanto menor o raio de cluster, mais precisa o mapa de calor será, mas com menos de um desempenho se beneficiar.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor ponderada de cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Cluster ponderado mapa de calor</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos de mouse nos pontos de dados em cluster

Quando ocorrem eventos de mouse em uma camada que contêm pontos de dados em cluster, o ponto de dados em cluster será retornado para o evento como um objeto de recurso de ponto GeoJSON. Esse recurso de ponto terá as seguintes propriedades:

| Nome da propriedade | Type | DESCRIÇÃO |
|---------------|------|-------------|
| cluster | boolean | Indica se o recurso representa um cluster. |
| cluster_id | string | Uma ID exclusiva para o cluster que pode ser usado com a fonte de dados `getClusterExpansionZoom`, `getClusterChildren`, e `getClusterLeaves` métodos. |
| point_count | número | O número de pontos que contém o cluster. |
| point_count_abbreviated | string | Uma cadeia de caracteres que abrevia o valor de point_count se ela for longa. (por exemplo, 4.000 se torna 4K) |

Este exemplo usa uma camada de bolha que renderiza os pontos de cluster e adiciona um evento de clique que quando disparado, calcular e aplique zoom no mapa para o próximo nível de zoom no qual o cluster será interrompido entre eles usando o `getClusterExpansionZoom` método da `DataSource` classe e o `cluster_id` ponto de dados em cluster de propriedade do clicado. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Área de exibição do cluster 

Os dados de ponto que representa um cluster são difundidos por uma área. Neste exemplo, quando o mouse é colocado em um cluster, os dados individuais de pontos que ele contém (folhas) serão usados para calcular uma forma convexa e exibidos no mapa para mostrar a área. Todos os pontos contidos em um cluster podem ser recuperados da fonte de dados usando o `getClusterLeaves` método. Uma forma convexa é um polígono que encapsula um conjunto de pontos, como uma banda Elástico e pode ser calculado usando o `atlas.math.getConvexHull` método.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Forma convexa de área de cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>casco convexo de área de Cluster</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Classe de fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objeto de DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)
