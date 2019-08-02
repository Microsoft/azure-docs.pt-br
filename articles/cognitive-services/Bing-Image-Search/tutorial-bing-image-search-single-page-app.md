---
title: 'Tutorial: criar um aplicativo Web de página única – API de Pesquisa de Imagem do Bing'
titleSuffix: Azure cognitive services
description: A API de Pesquisa de Imagem do Bing permite que você pesquise na Web imagens de alta qualidade e relevantes. Use este tutorial para criar um aplicativo Web de página única que pode enviar consultas de pesquisa para a API e exibir os resultados na página da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: aahi
ms.openlocfilehash: 7b530b3d415761956cbdb45fdc92bfed55a1bae5
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868253"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Tutorial: criar um aplicativo de página única usando a API de Pesquisa de Imagem do Bing

A API de Pesquisa de Imagem do Bing permite que você pesquise na Web imagens de alta qualidade e relevantes. Use este tutorial para criar um aplicativo Web de página única que pode enviar consultas de pesquisa para a API e exibir os resultados na página da Web. Este tutorial é semelhante ao [tutorial correspondente](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) para Pesquisa na Web do Bing.

O aplicativo de tutorial ilustra como:

> [!div class="checklist"]
> * Executar uma chamada à API de Pesquisa de Imagem do Bing em JavaScript
> * Melhorar os resultados da pesquisa usando as opções de pesquisa
> * Exibir e percorrer a página dos resultados da pesquisa
> * Solicitar e lidar com uma chave de assinatura de API e a ID do cliente do Bing.

O código-fonte completo deste tutorial está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/).
* A estrutura [Express.js](https://expressjs.com/) para Node.js. Instruções de instalação do código-fonte estão disponíveis no arquivo Leiame da amostra do GitHub.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Gerenciar e armazenar chaves de assinatura de usuário

Este aplicativo usa o armazenamento persistente dos navegadores da Web para armazenar as chaves de assinatura de API. Se nenhuma chave for armazenada, a página da Web solicitará a chave do usuário, armazenando-a para uso posterior. Se a chave for rejeitada mais tarde pela API, o aplicativo vai removê-la do armazenamento.


Defina as funções `storeValue` e `retrieveValue` para usar o objeto `localStorage` (se o navegador for compatível) ou um cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

A função `getSubscriptionKey()` tenta recuperar uma chave armazenada anteriormente usando `retrieveValue`. Se uma chave não é encontrada, ela solicita ao usuário sua chave e armazena-a usando `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

A marca `onsubmit` HTML `<form>` chama a função `bingWebSearch` para retornar os resultados da pesquisa. `bingWebSearch` usa `getSubscriptionKey` para autenticar cada consulta. Conforme mostrado na definição anterior, `getSubscriptionKey` solicita ao usuário a chave, caso ela ainda não tenha sido inserida. A chave é então armazenada para continuar o uso pelo aplicativo.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Enviar solicitações de pesquisa

Este aplicativo usa um HTML `<form>` para enviar inicialmente solicitações de pesquisa de usuário, usando o atributo `onsubmit` para chamar `newBingImageSearch()`.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Por padrão, o manipulador `onsubmit` retorna `false`, o que impede o envio do formulário.

## <a name="select-search-options"></a>Selecionar opções de pesquisa

![[Formulário da Pesquisa de Imagem do Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

A API de Pesquisa de Imagem do Bing oferece vários [parâmetros de consulta de filtro](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters) para restringir e filtrar os resultados da pesquisa. O formulário HTML neste aplicativo usa e exibe as seguintes opções de parâmetro:

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Um menu suspenso para selecionar o mercado (local e idioma) usado para a pesquisa.                                                                                             |
| `query`      | O campo de texto no qual inserir os termos de pesquisa.                                                                                                                                 |
| `aspect`     | Botões de opção para escolher as proporções das imagens encontradas: aproximadamente quadrada, larga ou alta.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Menu suspenso para, opcionalmente, limitar a pesquisa para o dia, a semana ou o mês mais recente.                                                                                          |
| `safe`       | Uma caixa de seleção que indica se o recurso Pesquisa Segura do Bing deve ser usado para filtrar resultados "para adultos".                                                                                      |
| `count`      | Campo oculto. O número de resultados da pesquisa a ser retornado em cada solicitação. Altere para exibir menos ou mais resultados por página.                                                            |
| `offset`     | Campo oculto. O deslocamento do primeiro resultado da pesquisa na solicitação; usado para paginação. Ele é redefinido para `0` em uma nova solicitação.                                                           |
| `nextoffset` | Campo oculto. Ao receber um resultado da pesquisa, esse campo é definido com o valor de `nextOffset` na resposta. O uso desse campo previne resultados sobrepostos em páginas sucessivas. |
| `stack`      | Campo oculto. Uma lista codificada em JSON de deslocamentos de páginas anteriores dos resultados da pesquisa, para navegar novamente para páginas anteriores.                                                      |

A função `bingSearchOptions()` formata essas opções em uma cadeia de caracteres de consulta parcial, que pode ser usada nas solicitações de API do aplicativo.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>Executando a solicitação

Usando a consulta de pesquisa, a cadeia de caracteres de opções e a chave de API, a função `BingImageSearch()` usa um objeto XMLHttpRequest para fazer a solicitação ao ponto de extremidade da Pesquisa de Imagem do Bing.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

Após a conclusão bem-sucedida da solicitação HTTP, o JavaScript chama o manipulador de eventos "load" `handleBingResponse()` para manipular uma solicitação HTTP GET bem-sucedida.

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

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
> Solicitações HTTP bem-sucedidas podem conter informações de pesquisa com falha. Se ocorrer um erro durante a operação de pesquisa, a API de Pesquisa de Imagem do Bing retornará um código de status HTTP não 200 e informações de erro na resposta JSON. Além disso, se a solicitação tinha limite de taxa, a API retornará uma resposta vazia.

## <a name="display-the-search-results"></a>Exibir os resultados da pesquisa

Os resultados da pesquisa são exibidos pela função `renderSearchResults()`, que usa o JSON retornado pelo serviço de Pesquisa de Imagem do Bing e chama uma função de renderizador apropriado em imagens e pesquisas relacionadas.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Os resultados da pesquisa de imagem são contidos no objeto `value` de nível superior na resposta JSON. Eles são passados para `renderImageResults()`, que itera por meio dos resultados e converte cada item em HTML.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

A API de Pesquisa de Imagem do Bing pode retornar quatro tipos de sugestões de pesquisa para ajudar a orientar as experiências de pesquisa dos usuários, cada um em seu próprio objeto de nível superior:

| Sugestão         | DESCRIÇÃO                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Consultas que substituem uma palavra original na pesquisa original por outra. Por exemplo, se você pesquisar "flores vermelhas", uma palavra original pode ser "vermelhas" e uma sugestão dinâmica poderá ser "flores amarelas". |
| `queryExpansions`  | Consultas que restringem a pesquisa original com a adição de mais termos. Por exemplo, se você pesquisar "Microsoft Surface", uma expansão da consulta poderá ser "Microsoft Surface Pro".                                   |
| `relatedSearches`  | Consultas que também foram inseridas por outros usuários que inseriram a pesquisa original. Por exemplo, se você pesquisar "Monte Rainier", uma pesquisa relacionada poderá ser "Mt. Santa Helena".                       |
| `similarTerms`     | Consultas que são semelhantes em significado à pesquisa original. Por exemplo, se você pesquisar "gatinhos", um termo semelhante poderá ser "bonito".                                                                   |

Este aplicativo renderiza apenas as sugestões `relatedItems` e coloca os links resultantes na barra lateral da página.

## <a name="rendering-search-results"></a>Renderizando os resultados da pesquisa

Neste aplicativo, o objeto `searchItemRenderers` contém funções de renderizador que geram um HTML para cada tipo de resultado da pesquisa.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Essas funções de renderizador aceitam os seguintes parâmetros:

| Parâmetro         | DESCRIÇÃO                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | O objeto do JavaScript que contém as propriedades do item, como a URL e a descrição. |
| `index` | O índice do item de resultado na coleção.                                          |
| `count` | O número de itens na coleção do item de resultado da pesquisa.                                  |

Os parâmetros `index` e `count` são usados para numerar os resultados, gerar HTML para coleções e organizar o conteúdo. Especificamente, ele:

* Calcula o tamanho da miniatura da imagem (a largura varia, com um mínimo de 120 pixels, enquanto a altura é fixada em 90 pixels).
* Cria a marca HTML `<img>` para exibir a miniatura da imagem.
* Cria as marcas HTML `<a>` que fornecem links para a imagem e a página que a contém.
* Compila a descrição que exibe informações sobre a imagem e o site no qual ela está localizada.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

As imagens em miniatura `height` e `width` são usadas na tag `<img>` e nos campos `h` e `w` na URL da miniatura. Isso permite que o Bing retorne [uma miniatura](../bing-web-search/resize-and-crop-thumbnails.md) exatamente desse tamanho.

## <a name="persisting-client-id"></a>Persistir ID do cliente

As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID` que deve ser enviado novamente para a API com solicitações sucessivas. Se várias APIs de Pesquisa do Bing estiverem sendo usadas, a mesma ID do cliente deverá ser usada com todas elas, se possível.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite que as APIs do Bing associem todas as pesquisas do usuário, o que é útil em

Primeiro, ele permite que o mecanismo de pesquisa do Bing aplique o contexto passado às pesquisas para localizar resultados que melhor atendam às expectativas do usuário. Se um usuário tiver pesquisado termos relacionados à navegação anteriormente, por exemplo, uma pesquisa posterior por "nós" poderá, preferencialmente, retornar informações sobre os nós usados em navegação.

Em segundo lugar, o Bing poderá selecionar usuários aleatoriamente para experimentar novos recursos antes de eles ficarem amplamente disponíveis. O fornecimento da mesma ID do cliente com cada solicitação garante que os usuários que foram escolhidos para ver um recurso sempre o vejam. Sem a ID do cliente, o usuário poderá ver um recurso aparecer e desaparecer, aparentemente de forma aleatória, nos resultados da pesquisa.

As políticas de segurança do navegador (CORS) podem impedir que o cabeçalho `X-MSEdge-ClientID` fique disponível para JavaScript. Essa limitação ocorre quando a resposta da pesquisa tem uma origem diferente da página que a solicitou. Em um ambiente de produção, você deve lidar com essa política hospedando um script do servidor que faz a chamada à API no mesmo domínio da página da Web. Como o script tem a mesma origem da página da Web, o cabeçalho `X-MSEdge-ClientID` estará disponível para JavaScript.

> [!NOTE]
> Em um aplicativo Web de produção, você deve executar a solicitação do lado do servidor de qualquer maneira. Caso contrário, a chave da API de Pesquisa do Bing deverá ser incluída na página da Web, onde estará disponível para qualquer pessoa que exibir a origem. Você é cobrado por todos os usos em sua chave de assinatura de API, até mesmo por solicitações feitas por partes não autorizadas. Portanto, é importante não expor sua chave.

Para fins de desenvolvimento, você pode fazer a solicitação da API de Pesquisa na Web do Bing por meio de um proxy CORS. A resposta desse proxy tem um cabeçalho `Access-Control-Expose-Headers` que permite cabeçalhos de resposta e disponibiliza-os para o JavaScript.

É fácil instalar um proxy CORS para permitir que o aplicativo de tutorial acesse o cabeçalho da ID do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, emita o seguinte comando em uma janela de comando:

    npm install -g cors-proxy-server

Em seguida, altere o ponto de extremidade da Pesquisa na Web do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy CORS com o seguinte comando:

    cors-proxy-server

Deixe a janela Comando aberta enquanto usa o aplicativo de tutorial, porque se você fechá-la, isso interromperá o proxy. Na seção Cabeçalhos HTTP expansíveis abaixo dos resultados da pesquisa, é possível ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Extrair detalhes da imagem usando a API de Pesquisa de Imagem do Bing](tutorial-image-post.md)

## <a name="see-also"></a>Consulte também

* [Referência da API de Pesquisa de Imagem do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
