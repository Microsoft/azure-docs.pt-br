---
title: Mostrar informações sobre uma coordenada em um mapa | Microsoft Azure Mapas
description: Saiba como exibir informações sobre um endereço no mapa quando um usuário seleciona uma coordenada.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 31e4004379340912051204786da592fe33a5bd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890744"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações de uma coordenada

Este artigo mostra como fazer uma pesquisa inversa de endereço que mostra o endereço de um local de pop-up clicado.

Há duas maneiras de fazer uma pesquisa inversa de endereço. Uma delas é a consulta da [API de Pesquisa do Endereço Reverso do Azure Mapas](/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra maneira é usar a [API de Busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação à [API de pesquisa de endereço reverso do Azure Mapas](/rest/api/maps/search/getsearchaddressreverse) a fim de encontrar um endereço. As duas formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa reversa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obter informações de uma coordenada (Módulo de Serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar solicitações HTTP para o Azure Mapas com o token de acesso. Em seguida, ele passa a `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline). O `searchURL` representa uma URL para as operações de [pesquisa](/rest/api/maps/search) do Azure Mapas.

O terceiro bloco de código atualiza o estilo do cursor do mouse para um ponteiro e cria um objeto [popup](/javascript/api/azure-maps-control/atlas.popup#open). Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona um [ouvinte de eventos](/javascript/api/azure-maps-control/atlas.map#events) para cliques do mouse. Quando disparado, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, ele usa o método [getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) para consultar a [API reversa de obtenção de endereço de pesquisa](/rest/api/maps/search/getsearchaddressreverse) a fim de obter o endereço das coordenadas. Uma coleção de recursos GeoJSON é então extraída da resposta usando o método `geojson.getFeatures()`.

O quinto bloco de código configura o conteúdo do pop-up HTML para exibir o endereço de resposta para a posição da coordenada clicada.

A alteração do cursor, o objeto pop-up e o evento de clique são todos criados no [ouvinte de eventos de carregamento](/javascript/api/azure-maps-control/atlas.map#events) do mapa. Essa estrutura de código garante que o mapa seja totalmente carregado antes de recuperar as informações de coordenadas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa inversa via API Fetch

Clique no mapa para fazer uma solicitação inversa de código geográfico para essa localização usando fetch.

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as informações de Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get de uma coordenada </a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código cria um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo do cursor do mouse para um ponteiro. Ele instancia um objeto [pop-up](/javascript/api/azure-maps-control/atlas.popup#open). Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona um ouvinte de evento para cliques do mouse. Com um clique do mouse, ele usa a [API Fetch](https://fetch.spec.whatwg.org/) para consultar a [API de pesquisa de endereço reverso do Azure Mapas](/rest/api/maps/search/getsearchaddressreverse) buscando o endereço das coordenadas clicadas. Para uma resposta bem-sucedida, ele coleta o endereço em busca do local clicado. Ele define a posição e o conteúdo do pop-up usando a função [setOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) da classe popup.

A alteração do cursor, o objeto pop-up e o evento de clique são todos criados no [ouvinte de eventos de carregamento](/javascript/api/azure-maps-control/atlas.map#events) do mapa. Essa estrutura de código garante que o mapa seja totalmente carregado antes das informações de coordenadas serem recuperadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Melhores práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Pop-up](/javascript/api/azure-maps-control/atlas.popup)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego](./map-show-traffic.md)