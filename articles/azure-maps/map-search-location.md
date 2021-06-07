---
title: Mostrar resultados da pesquisa em um mapa | Microsoft Azure Mapas
description: Neste artigo, você aprenderá a executar uma solicitação de pesquisa usando o SDK Web do Microsoft Azure Mapas e exibir os resultados no mapa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895266"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como fazer uma pesquisa para localização de interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar um local de interesse. Uma maneira é usar um módulo de serviço para fazer uma solicitação de pesquisa. A outra maneira é fazer uma solicitação de pesquisa à [API de pesquisa difusa do Azure Mapas](/rest/api/maps/search/getsearchfuzzy) por meio da [API Fetch](https://fetch.spec.whatwg.org/). As duas formas são discutidas abaixo.

## <a name="make-a-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados de pesquisa em um mapa (módulo de serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar solicitações HTTP para o Azure Mapas com o token de acesso. Em seguida, ele passa a `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline). O `searchURL` representa uma URL para as operações de [pesquisa](/rest/api/maps/search) do Azure Mapas.

O terceiro bloco de código cria um objeto de fonte de dados usando a classe [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) e adiciona resultados de pesquisa a ele. Uma [camada de símbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos no mapa.  Em seguida, uma camada de símbolo é criada. A fonte de dados é adicionada à camada de símbolo, que é então adicionada ao mapa.

O quarto bloco de código usa o método [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) no [módulo de serviço](how-to-use-services-module.md). Ele permite que você faça uma pesquisa de texto de forma livre por meio da [API REST da Pesquisa Difusa](/rest/api/maps/search/getsearchfuzzy) para pesquisar pelo ponto de interesse. As solicitações Get à API de pesquisa difusa podem manipular qualquer combinação de entradas difusas. A coleção do recurso GeoJSON da resposta é então extraída usando o método `geojson.getFeatures()` e adicionado à fonte de dados, que automaticamente faz com que os dados sejam renderizados no mapa através da camada do símbolo.

O último bloco de código ajusta os limites da câmera para o mapa usando a propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

Os limites de solicitação de pesquisa, fonte de dados, camada de símbolo e câmera estão dentro do [ouvinte de eventos](/javascript/api/azure-maps-control/atlas.map#events) do mapa. Queremos garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.


## <a name="make-a-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa via API Fetch

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Mostrar resultados em um mapa</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Ele define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma URL para a qual fazer uma solicitação de pesquisa. Ele também cria duas matrizes para armazenar limites e marcadores para os resultados da pesquisa.

O terceiro bloco de código usa a [API Fetch](https://fetch.spec.whatwg.org/). A [API Fetch](https://fetch.spec.whatwg.org/) é usada para fazer uma solicitação à [API de pesquisa difusa do Azure Mapas](/rest/api/maps/search/getsearchfuzzy) a fim de pesquisar os pontos de interesse. A API de pesquisa difusa pode manipular qualquer combinação de entradas difusas. Em seguida, ele manipula e analisa a resposta de pesquisa e adiciona os marcadores do resultado à matriz searchPins.

O quarto bloco de código cria um objeto de fonte de dados usando a classe [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource). No código, adicionamos os resultados da pesquisa ao objeto de origem. Uma [camada de símbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos no mapa. Em seguida, uma camada de símbolo é criada. A fonte de dados é adicionada à camada de símbolo, que é então adicionada ao mapa.

O último bloco de código cria um objeto [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox). Ele usa a matriz de resultados e, em seguida, ajusta os limites da câmera para o mapa usando o [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) desse mapa. Em seguida, ele renderiza os marcadores do resultado.

A solicitação de pesquisa, a fonte de dados, a camada de símbolo e os limites da câmera são definidos no [ouvinte de eventos](/javascript/api/azure-maps-control/atlas.map#events) do mapa para garantir que os resultados sejam exibidos após o carregamento total do mapa.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Melhores práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

Saiba mais sobre a **Pesquisa Difusa**:

> [!div class="nextstepaction"]
> [API de pesquisa difusa do Mapas do Azure](/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](map-route.md)