---
title: Mostrar informações sobre uma coordenada com mapas do Azure | Microsoft Docs
description: Como exibir informações sobre um endereço no mapa quando um usuário seleciona uma coordenada
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769117"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações de uma coordenada

Este artigo mostra como fazer uma pesquisa inversa de endereço que mostra o endereço de um local de pop-up clicado.

Há duas maneiras de fazer uma pesquisa inversa de endereço. Uma delas é a consulta da [API de Pesquisa do Endereço Reverso do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra maneira é utilizar a [API buscar](https://fetch.spec.whatwg.org/) para fazer uma solicitação para o [API de pesquisa do Azure mapas Inverter endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para localizar um endereço. As duas formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa reversa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obter informações de uma coordenada (Módulo de Serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar a chave de assinatura. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um `SubscriptionKeyCredentialPolicy` para autenticar solicitações HTTP para mapas do Azure com a chave de assinatura. Em seguida, a `atlas.service.MapsURL.newPipeline()` usa o `SubscriptionKeyCredential` política e cria um [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instância. O `searchURL` representa uma URL para as operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Mapas.

O terceiro bloco de código atualiza o estilo do cursor do mouse em um ponteiro e cria um [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objeto. Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona um clique do mouse [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Quando disparado, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Ele usa o módulo de serviço [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) método para consultar o [pesquisa endereço Inverter API obter](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço das coordenadas. Uma coleção de recursos do GeoJSON da resposta é, em seguida, extraída usando o `geojson.getFeatures()` método.

O quinto bloco de código define o conteúdo do popup HTML para exibir o endereço de resposta para a posição da coordenada clicado.

A mudança de cursor, um objeto pop-up e o evento click são todos criados no [listener de eventos de carregamento do mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o mapa carregue totalmente antes que as informações de coordenadas possam ser recuperadas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa inversa por meio da API de busca

Clique no mapa para fazer uma solicitação de geocode reverso para esse local usando fetch.

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as informações de Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get de uma coordenada </a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar a chave de assinatura. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo do cursor do mouse para um ponteiro e o objeto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona um ouvinte de evento para cliques do mouse. Após um clique do mouse, ele utiliza o [API buscar](https://fetch.spec.whatwg.org/) para consultar o [API de pesquisa do Azure mapas Inverter endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço de coordenadas clicado. Para uma resposta bem-sucedida, ele coleta o endereço do local clicado e define o conteúdo e a posição do popup por meio da função [ setOptions ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) da classe pop-up.

A mudança de cursor, um objeto pop-up e o evento click são todos criados no [listener de eventos de carregamento do mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o mapa carregue totalmente antes que as informações de coordenadas possam ser recuperadas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego](./map-show-traffic.md)
