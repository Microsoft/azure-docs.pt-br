---
title: 'Tutorial: Aplicativo Web de página única da Pesquisa de Entidade do Bing'
titleSuffix: Azure Cognitive Services
description: Mostra como usar a API de Pesquisa de Entidade do Bing em um aplicativo Web de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: 5a8276f06207eb69ffec0e21c6d92794973f3b83
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423979"
---
# <a name="tutorial-single-page-web-app"></a>Tutorial: Aplicativo Web de página única

A API de Pesquisa de Entidade do Bing permite pesquisar na Web informações sobre *entidades* e *locais.* É possível solicitar qualquer tipo de resultado, ou ambos, em uma determinada consulta. As definições de entidades e locais são fornecidas abaixo.

|||
|-|-|
|Entidades|Pessoas conhecidas, locais e coisas que você localiza pelo nome|
|Locais|Restaurantes, hotéis e outras empresas locais que você encontra por nome *ou* por tipo (restaurantes italianos)|

Neste tutorial, compilamos um aplicativo Web de página única que usa a API de Pesquisa de Entidade do Bing para exibir os resultados da pesquisa diretamente na página. O aplicativo inclui componentes HTML, CSS e JavaScript.

A API permite que você priorize os resultados por local. Em um aplicativo móvel, você pode perguntar ao dispositivo sua própria localização. Em um aplicativo Web, é possível usar a função `getPosition()`. Mas essa chamada funciona apenas em contextos seguros, e pode não fornecer uma localização precisa. Além disso, o usuário pode querer procurar entidades próximas a um local diferente do seu.

Nosso aplicativo, portanto, chama o serviço Bing Mapas para obter latitude e longitude de uma localização inserida pelo usuário. O usuário pode inserir o nome de um ponto de referência ("Space Needle") ou um endereço completo ou parcial ("Cidade de Nova Iorque"), e a API do Bing Mapas fornece as coordenadas.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Os cabeçalhos JSON e HTTP na parte inferior da página revelam as informações da resposta JSON e da solicitação HTTP quando recebem um clique. Esses detalhes são úteis ao explorar o serviço.

O aplicativo de tutorial ilustra como:

> [!div class="checklist"]
> * Executar uma chamada à API de Pesquisa de Entidade do Bing em JavaScript
> * Executar uma chamada à API do `locationQuery` Bing Mapas em JavaScript
> * Passar opções de pesquisa para as chamadas à API
> * Exibir os resultados da pesquisa
> * Manipular a ID do cliente do Bing e a chave de assinatura da API
> * Lidar com quaisquer erros que possam ocorrer

A página do tutorial é completamente autossuficiente; ela não usa estruturas externas, folhas de estilo, nem mesmo arquivos de imagem. Ela usa apenas recursos de linguagem JavaScript amplamente compatíveis e funciona com as versões atuais de todos os principais navegadores da Web.

Neste tutorial, abordaremos somente as partes selecionadas do código-fonte. O código-fonte completo está disponível [em uma página separada](tutorial-bing-entities-search-single-page-app-source.md). Copie e cole esse código em um editor de texto e salve-o como `bing.html`.

> [!NOTE]
> Este tutorial é muito semelhante ao [tutorial de aplicativo da Pesquisa na Web do Bing de página única](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), mas lida apenas com os resultados da pesquisa de entidade.

## <a name="app-components"></a>Componentes do aplicativo

Como qualquer aplicativo Web de página única, o aplicativo de tutorial inclui três partes:

> [!div class="checklist"]
> * HTML – define a estrutura e o conteúdo da página
> * CSS – define a aparência da página
> * JavaScript – define o comportamento da página

Este tutorial não aborda a maior parte do CSS ou do HTML em detalhes, pois eles são simples.

O HTML contém o formulário de pesquisa no qual o usuário insere uma consulta e escolhe as opções de pesquisa. O formulário está conectado ao JavaScript que, de fato, executa a pesquisa pelo atributo `onsubmit` da marca `<form>`:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

O manipulador `onsubmit` retorna `false`, o que impede o envio do formulário para um servidor. Na verdade, o código JavaScript, faz o trabalho de coletar as informações necessárias do formulário e realizar a pesquisa.

A pesquisa é feita em duas fases. Primeiro, se o usuário tiver inserido uma restrição de local, uma consulta do Bing Mapas será feita para converter em coordenadas. O retorno de chamada para essa consulta, em seguida, inicia a consulta de Pesquisa de Entidade do Bing.

O HTML também contém as divisões (marcas HTML `<div>`) nas quais os resultados da pesquisa são exibidos.

## <a name="managing-subscription-keys"></a>Gerenciar a chave de assinatura

> [!NOTE]
> Este aplicativo exige chaves de assinatura para ambas as API de Pesquisa do Bing e API do Bing Mapas. É possível usar uma [chave de Pesquisa do Bing de avaliação](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) e uma [chave do Bing Mapas básica](https://www.microsoft.com/maps/create-a-bing-maps-key).

Para evitar a necessidade de incluir as chaves de assinatura da Pesquisa do Bing e da API do Bing Mapas no código, usamos o armazenamento persistente do navegador para armazená-las. Se uma das chaves não foi armazenada, solicitamos e armazenamos para uso posterior. Se a chave for posteriormente rejeitada pela API, invalidamos a chave armazenada para que o usuário seja solicitado na próxima pesquisa.

Definimos as funções `storeValue` e `retrieveValue` que usam o objeto `localStorage` (se o navegador der suporte) ou um cookie. A função `getSubscriptionKey()` usa essas funções para armazenar e recuperar a chave do usuário.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

A marca HTML `<body>` inclui um atributo `onload` que chama `getSearchSubscriptionKey()` e `getMapsSubscriptionKey()` quando a página termina de ser carregada. Essas chamadas servem para solicitar imediatamente ao usuário as chaves, caso ainda não tenham entrado nelas.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Selecionar as opções de pesquisa

![[Formulário de Pesquisa de Entidade do Bing]](media/entity-search-spa-form.png)

O formulário HTML inclui os seguintes controles:

| | |
|-|-|
|`where`|Um menu suspenso para selecionar o mercado (local e idioma) usado para a pesquisa.|
|`query`|O campo de texto no qual inserir os termos de pesquisa.|
|`safe`|Uma caixa de seleção que indica se o SafeSearch está ativado (restringe os resultados "adulto")|
|`what`|Um menu para escolher pesquisar entidades, locais ou ambos.|
|`mapquery`|O campo de texto no qual o usuário pode inserir um endereço completo ou parcial, um ponto de referência, etc. para ajudar a Pesquisa de Entidade do Bing retornar resultados mais relevantes.|

> [!NOTE]
> Atualmente, os resultados estão disponíveis apenas nos Estados Unidos. Os menus `where` e `what` têm código para impor essa restrição. Se você escolher um mercado não EUA enquanto Locais estiver selecionado no menu `what`, `what` altera para Tudo. Se você escolher Locais enquanto um mercado não EUA estiver selecionado no menu `where`, `where` altera para os EUA.

A função JavaScript `bingSearchOptions()` converte esses campos em uma cadeia de caracteres de consulta parcial para a API de Pesquisa do Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Por exemplo, o recurso SafeSearch pode ser `strict`, `moderate` ou `off`, com `moderate` sendo o padrão. Porém, nosso formulário usa uma caixa de seleção, que tem apenas dois estados. O código JavaScript converte essa configuração para `strict` ou `off` (não usamos `moderate`).

O campo `mapquery` não é tratado em `bingSearchOptions()` porque é usado para a consulta de localização do Bing Mapas, não para a Pesquisa de Entidade do Bing.

## <a name="obtaining-a-location"></a>Obter um local

A API do Bing Mapas oferece um [`locationQuery` método](//msdn.microsoft.com/library/ff701711.aspx), que usamos para localizar a latitude e longitude do local inserido pelo usuário. Essas coordenadas são então passadas para a API de Pesquisa de Entidade do Bing com a solicitação do usuário. Os resultados da pesquisa priorizam entidades e lugares próximos ao local especificado.

Não podemos acessar a API do Bing Mapas usando uma consulta `XMLHttpRequest` comum em um aplicativo Web, pois o serviço não dá suporte a consultas entre origens. Felizmente, há suporte para JSONP (o "P" é para "preenchido"). Uma resposta JSONP é uma resposta JSON comum envolvida em uma chamada de função. A solicitação é feita, inserindo uma marca `<script>` no documento. (Carregar scripts não está sujeito a políticas de segurança do navegador.)

A função `bingMapsLocate()` cria e insere a marca `<script>` para a consulta. O segmento `jsonp=bingMapsCallback` da cadeia de caracteres de consulta especifica o nome da função a ser chamada com a resposta.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Se a API do Bing Mapas não responder, a função `bingMapsCallBack()` nunca será chamada. Normalmente, isso significa que `bingEntitySearch()` não é chamado e os resultados da pesquisa de entidade não aparecem. Para evitar esse cenário, `bingMapsLocate()` também define um temporizador para chamar `bingEntitySearch()` após cinco segundos. Há lógica na função de retorno de chamada para evitar a realização da pesquisa de entidade duas vezes.

Quando a consulta é concluída, a função `bingMapsCallback()` é chamada, conforme solicitado.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Juntamente com latitude e longitude, a consulta da Pesquisa de Entidade do Bing requer um *raio* que indique a precisão das informações do local. Calculamos o raio usando a *caixa delimitadora*  fornecida na resposta do Bing Mapas. A caixa delimitadora é um retângulo que envolve todo o local. Por exemplo, se o usuário inserir `NYC`, o resultado conterá aproximadamente coordenadas centrais de Nova Iorque e uma caixa delimitadora que engloba a cidade. 

Primeiro, calculamos as distâncias das coordenadas primárias para cada um dos quatro cantos da caixa delimitadora usando a função `haversineDistance()` (não mostrado). Nós usamos a maior dessas quatro distâncias como o raio. O raio mínimo é um quilômetro. Esse valor também será usado como padrão, se nenhuma caixa delimitadora for fornecida na resposta.

Tendo obtido as coordenadas e o raio, nós chamamos `bingEntitySearch()` para realizar a pesquisa atual.

## <a name="performing-the-search"></a>Executar a pesquisa

Fornecida a consulta, um local, uma cadeia de caracteres de opções e a chave da API, a função `BingEntitySearch()` fará a solicitação da Pesquisa de Entidade do Bing.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");

    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Após a conclusão com êxito da solicitação HTTP, o JavaScript chamará o manipulador de eventos `load`, a função `handleBingResponse()`, para manipular uma solicitação HTTP GET com êxito para a API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Uma solicitação HTTP com êxito *não* significa necessariamente que a pesquisa ocorreu com êxito. Se ocorrer um erro na operação de pesquisa, a API de Pesquisa de Entidade do Bing retornará um código de status HTTP diferente de 200 e incluirá informações de erro na resposta JSON. Além disso, se a solicitação tinha limite de taxa, a API retorna uma resposta vazia.

Grande parte do código em ambas as funções anteriores é dedicada ao tratamento de erro. Erros podem ocorrer nos seguintes estágios:

|Estágio|Erro(s) potencial(is)|Manipulado por|
|-|-|-|
|Compilar objeto de solicitação JavaScript|URL inválida|Bloco `try`/`catch`|
|Fazer a solicitação|Erros de rede, conexões anuladas|Manipuladores de eventos `error` e `abort`|
|Executar a pesquisa|Solicitação inválida, JSON inválido, limites de taxa|testes no manipulador de eventos `load`|

Os erros são tratados com uma chamada a `renderErrorMessage()` com os detalhes conhecidos sobre o erro. Se a resposta passar no desafio completo de testes de erro, chamamos `renderSearchResults()` para exibir os resultados da pesquisa na página.

## <a name="displaying-search-results"></a>Exibir os resultados da pesquisa

A API de Pesquisa de Entidade do Bing [exige a exibição dos resultados em uma ordem especificada](use-display-requirements.md). Como a API pode retornar dois tipos diferentes de respostas, não é suficiente fazer uma iteração na coleção `Entities` ou `Places` de nível superior na resposta JSON e exibir esses resultados. (Se você quiser apenas um tipo de resultado, use o parâmetro de consulta `responseFilter`.)

Em vez disso, usamos a coleção `rankingResponse` nos resultados da pesquisa para ordenar os resultados para exibição. Esse objeto refere-se a itens nas coleções `Entitiess` e/ou `Places`.

`rankingResponse` pode conter até três coleções de resultados da pesquisa, designadas `pole`, `mainline` e `sidebar`. 

`pole`, se estiver presente, será o resultado de pesquisa mais relevante e deverá ser exibido em destaque. `mainline` refere-se à maior parte dos resultados da pesquisa. Os resultados da linha principal devem ser exibidos imediatamente após `pole` (ou primeiro, caso `pole` não esteja presente). 

Por fim. `sidebar` refere-se aos resultados da pesquisa auxiliares. Eles podem ser exibidos em uma barra lateral real ou simplesmente após os resultados da linha principal. Nós escolhemos o último para o nosso aplicativo de tutorial.

Cada item em uma coleção `rankingResponse` refere-se aos itens do resultado da pesquisa real de duas formas diferentes, mas equivalentes.

| | |
|-|-|
|`id`|O tipo `id` é semelhante a uma URL, mas não deve ser usado para links. O tipo `id` de um resultado de classificação corresponde à `id` de um item de resultado da pesquisa em uma coleção de respostas *ou* a uma coleção inteira de respostas (como `Entities`).
|`answerType`<br>`resultIndex`|O `answerType` refere-se à coleção de respostas de nível superior que contém o resultado (por exemplo, `Entities`). O `resultIndex` refere-se ao índice do resultado nessa coleção. Se `resultIndex` estiver omitido, o resultado da classificação refere-se à coleção inteira.

> [!NOTE]
> Para obter mais informações sobre essa parte da resposta da pesquisa, confira [Resultados de Classificação](rank-results.md).

Use qualquer método de localização do item de resultado da pesquisa referenciado que seja mais conveniente para o aplicativo. Em nosso código do tutorial, usamos `answerType` e `resultIndex` para localizar cada resultado da pesquisa.

Por fim, é hora de examinar nossa função `renderSearchResults()`. Essa função itera pelas três coleções `rankingResponse` que representam as três seções dos resultados da pesquisa. Para cada seção, chamamos `renderResultsItems()` para renderizar os resultados para essa seção.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Renderizar os itens de resultado

Em nosso código JavaScript há um objeto, `searchItemRenderers`, que contém *renderizadores:* funções que geram um HTML para cada tipo de resultado da pesquisa.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Uma função de renderizador pode aceitar os seguintes parâmetros:

| | |
|-|-|
|`item`|O objeto do JavaScript que contém as propriedades do item, como a URL e a descrição.|
|`index`|O índice do item de resultado na coleção.|
|`count`|O número de itens na coleção do item de resultado da pesquisa.|

Os parâmetros `index` e `count` podem ser usados para numerar os resultados, para gerar um HTML especial no início ou no fim de uma coleção, para inserir quebras de linha após determinado número de itens e assim por diante. Se um renderizador não precisar dessa funcionalidade, não precisará aceitar esses dois parâmetros. Na verdade, não os utilizamos nos renderizadores do nosso aplicativo de tutorial.

Vamos examinar o renderizador `entities` mais detalhadamente:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Nossa função de renderizador de entidade:

> [!div class="checklist"]
> * Compila a marca HTML `<img>` para exibir a miniatura da imagem, se houver. 
> * Compila a marca HTML `<a>` vinculada à página que contém a imagem.
> * Compila a descrição que exibe informações sobre a imagem e o site no qual ela está localizada.
> * Incorpora a classificação da entidade usando as dicas de exibição, se houver.
> * Inclui um link para uma pesquisa do Bing para obter mais informações sobre a entidade.
> * Exibe qualquer informação de licenciamento ou atribuição exigida por fontes de dados.

## <a name="persisting-client-id"></a>Persistir ID do cliente

As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID` que deve ser enviado novamente para a API com solicitações sucessivas. Se várias APIs de Pesquisa do Bing estiverem sendo usadas, a mesma ID do cliente deverá ser usada com todas elas, se possível.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite que as APIs do Bing associem todas as pesquisas do usuário, o que oferece dois benefícios importantes.

Primeiro, ele permite que o mecanismo de pesquisa do Bing aplique o contexto passado às pesquisas para localizar resultados que melhor atendam às expectativas do usuário. Se um usuário tiver pesquisado termos relacionados à navegação anteriormente, por exemplo, uma pesquisa posterior por "nós" poderá, preferencialmente, retornar informações sobre os nós usados em navegação.

Em segundo lugar, o Bing pode selecionar aleatoriamente usuários para experimentar novos recursos antes de serem amplamente disponibilizados. O fornecimento da mesma ID do cliente com cada solicitação garante que os usuários que foram escolhidos para ver um recurso sempre o vejam. Sem a ID do cliente, o usuário poderá ver um recurso aparecer e desaparecer, aparentemente de forma aleatória, nos resultados da pesquisa.

As políticas de segurança do navegador (CORS) podem impedir que o cabeçalho `X-MSEdge-ClientID` fique disponível para JavaScript. Essa limitação ocorre quando a resposta da pesquisa tem uma origem diferente da página que a solicitou. Em um ambiente de produção, você deve lidar com essa política hospedando um script do servidor que faz a chamada à API no mesmo domínio da página da Web. Como o script tem a mesma origem da página da Web, o cabeçalho `X-MSEdge-ClientID` estará disponível para JavaScript.

> [!NOTE]
> Em um aplicativo Web de produção, você deve executar a solicitação do lado do servidor de qualquer maneira. Caso contrário, a chave da API de Pesquisa do Bing deverá ser incluída na página da Web, onde estará disponível para qualquer pessoa que exibir a origem. Você é cobrado por todos os usos em sua chave de assinatura de API, até mesmo por solicitações feitas por partes não autorizadas. Portanto, é importante não expor sua chave.

Para fins de desenvolvimento, você pode fazer a solicitação da API de Pesquisa na Web do Bing por meio de um proxy CORS. A resposta desse proxy tem um cabeçalho `Access-Control-Expose-Headers` que inclui os cabeçalhos de resposta na lista de permissões e disponibiliza-os para o JavaScript.

É fácil instalar um proxy CORS para permitir que o aplicativo de tutorial acesse o cabeçalho da ID do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, emita o seguinte comando em uma janela de comando:

    npm install -g cors-proxy-server

Em seguida, altere o ponto de extremidade da Pesquisa na Web do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy CORS com o seguinte comando:

    cors-proxy-server

Deixe a janela Comando aberta enquanto usa o aplicativo de tutorial, porque se você fechá-la, isso interromperá o proxy. Na seção Cabeçalhos HTTP expansíveis abaixo dos resultados da pesquisa, é possível ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de API de Pesquisa de Entidade do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Documentação de API do Bing Mapas](//msdn.microsoft.com/library/dd877180.aspx)
