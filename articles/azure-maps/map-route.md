---
title: Mostrar direções de rota em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a exibir as instruções entre dois locais em um mapa usando o SDK da Web do Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890710"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar direções de A para B

Este artigo mostra como fazer uma solicitação de rota e mostrar a rota no mapa.

Há duas maneiras de fazer isso. A primeira maneira é a consultar a [API de Rotas do Azure Mapas](/rest/api/maps/route/getroutedirections) por meio de um módulo de serviço. A segunda maneira é usar a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a API de [rota do Azure Maps](/rest/api/maps/route/getroutedirections). As duas formas são discutidas abaixo.

## <a name="query-the-route-via-service-module"></a>A rota por meio do módulo de serviço de consulta

<iframe height='500' scrolling='no' title='Mostrar direções de A para B em um mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Mostrar rotas de A para B em um mapa (Módulo de Serviço)</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar solicitações HTTP para o Azure Mapas com o token de acesso. Em seguida, ele passa a `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline). O `routeURL` representa uma URL para as operações de [rota](/rest/api/maps/route) do Azure Mapas.

O terceiro bloco de código cria e adiciona um objeto [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) ao mapa.

O quarto bloco de código cria objetos Start e End [Points](/javascript/api/azure-maps-control/atlas.data.point) e os adiciona ao objeto DataSource.

Uma linha é um [recurso](/javascript/api/azure-maps-control/atlas.data.feature) para LineString. Um [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) renderiza objetos de linha agrupados no [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linhas em [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Uma [camada de símbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa textos ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource). Os textos ou os ícones são renderizados como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa.

O sexto bloco de código consulta o serviço de roteamento do Azure Maps, que faz parte do [módulo de serviço](how-to-use-services-module.md). O método [calculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) do RouteUrl é usado para obter uma rota entre os pontos inicial e final. Uma coleção de recursos geojson da resposta é extraída usando o `geojson.getFeatures()` método e é adicionada à fonte de fontes. Em seguida, renderiza a resposta como uma rota no mapa. Para mais informações sobre como adicionar uma linha ao mapa, consulte [adicionar uma linha no mapa](map-add-line-layer.md).

O último bloco de código define os limites do mapa usando a propriedade [setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

A consulta de rota, a fonte de dados, o símbolo, as camadas de linha e os limites de câmera são criados dentro do [ouvinte de eventos](/javascript/api/azure-maps-control/atlas.map#events). Essa estrutura de código garante que os resultados sejam exibidos somente depois que o mapa for totalmente carregado.

## <a name="query-the-route-via-fetch-api"></a>Consultar a rota por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar instruções de A para B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Mostrar direções de A para B em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código cria um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona um objeto [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) ao mapa.

O terceiro bloco de código cria os pontos inicial e de destino para a rota. Em seguida, ele os adiciona à fonte de dados. Você pode consultar [adicionar um pin no mapa](map-add-pin.md) para obter instruções sobre como usar [addPins](/javascript/api/azure-maps-control/atlas.map).

Um [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) renderiza objetos de linha agrupados no [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Uma [camada de símbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa. Veja as propriedades de uma camada de símbolo em [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

O próximo bloco de código cria pontos `SouthWest` e `NorthEast` a partir dos pontos inicial e de destino e define os limites do mapa usando a propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

O último bloco de código usa a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a [API de rota do Azure Maps](/rest/api/maps/route/getroutedirections). Em seguida, a resposta é analisada. Se a resposta foi bem-sucedida, as informações de latitude e longitude são usadas para criar uma matriz uma linha conectando esses pontos. Os dados de linha são então adicionados à fonte de dados para renderizar a rota no mapa. Você pode consultar [adicionar uma linha no mapa](map-add-line-layer.md) para obter instruções.

A consulta de rota, a fonte de dados, o símbolo, as camadas de linha e os limites de câmera são criados dentro do [ouvinte de eventos](/javascript/api/azure-maps-control/atlas.map#events). Novamente, queremos garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Melhores práticas para usar o serviço de roteamento](how-to-use-best-practices-for-search.md)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com os eventos MAP-mouse](./map-events.md)