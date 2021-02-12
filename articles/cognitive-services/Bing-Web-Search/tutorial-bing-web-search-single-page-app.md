---
title: 'Tutorial: Criar um aplicativo Web de página única – API de Pesquisa na Web do Bing'
titleSuffix: Azure Cognitive Services
description: Este aplicativo de página única demonstra como a API de Pesquisa na Web do Bing pode ser usada para recuperar, analisar e exibir resultados da pesquisa relevantes em um aplicativo de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ff54d605fd81fa640314d99359f1aabacf7a469e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350374"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Tutorial: Criar um aplicativo de página única usando a API de Pesquisa na Web do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Este aplicativo de página única demonstra como recuperar, analisar e exibir os resultados da pesquisa com base na API de Pesquisa na Web do Bing. O tutorial usa o HTML e CSS clichês e concentra-se no código JavaScript. Arquivos HTML, CSS e JS estão disponíveis no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) com instruções de início rápido.

Este aplicativo de exemplo pode:

> [!div class="checklist"]
> * Chamar a API de Pesquisa na Web do Bing com opções de pesquisa
> * Exibir resultados da Web, da imagem, de notícias e de vídeo
> * Paginar resultados
> * Gerenciar chaves de assinatura
> * Tratar erros

Para usar este aplicativo, é necessária uma [conta dos Serviços Cognitivos do Azure](../cognitive-services-apis-create-account.md) com APIs de Pesquisa do Bing.

## <a name="prerequisites"></a>Pré-requisitos

Veja algumas coisas de que você precisará para executar o aplicativo:

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter a assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Criar um recurso de Pesquisa do Bing"  target="_blank">crie um recurso de Pesquisa do Bing <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.

* Node.js 8 ou posterior

A primeira etapa é clonar o repositório com o código-fonte do aplicativo de exemplo.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Em seguida, execute `npm install`. Para este tutorial, o Express.js é a única dependência.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>Componentes do aplicativo

O aplicativo de exemplo que estamos compilando é composto por quatro partes:

* `bing-web-search.js` – Nosso aplicativo Express.js. Ele lida com roteamento e lógica de resposta/solicitação.
* `public/index.html` – O esqueleto do nosso aplicativo; define como os dados são apresentados ao usuário.
* `public/css/styles.css` – Define estilos da página, como fontes, cores, tamanho do texto.
* `public/js/scripts.js` – Contém a lógica para fazer solicitações à API de Pesquisa na Web do Bing, gerenciar chaves de assinatura, manipular e analisar as respostas e exibir os resultados.

Este tutorial se concentra em `scripts.js` e a lógica necessária para chamar a API de Pesquisa na Web do Bing e lidar com a resposta.

## <a name="html-form"></a>Formulário HTML

O `index.html` inclui um formulário que permite que os usuários pesquisem e selecionem opções de pesquisa. O atributo `onsubmit` é disparado quando o formulário é enviado, chamando o método `bingWebSearch()` definido no `scripts.js`. Ele usa três argumentos:

* Consulta de pesquisa
* Opções selecionadas
* Chave de assinatura

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Opções de consulta

O formulário HTML inclui opções mapeadas para parâmetros de consulta na [API de Pesquisa na Web do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters). Esta tabela fornece um detalhamento de como os usuários podem filtrar os resultados da pesquisa usando o aplicativo de exemplo:

| Parâmetro | Descrição |
|-----------|-------------|
| `query` | Um campo de texto para inserir uma cadeia de consulta. |
| `where` | Um menu suspenso para selecionar o mercado (local e idioma). |
| `what` | Caixas de seleção para promover tipos de resultado específicos. A promoção de imagens, por exemplo, aumenta a classificação de imagens nos resultados da pesquisa. |
| `when` | Um menu suspenso que permite que o usuário limite os resultados da pesquisa para hoje, esta semana ou este mês. |
| `safe` | Uma caixa de seleção para habilitar o Bing SafeSearch, que filtra conteúdo para adulto. |
| `count` | Campo oculto. O número de resultados da pesquisa a ser retornado em cada solicitação. Altere esse valor para exibir menos ou mais resultados por página. |
| `offset` | Campo oculto. O deslocamento do primeiro resultado da pesquisa na solicitação, usado para paginação. Ele é redefinido para `0` com cada nova solicitação. |

> [!NOTE]
> A API de Pesquisa na Web do Bing oferece parâmetros de consulta adicionais para ajudar a refinar os resultados da pesquisa. Este exemplo usa apenas alguns. Para obter uma lista completa de parâmetros disponíveis, consulte [Referência da API de Pesquisa na Web do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters).

A função `bingSearchOptions()` converte essas opções para corresponder ao formato exigido pela API de Pesquisa do Bing.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`SafeSearch` pode ser definido como `strict`, `moderate`, ou `off`, com `moderate` sendo a configuração padrão para Pesquisa na Web do Bing. Este formulário usa uma caixa de seleção, que tem dois estados: `strict` ou `moderate`.

Se qualquer uma das caixas de seleção **Promover** forem selecionadas, o parâmetro `answerCount` será adicionado à consulta. `answerCount` é necessário ao usar o parâmetro `promote`. Neste snippet, o valor é definido como `9` para retornar todos os tipos de resultado disponíveis.
> [!NOTE]
> A promoção de um tipo de resultado não *garante* que ele será incluído nos resultados da pesquisa. Em vez disso, a promoção aumenta a classificação desses tipos de resultados em relação à classificação normal. Para limitar as pesquisas a determinados tipos de resultados, use o parâmetro de consulta `responseFilter` ou chame um ponto de extremidade mais específico, como a Pesquisa de Imagem do Bing ou a Pesquisa de Notícias do Bing.

Os parâmetros de consulta `textDecoration` e `textFormat` são embutidos em código no script e fazem o termo da pesquisa ser colocado em negrito nos resultados da pesquisa. Esses parâmetros não são necessários.

## <a name="manage-subscription-keys"></a>Gerenciar chaves de assinatura

Para evitar embutir a chave de assinatura da API de Pesquisa do Bing em código, esse aplicativo de exemplo usa um armazenamento persistente do navegador para armazenar a chave de assinatura. Se nenhuma chave de assinatura for armazenada, o usuário deverá inserir uma. Se a chave de assinatura for rejeitada pela API, o usuário deverá inserir novamente uma chave de assinatura.

A função `getSubscriptionKey()` usa as funções `storeValue` e `retrieveValue` para armazenar e recuperar uma chave de assinatura do usuário. Essas funções usarão o objeto `localStorage`, se houver suporte, ou cookies.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Como vimos anteriormente, quando o formulário é enviado, `onsubmit` é disparado, chame `bingWebSearch`. Essa função inicializa e envia a solicitação. `getSubscriptionKey` é chamado em cada envio para autenticar a solicitação.

## <a name="call-bing-web-search"></a>Chamar Pesquisa na Web do Bing

Considerando a consulta, a cadeia de caracteres de opções e a chave de assinatura, a função `BingWebSearch` cria um objeto `XMLHttpRequest` para chamar o ponto de extremidade da Pesquisa na Web do Bing.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Após uma solicitação bem-sucedida, o manipulador de eventos `load` é disparado e chama a função `handleBingResponse`. `handleBingResponse` analisa o objeto de resultado, exibe os resultados e contém a lógica de erro para solicitações com falha.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Uma solicitação HTTP com êxito *não* significa que a pesquisa ocorreu com êxito. Se ocorrer um erro na operação de pesquisa, a API de Pesquisa na Web do Bing retornará um código de status HTTP não 200 e incluirá informações de erro na resposta JSON. Se a solicitação tinha limite de taxa, a API retorna uma resposta vazia.

Grande parte do código em ambas as funções anteriores é dedicada ao tratamento de erro. Erros podem ocorrer nos seguintes estágios:

| Estágio | Erro(s) potencial(is) | Manipulado por |
|-------|--------------------|------------|
| Criando o objeto de solicitação | URL inválida | Bloco `try` / `catch` |
| Fazer a solicitação | Erros de rede, conexões anuladas | Manipuladores de eventos `error` e `abort` |
| Executar a pesquisa | Solicitação inválida, JSON inválido, limites de taxa | Testes no manipulador de eventos `load` |

Os erros são tratados por meio da chamada a `renderErrorMessage()`. Se a resposta passar todos os testes de erro, `renderSearchResults()` será chamado para exibir os resultados da pesquisa.

## <a name="display-search-results"></a>Exibir os resultados da pesquisa

Há [requisitos de uso e de exibição](./use-display-requirements.md) para os resultados retornados pela API de Pesquisa na Web do Bing. Como uma resposta pode incluir vários tipos de resultado, não é suficiente iterar por meio da coleção `WebPages` de nível superior. Em vez disso, o aplicativo de exemplo usa `RankingResponse` para ordenar os resultados de acordo com a especificação.

> [!NOTE]
> Se você desejar um tipo de resultado único, use o parâmetro de consulta `responseFilter` ou considere usar um dos outros pontos de extremidade da Pesquisa do Bing, como a Pesquisa de Imagem do Bing.

Cada resposta tem um objeto `RankingResponse` que pode incluir até três coleções: `pole`, `mainline` e `sidebar`. `pole`, se estiver presente, será o resultado de pesquisa mais relevante e deverá ser exibido em destaque. `mainline` contém a maior parte dos resultados da pesquisa e é exibido imediatamente após `pole`. `sidebar` inclui os resultados da pesquisa auxiliares. Se possível, esses resultados deverão ser exibidos em uma barra lateral. Se os limites da tela impossibilitarem a exibição de uma barra lateral, esses resultados deverão ser exibidos após os resultados `mainline`.

Cada `RankingResponse` inclui uma matriz `RankingItem` que especifica como os resultados devem ser ordenados. Nosso aplicativo de exemplo usa os parâmetros `answerType` e `resultIndex` para identificar o resultado.

> [!NOTE]
> Há outras maneiras de identificar e classificar resultados. Para obter mais informações, consulte [Using ranking to display results](rank-results.md) (Usando a classificação para exibir os resultados).

Vamos examinar o código:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

A função `renderResultsItems()` itera pelos itens em cada coleção `RankingResponse`, mapeia cada resultado de classificação para um resultado da pesquisa usando os valores `answerType` e `resultIndex` e chama a função de renderização apropriada para gerar o HTML. Se `resultIndex` não for especificado para um item, `renderResultsItems()` iterará por todos os resultados desse tipo e chamará a função de renderização de cada item. O HTML resultante é inserido no elemento `<div>` apropriado no `index.html`.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>Analisar funções do renderizador

Em nosso aplicativo de exemplo, o objeto `searchItemRenderers` inclui funções que geram HTML para cada tipo de resultado da pesquisa.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> O aplicativo de exemplo tem renderizadores para páginas da Web, notícias, imagens, vídeos e pesquisas relacionadas. Seu aplicativo precisará de renderizadores para qualquer tipo de resultados que você poderá receber, que podem incluir cálculos, sugestões de ortografia, entidades, fusos horários e definições.

Algumas das funções de renderização aceitam apenas o parâmetro `item`. Outras aceitam parâmetros adicionais, que podem ser usados para renderizar itens de maneira diferente com base no contexto. Um renderizador que não usa essas informações não precisa aceitar esses parâmetros.

Os argumentos de contexto são:

| Parâmetro  | Descrição |
|------------|-------------|
| `section` | A seção de resultados (`pole`, `mainline` ou `sidebar`) na qual o item é exibido. |
| `index`<br>`count` | Disponível quando o item `RankingResponse` especifica que todos os resultados de determinada coleção devem ser exibidos; caso contrário, `undefined`. O índice do item na coleção e o número total de itens nessa coleção. É possível usar essas informações para numerar os resultados, gerar um HTML diferente para o primeiro ou o último resultado e assim por diante. |

No aplicativo de exemplo, os renderizadores `images` e `relatedSearches` usam os argumentos do contexto para personalizar o HTML gerado. Vamos examinar o renderizador `images` mais detalhadamente:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

O renderizador da imagem:

* Calcula o tamanho da miniatura da imagem (a largura varia, enquanto a altura é fixada em 60 pixels).
* Insere o HTML que precede o resultado de imagem com base no contexto.
* Compila a marca HTML `<a>` vinculada à página que contém a imagem.
* Cria a marca HTML `<img>` para exibir a miniatura da imagem.

O renderizador de imagem usa as variáveis `section` e `index` para exibir os resultados de maneira diferente, dependendo do local em que são exibidos. Uma quebra de linha (marcação `<br>`) é inserida entre os resultados de imagem na barra lateral, para que a barra lateral exiba uma coluna de imagens. Em outras seções, o primeiro resultado de imagem `(index === 0)` é precedido por uma marcação `<p>`.

O tamanho da miniatura é usado na marcação `<img>` e nos campos `h` e `w` na URL da miniatura. Os atributos `title` e `alt` (uma descrição textual da imagem) são construídos com base no nome da imagem e no nome do host na URL.

Veja um exemplo de como as imagens são exibidas no aplicativo de exemplo:

![[Resultados de imagem do Bing]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Persistir a ID do cliente

As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID` que deve ser enviado novamente para a API com cada solicitação sucessiva. Se mais de uma das APIs de Pesquisa do Bing for usada pelo seu aplicativo, certifique-se de que a mesma ID do cliente seja enviada com cada solicitação entre serviços.

Fornecer o cabeçalho `X-MSEdge-ClientID` permite que as APIs do Bing associem as pesquisas de um usuário. Primeiro, ele permite que o mecanismo de pesquisa do Bing aplique o contexto passado às pesquisas para localizar resultados que melhor atendam às expectativas da solicitação. Se um usuário tiver pesquisado termos relacionados à navegação anteriormente, por exemplo, uma pesquisa posterior por "nós" poderá, preferencialmente, retornar informações sobre os nós usados em navegação. Em segundo lugar, o Bing pode selecionar aleatoriamente usuários para experimentar novos recursos antes de serem amplamente disponibilizados. O fornecimento da mesma ID do cliente com cada solicitação garante que os usuários que foram escolhidos para ver um recurso sempre o vejam. Sem a ID do cliente, o usuário poderá ver um recurso aparecer e desaparecer, aparentemente de forma aleatória, nos resultados da pesquisa.

As políticas de segurança do navegador, como CORS (Compartilhamento de Recursos entre Origens), pode impedir que o aplicativo exemplo acesse o cabeçalho `X-MSEdge-ClientID`. Essa limitação ocorre quando a resposta da pesquisa tem uma origem diferente da página que a solicitou. Em um ambiente de produção, você deve lidar com essa política hospedando um script do servidor que faz a chamada à API no mesmo domínio da página da Web. Como o script tem a mesma origem da página da Web, o cabeçalho `X-MSEdge-ClientID` estará disponível para JavaScript.

> [!NOTE]
> Em um aplicativo Web de produção, você deve executar a solicitação do lado do servidor de qualquer maneira. Caso contrário, a chave de assinatura da API de Pesquisa do Bing deverá ser incluída na página da Web, em que estará disponível para qualquer pessoa que exibir a origem. Você é cobrado por todos os usos em sua chave de assinatura de API, até mesmo por solicitações feitas por partes não autorizadas. Portanto, é importante não expor sua chave.

Para fins de desenvolvimento, é possível fazer uma solicitação por meio de um proxy CORS. A resposta desse tipo de proxy tem um cabeçalho `Access-Control-Expose-Headers` que filtra os cabeçalhos de resposta e disponibiliza-os para o JavaScript.

É fácil instalar um proxy CORS para permitir que o aplicativo de exemplo acesse o cabeçalho da ID do cliente. Execute este comando:

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto de extremidade da Pesquisa na Web do Bing no `script.js` para:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Iniciar o proxy CORS com este comando:

```console
cors-proxy-server
```

Deixe a janela Comando aberta enquanto você usa o aplicativo de exemplo; o fechamento da janela interromperá o proxy. Na seção Cabeçalhos HTTP expansível embaixo dos resultados da pesquisa, o cabeçalho `X-MSEdge-ClientID` deve estar visível. Verifique se ele é o mesmo para cada solicitação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API de Pesquisa na Web do Bing v7](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)