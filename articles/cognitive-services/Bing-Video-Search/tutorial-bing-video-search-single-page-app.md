---
title: 'Tutorial: Criar um aplicativo de página única da Pesquisa de Vídeo do Bing'
titleSuffix: Azure Cognitive Services
description: Explica como usar a API de Pesquisa de Vídeo do Bing em um aplicativo Web de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: aahi
ms.openlocfilehash: d2cd3d37801fc1a42a9bcbd5f70a6a55e78aaf08
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500070"
---
# <a name="tutorial-single-page-video-search-app"></a>Tutorial: Aplicativo de Pesquisa de Vídeo de página única
A API de Pesquisa de Vídeo do Bing permite que você faça pesquisas na Web e obtenha resultados de vídeo relevantes para uma consulta de pesquisa. Neste tutorial, criaremos um aplicativo Web de página única que usa a API de Pesquisa do Bing para exibir os resultados da pesquisa na página. O aplicativo inclui componentes HTML, CSS e JavaScript.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> Os cabeçalhos JSON e HTTP na parte inferior da página mostram as informações da resposta JSON e da solicitação HTTP quando recebem um clique. Esses detalhes podem ser úteis ao explorar o serviço.

![JSON, resultados brutos de HTTP](./media/json-http-raw-results.png)

Esse aplicativo de tutorial ilustra como:
> [!div class="checklist"]
> * Executar uma chamada à API de Pesquisa de Vídeo do Bing em JavaScript
> * Passar opções de pesquisa para a API de Pesquisa do Bing
> * Exibir os resultados da pesquisa de vídeo ou, opcionalmente, incluir páginas da Web, notícias ou imagens
> * Pesquisar períodos de 24 horas, a última semana, o mês ou todo o tempo disponível
> * Percorrer a página dos resultados da pesquisa
> * Manipular a ID do cliente do Bing e a chave de assinatura da API
> * Tratar erros que podem ocorrer

A página do tutorial é completamente autossuficiente; ela não usa estruturas externas, folhas de estilo nem arquivos de imagem. Ela usa apenas recursos de linguagem JavaScript amplamente compatíveis e funciona com as versões atuais de todos os principais navegadores da Web.

Neste tutorial, abordaremos as partes selecionadas do código-fonte. O [código-fonte](tutorial-bing-video-search-single-page-app-source.md) completo está disponível. Para executar o exemplo, copie e cole o código-fonte em um editor de texto e salve-o como `bing.html`.

## <a name="app-components"></a>Componentes do aplicativo
Como qualquer aplicativo Web de página única, esse aplicativo de tutorial inclui três partes:

> [!div class="checklist"]
> * HTML – define a estrutura e o conteúdo da página
> * CSS – define a aparência da página
> * JavaScript – define o comportamento da página

A maior parte do HTML e do CSS é convencional e, portanto, o tutorial não o aborda. O HTML contém o formulário de pesquisa no qual o usuário insere uma consulta e escolhe as opções de pesquisa. O formulário é conectado ao JavaScript que faz a pesquisa usando a marcação `<form>` do atributo `onsubmit`:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
O manipulador `onsubmit` retorna `false`, o que impede o envio do formulário para um servidor. O código JavaScript faz o trabalho de coletar as informações necessárias do formulário e realizar a pesquisa.

O HTML também contém as divisões (marcas HTML `<div>`) nas quais os resultados da pesquisa são exibidos.

## <a name="managing-subscription-key"></a>Gerenciando a chave de assinatura

Para evitar a necessidade de incluir a chave de assinatura da API de Pesquisa do Bing no código, usamos o armazenamento persistente do navegador para armazenar a chave. Antes de a chave ser armazenada, solicitamos a chave do usuário. Se a chave for rejeitada pela API mais tarde, invalidaremos a chave armazenada para que o usuário receba a solicitação novamente.

Definimos as funções `storeValue` e `retrieveValue` que usam o objeto `localStorage` (nem todos os navegadores dão suporte e ele) ou um cookie. A função `getSubscriptionKey()` usa essas funções para armazenar e recuperar a chave do usuário.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
A marca `onsubmit` HTML `<form>` chama a função `bingWebSearch` para retornar os resultados da pesquisa. `bingWebSearch` usa `getSubscriptionKey()` para autenticar cada consulta. Conforme mostrado na definição anterior, `getSubscriptionKey` solicita ao usuário a chave, caso ela ainda não tenha sido inserida. A chave é então armazenada para continuar o uso pelo aplicativo.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Selecionando as opções de pesquisa
A figura a seguir mostra a caixa de texto de consulta e as opções que definem uma pesquisa.

![Opções da Pesquisa de Notícias do Bing](media/video-search-options.png)

O formulário HTML inclui elementos com os seguintes nomes:

|Elemento|DESCRIÇÃO|
|-|-|
| `where` | Um menu suspenso para selecionar o mercado (local e idioma) usado para a pesquisa. |
| `query` | O campo de texto no qual inserir os termos de pesquisa. |
| `modules` | Caixas de seleção para promover módulos específicos de resultados, todos os resultados ou vídeos relacionados. |
| `when` | Menu suspenso para, opcionalmente, limitar a pesquisa para o dia, a semana ou o mês mais recente. |
| `safe` | Uma caixa de seleção que indica se o recurso Pesquisa Segura do Bing deve ser usado para filtrar resultados "para adultos". |
| `count` | Campo oculto. O número de resultados da pesquisa a ser retornado em cada solicitação. Altere para exibir menos ou mais resultados por página. |
| `offset`|  Campo oculto. O deslocamento do primeiro resultado da pesquisa na solicitação; usado para paginação. Ele é redefinido para `0` em uma nova solicitação. |

> [!NOTE]
> A Pesquisa na Web do Bing oferece outros parâmetros de consulta. Estamos usando apenas alguns deles.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Por exemplo, o parâmetro `SafeSearch` em uma chamada à API real pode ser `strict`, `moderate` ou `off`, com `moderate` sendo o padrão. No entanto, nosso formulário usa uma caixa de seleção, que tem apenas dois estados. O código JavaScript converte essa configuração para `strict` ou `off` (`moderate` não é usado).

## <a name="performing-the-request"></a>Executando a solicitação
Considerando a consulta, a cadeia de caracteres de opções e a chave de API, a função `BingWebSearch` usa um objeto `XMLHttpRequest` para fazer a solicitação ao ponto de extremidade da Pesquisa do Bing.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
Após a conclusão bem-sucedida da solicitação HTTP, o JavaScript chama o manipulador de eventos `load`, `handleOnLoad()`, para manipular uma solicitação HTTP GET bem-sucedida para a API. 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> Se ocorrer um erro na operação de pesquisa, a API de Pesquisa de Notícias do Bing retornará um código de status HTTP não 200 e incluirá informações de erro na resposta JSON. Além disso, se a solicitação tinha limite de taxa, a API retorna uma resposta vazia.
Uma solicitação HTTP bem-sucedida *não* necessariamente significa que a pesquisa em si foi bem-sucedida. 

Grande parte do código nas duas funções anteriores é dedicada ao tratamento de erro. Erros podem ocorrer nos seguintes estágios:

|Estágio|Erro(s) potencial(is)|Manipulado por|
|-|-|-|
|Criando o objeto de solicitação do JavaScript|URL inválida|Bloco `try`/`catch`|
|Fazer a solicitação|Erros de rede, conexões anuladas|Manipuladores de eventos `error` e `abort`|
|Executar a pesquisa|Solicitação inválida, JSON inválido, limites de taxa|testes no manipulador de eventos `load`|

Os erros são tratados com uma chamada a `renderErrorMessage()` com os detalhes conhecidos sobre o erro. Se a resposta passar no desafio completo de testes de erro, chamamos `renderSearchResults()` para exibir os resultados da pesquisa na página.

## <a name="displaying-search-results"></a>Exibindo os resultados da pesquisa
A função principal para exibir os resultados da pesquisa é `renderSearchResults()`. Essa função usa o JSON retornado pelo serviço de Pesquisa de Notícias do Bing e renderiza os resultados das notícias e as pesquisas relacionadas se houver.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
Os resultados da pesquisa são retornados como o objeto `value` de nível superior na resposta JSON. Passamos esses resultados para a função `renderResultsItems()`, que itera por eles e chama uma função para renderizar cada item em HTML. O HTML resultante é retornado para `renderSearchResults()`, no qual ele é inserido na divisão `results` da página.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

A API de Pesquisa de Notícias do Bing retorna até quatro tipos diferentes de resultados relacionados, cada um em seu próprio objeto de nível superior. Eles são:

|Relação|DESCRIÇÃO|
|-|-|
|`pivotSuggestions`|Consultas que substituem uma palavra original na pesquisa original por outra. Por exemplo, se você pesquisar "flores vermelhas", uma palavra original pode ser "vermelhas" e uma sugestão dinâmica poderá ser "flores amarelas".|
|`queryExpansions`|Consultas que restringem a pesquisa original com a adição de mais termos. Por exemplo, se você pesquisar "Microsoft Surface", uma expansão da consulta poderá ser "Microsoft Surface Pro".|
|`relatedSearches`|Consultas que também foram inseridas por outros usuários que inseriram a pesquisa original. Por exemplo, se você pesquisar "Monte Rainier", uma pesquisa relacionada poderá ser "Mt. Santa Helena".|
|`similarTerms`|Consultas que são semelhantes em significado à pesquisa original. Por exemplo, se você pesquisar "escolas", um termo semelhante poderá ser "educação".|

Conforme visto anteriormente em `renderSearchResults()`, renderizamos apenas as sugestões de `relatedItems` e colocamos os links resultantes na barra lateral da página.

## <a name="rendering-result-items"></a>Renderizando os itens de resultado

No código JavaScript, o objeto, `searchItemRenderers`, pode conter funções *renderers:* que geram um HTML para cada tipo de resultado da pesquisa. A página de pesquisa de vídeo usa apenas `videos`. Confira outros tutoriais para vários tipos de renderizadores.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Uma função de renderizador pode aceitar os seguintes parâmetros:

|Parâmetro|DESCRIÇÃO|
|-|-|
|`item`| O objeto do JavaScript que contém as propriedades do item, como a URL e a descrição.|
|`index`| O índice do item de resultado na coleção.|
|`count`| O número de itens na coleção do item de resultado da pesquisa.|

Os parâmetros `index` e `count` podem ser usados para numerar os resultados, para gerar um HTML especial no início ou no fim de uma coleção, para inserir quebras de linha após determinado número de itens e assim por diante. Se um renderizador não precisa dessa funcionalidade, ele não precisa aceitar esses dois parâmetros.

O renderizador `video` é mostrado no trecho de JavaScript a seguir. Usando o ponto de extremidade Vídeos, todos os resultados são do tipo `Videos`. Os `searchItemRenderers` são mostrados no segmento de código a seguir.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

A função do renderizador:
> [!div class="checklist"]
> * Cria uma marcação de parágrafo, atribui-a à classe `images` e a envia por push para a matriz de HTML.
> * Calcula o tamanho da miniatura da imagem (a largura é fixada em 60 pixels e a altura é calculada proporcionalmente).
> * Cria a marca HTML `<img>` para exibir a miniatura da imagem. 
> * Cria as marcas HTML `<a>` que fornecem links para a imagem e a página que a contém.
> * Cria a descrição que exibe informações sobre a imagem e o site no qual ela está localizada.

O tamanho da miniatura é usado na marcação `<img>` e nos campos `h` e `w` na URL da miniatura. O Bing retornará uma [miniatura](../bing-web-search/resize-and-crop-thumbnails.md) exatamente desse tamanho.

## <a name="persisting-client-id"></a>Persistir ID do cliente
As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID` que deve ser enviado novamente para a API com solicitações sucessivas. Se várias APIs de Pesquisa do Bing estiverem sendo usadas, a mesma ID do cliente deverá ser usada com todas elas, se possível.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite que as APIs do Bing associem todas as pesquisas do usuário, o que oferece dois benefícios importantes.

Primeiro, ele permite que o mecanismo de pesquisa do Bing aplique o contexto passado às pesquisas para encontrar resultados que melhor atendam às expectativas do usuário. Se um usuário tiver pesquisado termos relacionados à navegação anteriormente, por exemplo, uma pesquisa posterior por "nós" poderá, preferencialmente, retornar informações sobre os nós usados em navegação.

Em segundo lugar, o Bing poderá selecionar usuários aleatoriamente para experimentar novos recursos antes de eles ficarem amplamente disponíveis. O fornecimento da mesma ID do cliente com cada solicitação garante que os usuários que veem o recurso sempre o vejam. Sem a ID do cliente, o usuário poderá ver um recurso aparecer e desaparecer, aparentemente de forma aleatória, nos resultados da pesquisa.

As políticas de segurança do navegador (CORS) podem impedir que o cabeçalho `X-MSEdge-ClientID` fique disponível para JavaScript. Essa limitação ocorre quando a resposta da pesquisa tem uma origem diferente da página que a solicitou. Em um ambiente de produção, você deve lidar com essa política hospedando um script do servidor que faz a chamada à API no mesmo domínio da página da Web. Como o script tem a mesma origem da página da Web, o cabeçalho `X-MSEdge-ClientID` estará disponível para o JavaScript.

> [!NOTE]
> Em um aplicativo Web de produção, você deve executar a solicitação do servidor. Caso contrário, a chave de API de Pesquisa do Bing precisará ser incluída na página da Web, na qual ela estará disponível para qualquer pessoa que exiba a origem. Você é cobrado por todos os usos em sua chave de assinatura de API, até mesmo por solicitações feitas por partes não autorizadas. Portanto, é importante não expor sua chave.

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
> [Referência da API de Pesquisa de Vídeo do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)
