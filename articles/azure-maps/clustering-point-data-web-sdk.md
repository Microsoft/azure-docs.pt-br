---
title: Clustering de dados de ponto em um mapa | Microsoft Azure Mapas
description: Saiba como agrupar dados de ponto em mapas. Consulte como usar o SDK da Web do Azure Maps para dados de cluster, reagir a eventos de mouse de cluster e exibir agregações de cluster.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: f798b4a7964eba112ed9948b2db4f6d5c3edd1e3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608350"
---
# <a name="clustering-point-data"></a>Clustering de dados de ponto

Ao visualizar muitos pontos de dados no mapa, eles poderão se sobrepor uns aos outros. A sobreposição pode fazer com que o mapa se torne ilegível e difícil de usar. O clustering de dados de ponto são o processo de combinar dados de ponto que estão próximos uns dos outros e os representar no mapa como um ponto de dados clusterizado individual. À medida que o usuário aplica zoom ao mapa, os clusters se dividem em seus pontos de dados individuais. Quando você trabalha com um grande número de pontos de dados, use os processos de clustering para melhorar a experiência do usuário.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny]

## <a name="enabling-clustering-on-a-data-source"></a>Habilitar o clustering na fonte de dados

Habilite o clustering na `DataSource` classe definindo a `cluster` opção como `true` . Defina `clusterRadius` para selecionar pontos próximos e os combinar em um cluster. O valor de `clusterRadius` está em pixels. Use `clusterMaxZoom` para especificar um nível de zoom no qual desabilitar a lógica de clustering. Aqui está um exemplo de como habilitar o clustering em uma fonte de dados.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,
    
    //The radius in pixels to cluster points together.
    clusterRadius: 45,
    
    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Se dois pontos de dados estiverem próximos no solo, possivelmente o cluster nunca será dividido, independentemente de quanto o usuário ampliar. Para resolver isso, você pode definir a opção `clusterMaxZoom` para desabilitar a lógica de clustering e simplesmente exibir tudo.

A `DataSource` classe também fornece os seguintes métodos relacionados ao clustering.

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promessa&lt;Matriz&lt;Recurso&lt;Geometria, qualquer&gt; \| Forma&gt;&gt; | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes a ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | Calcula um nível de zoom no qual o cluster começará a se expandir ou separar. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promessa&lt;Matriz&lt;Recurso&lt;Geometria, qualquer&gt; \| Forma&gt;&gt; | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use `offset` para paginar os pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Exibir clusters usando uma camada de bolha

Uma camada de bolha é uma ótima maneira de renderizar os pontos clusterizados. Use as expressões para dimensionar o raio e alterar a cor com base no número de pontos no cluster. Se você exibir clusters usando uma camada de bolha, deverá usar uma camada separada para renderizar pontos de dados não clusterizados.

Para exibir o tamanho do cluster na parte superior da bolha, use uma camada de símbolo com texto e não use um ícone.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering de camada de bolha básica" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Clustering de camada de bolha básica</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Exibir clusters usando uma camada de símbolo

Ao visualizar pontos de dados, a camada de símbolo oculta automaticamente os símbolos que se sobrepõem para garantir uma interface do usuário mais limpa. Esse comportamento padrão poderá ser indesejável se você quiser mostrar a densidade dos pontos de dados no mapa. No entanto, a maioria dessas configurações pode ser alterada. Para exibir todos os símbolos, defina a opção `allowOverlap` da propriedade `iconOptions` das camadas de Símbolo como `true`. 

Use o clustering para mostrar a densidade dos pontos de dados enquanto mantém uma interface do usuário limpa. O exemplo a seguir mostra como adicionar símbolos personalizados e representar clusters e pontos de dados individuais usando a camada de símbolo.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de Símbolo clusterizado" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>Camada de Símbolo clusterizado</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e a camada de mapas de calor

Mapas de calor são uma ótima maneira de exibir a densidade dos dados no mapa. Esse método de visualização processa um grande número de pontos de dados por conta própria. Se os pontos de dados forem clusterizados e o tamanho do cluster for usado como o peso do mapa de calor, o mapa de calor poderá lidar com ainda mais dados. Para obter essa opção, defina a opção `weight` da camada do mapa de calor como `['get', 'point_count']`. Quando o raio do cluster for pequeno, o mapa de calor será praticamente idêntico a um mapa de calor que usa os pontos de dados não clusterizados, mas ele terá um desempenho muito melhor. No entanto, quanto menor for o raio do cluster, mais preciso será o mapa de calor, mas com menos benefícios de desempenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de Calor ponderado do cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Mapa de Calor ponderado do cluster</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos de mouse em pontos de dados clusterizados

Quando ocorrem eventos de mouse em uma camada que contém pontos de dados clusterizados, o ponto de dados clusterizado retorna ao evento como um objeto de recurso de ponto do GeoJSON. O recurso de ponto terá as seguintes propriedades:

| Nome da propriedade             | Type    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | booleano | Indica se o recurso representa um cluster. |
| `cluster_id`              | string  | Uma ID exclusiva para o cluster que pode ser usada com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves`. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma cadeia de caracteres que abreviará o valor `point_count`, se ele for longo. (por exemplo, 4.000 torna-se 4K)  |

Este exemplo usa uma camada de bolha que renderiza pontos de cluster e adiciona um evento de clique. Quando o evento de clique é disparado, o código calcula e amplia o mapa para o próximo nível de zoom, no qual o cluster se separa. Essa funcionalidade é implementada usando o método `getClusterExpansionZoom` da classe `DataSource` e a propriedade `cluster_id` do ponto de dados clusterizado clicado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Exibir a área do cluster 

Os dados de ponto que um cluster representa são distribuídos por uma área. Neste exemplo, ao passar o mouse sobre um cluster, ocorrem dois comportamentos principais. Primeiro, os pontos de dados individuais contidos no cluster serão usados para calcular uma envoltória convexa. Em seguida, a envoltória convexa será exibida no mapa para mostrar uma área.  Uma envoltória convexa é um polígono que encapsula um conjunto de pontos como uma banda elástica e pode ser calculada usando o método `atlas.math.getConvexHull`. Todos os pontos contidos em um cluster podem ser recuperados da fonte de dados usando o método `getClusterLeaves`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Envoltória convexa da área de cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>Envoltória convexa da área de cluster</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregando dados em clusters

Geralmente, os clusters são representados usando um símbolo com o número de pontos que estão dentro do cluster. Mas, às vezes, é desejável personalizar o estilo dos clusters com métricas adicionais. Com as agregações de cluster, as propriedades personalizadas podem ser criadas e populadas usando um cálculo de [expressão de agregação](data-driven-style-expressions-web-sdk.md#aggregate-expression).  As agregações de cluster podem ser definidas na opção `clusterProperties` do `DataSource`.

O exemplo a seguir usa uma expressão de agregação. O código calcula uma contagem com base na propriedade de tipo de entidade de cada ponto de dados em um cluster. Quando um usuário clica em um cluster, um pop-up é exibido com informações adicionais sobre o cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregações de cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>Agregações de cluster</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Classe DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [Objeto DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [Namespace atlas.math](/javascript/api/azure-maps-control/atlas.math)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)