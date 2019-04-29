---
title: Mostrar resultados de pesquisa com os Mapas do Azure | Microsoft Docs
description: Como realizar uma solicitação de pesquisa com os Mapas do Azure, exiba os resultados em um mapa Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be01c9d96386804b8bc074d81041104cbf592df6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768913"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como fazer uma pesquisa para localização de interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar um local de interesse. Uma maneira é usar um módulo de serviço para fazer uma solicitação de pesquisa. A outra maneira é fazer uma solicitação de pesquisa para [API de pesquisa difusa de mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) por meio de [buscar API](https://fetch.spec.whatwg.org/). As duas formas são discutidas abaixo.

## <a name="make-a-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados de pesquisa em um mapa (módulo de serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar a chave de assinatura. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um `SubscriptionKeyCredentialPolicy` para autenticar solicitações HTTP para mapas do Azure com a chave de assinatura. Em seguida, a `atlas.service.MapsURL.newPipeline()` usa o `SubscriptionKeyCredential` política e cria um [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instância. O `searchURL` representa uma URL para as operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Mapas.

O terceiro bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona resultados de pesquisa a ele. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Uma camada de símbolo é então criada e a fonte de dados é adicionada à camada de símbolo, que é então adicionada ao mapa.

O quarto bloco de código usa o [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) método na [módulo de serviço](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Ele permite que você execute uma pesquisa de texto de forma livre por meio de [obter pesquisa difusa rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar o ponto de interesse. Obter pesquisa difusa API pode lidar com qualquer combinação de entradas difusas. A coleção do recurso GeoJSON da resposta é então extraída usando o método `geojson.getFeatures()` e adicionado à fonte de dados, que automaticamente faz com que os dados sejam renderizados no mapa através da camada do símbolo.

O último bloco de código ajusta os limites da câmera para o mapa usando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Map.

Solicitação de pesquisa, a fonte de dados e camada de símbolo e os limites de câmera são criados e set dentro do mapa do pronto [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os resultados são exibidos depois que o mapa totalmente carregada.


## <a name="make-a-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Mostrar resultados em um mapa</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar a chave de assinatura. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma URL para fazer uma solicitação de pesquisa para. Ele também cria duas matrizes para armazenar limites e pins para resultados da pesquisa.

O terceiro bloco de código usa o [API buscar](https://fetch.spec.whatwg.org/) para fazer uma solicitação para [API de pesquisa difusa de mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar os pontos de interesse. A API de pesquisa difusa pode manipular qualquer combinação de entradas difusas. E, em seguida, ele manipula analisa a resposta de pesquisa e adiciona os pinos de resultado à matriz searchPins.

O quarto bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona resultados de pesquisa a ele. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. Uma camada de símbolo é então criada e a fonte de dados é adicionada à camada de símbolo, que é então adicionada ao mapa.

O último bloco de código cria um objeto [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) usando a matriz de resultados e, em seguida, ajusta os limites da câmera para o mapa usando [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Map. Ela então processa os pinos de resultado.

A solicitação de pesquisa, a fonte de dados e a camada de símbolo e os limites da câmera são definidos no [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir que os resultados sejam exibidos após o carregamento total do mapa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a **Pesquisa Difusa**:

> [!div class="nextstepaction"]
> [API de pesquisa difusa do Mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)
