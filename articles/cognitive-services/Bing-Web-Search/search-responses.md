---
title: Estrutura de resposta da API do Bing Web Search e tipos de resposta
titleSuffix: Azure Cognitive Services
description: Quando você envia uma solicitação de pesquisa para a Pesquisa na Web do Bing, ela retorna um objeto `SearchResponse` no corpo da resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 3dda95312a0b9191ddc11de62959f308ee19fff4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94380973"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Estrutura de resposta da API do Bing Web Search e tipos de resposta  

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Quando você envia Pesquisa na Web do Bing uma solicitação de pesquisa, ela retorna um [`SearchResponse`](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objeto no corpo da resposta. O objeto inclui um campo para cada resposta que Bing considerou relevante para a consulta. Este exemplo ilustra um objeto de resposta caso o Bing retorne todas as respostas:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Normalmente, a Pesquisa na Web do Bing retorna um subconjunto das respostas. Por exemplo, se o termo de consulta era *velejando Dinghies*, a resposta pode incluir `webPages` , `images` e `rankingResponse` . A menos que você tenha usado [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) para filtrar as páginas da Web, a resposta sempre incluirá as respostas `webpages` e `rankingResponse`.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Resposta de páginas da Web

A resposta [webPages](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) contém uma lista de links para páginas da Web que a Pesquisa na Web do Bing considerou relevantes para a consulta. Cada [página da Web](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) na lista incluirá: o nome, a URL e a URL de exibição da página, uma breve descrição do conteúdo e a data em que o Bing encontrou o conteúdo.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Use `name` e `url` para criar um hiperlink que leva o usuário para a página da Web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Resposta de imagens

A resposta [images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contém uma lista de imagens que o Bing considerou relevante para a consulta. Cada [imagem](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na lista inclui a URL da imagem, o tamanho, as dimensões e o formato de codificação dela. O objeto de imagem também inclui a URL de uma miniatura da imagem e as dimensões da miniatura.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Dependendo do dispositivo do usuário, normalmente seria exibido um subconjunto das miniaturas com uma opção para o usuário [percorrer as páginas](./paging-search-results.md) das imagens restantes.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Também é possível expandir a miniatura quando o usuário passar o cursor sobre ela. Se expandir a imagem, verifique se ela foi atribuída. Por exemplo, extraia o host de `hostPageDisplayUrl` e exiba-o abaixo da imagem. Para obter informações sobre o redimensionamento da miniatura, consulte [Redimensionando e cortando miniaturas](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se o usuário clicar na miniatura, use `webSearchUrl` para levá-lo para a página resultados da pesquisa do Bing para imagens, que contém uma colagem das imagens.

Para ver detalhes sobre a resposta de imagem e as imagens, consulte [API de Pesquisa de Imagem do Bing](../bing-image-search/overview.md).

## <a name="related-searches-answer"></a>Resposta de pesquisas relacionadas

A resposta [relatedSearches](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) contém uma lista das consultas relacionadas mais populares feitas por outros usuários. Cada [consulta](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) na lista inclui uma cadeia de caracteres de consulta (`text`), uma cadeia de caracteres de consulta com caracteres da ocorrência realçados (`displayText`) e uma URL (`webSearchUrl`) para a página de resultados da pesquisa do Bing para a consulta.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Use a cadeia de caracteres de consulta `displayText` e a URL `webSearchUrl` para criar um hiperlink que leva o usuário para a página de resultados da pesquisa do Bing para a consulta relacionada. Também é possível usar a cadeia de caracteres de consulta `text` em sua própria consulta da API de Pesquisa na Web do Bing exibir os resultados por conta própria.

Para obter informações sobre como lidar com o realce de marcadores em `displayText`, consulte [Realce de ocorrências](../bing-web-search/hit-highlighting.md).

Veja a seguir um exemplo do uso de consultas relacionadas no Bing.com.

![Exemplo de pesquisas relacionadas no Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Resposta de vídeos

A resposta [videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contém uma lista de vídeos que o Bing considerou relevante para a consulta. Cada [vídeo](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na lista inclui a URL do vídeo, sua duração, suas dimensões e seu formato de codificação. O objeto de vídeo também inclui a URL de uma miniatura do vídeo e as dimensões da miniatura.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Dependendo do dispositivo do usuário, normalmente seria exibido um subconjunto dos vídeos com uma opção para o usuário exibir os vídeos restantes. Seria possível exibir uma miniatura do vídeo com o tamanho do vídeo, a descrição (nome) e a atribuição (publicador) dele.

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

À medida que o usuário passa o mouse sobre a miniatura, é possível usar `motionThumbnailUrl` para reproduzir uma versão em miniatura do vídeo. Atribua a miniatura de movimento ao exibi-la.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Se o usuário clicar na miniatura, estas serão as opções de exibição de vídeo:

- Use `hostPageUrl` para exibir o vídeo no site do host (por exemplo, no YouTube)
- Use `webSearchUrl` para exibir o vídeo no navegador de vídeo do Bing
- Use `embedHtml` para inserir o vídeo em sua própria experiência

Para ver detalhes sobre a resposta de vídeo e os vídeos, consulte [API de Pesquisa de Vídeo do Bing](../bing-video-search/overview.md).

## <a name="news-answer"></a>Resposta de notícias

A resposta [news](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) contém uma lista de artigos de notícias que o Bing considerou relevante para a consulta. Cada [artigo de notícias](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) na lista inclui o nome, descrição e URL do artigo para o artigo no site do host. Se o artigo contiver uma imagem, o objeto incluirá uma miniatura da imagem.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Dependendo do dispositivo do usuário, normalmente seria exibido um subconjunto dos artigos de notícias com uma opção para o usuário exibir os artigos restantes. Use `name` e `url` para criar um hiperlink que leva o usuário para o artigo de notícias no site do host. Se o artigo incluir uma imagem, verifique se é possível clicar nela usando `url`. Lembre-se de usar `provider` para atribuir o artigo.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Para saber mais detalhes sobre a resposta de notícias e os artigos de notícias, consulte [API de Pesquisa de Notícias do Bing](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Resposta de computação

Se o usuário inserir uma expressão matemática ou uma consulta de conversão de unidade, a resposta poderá conter uma resposta [Computation](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation). A resposta `computation` contém a expressão normalizada e o respectivo resultado.

Uma consulta de conversão de unidade é uma consulta que converte uma unidade em outra. Por exemplo, *Quantos pés há em 10 metros?* ou *Quantas colheres de sopa há em uma 1/4 de xícara?*

Veja a seguir a resposta `computation` para *Quantos pés há em 10 metros?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Veja a seguir exemplos de consultas matemáticas e as respectivas respostas `computation`.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Uma expressão matemática pode conter os seguintes símbolos:

|Símbolo|Descrição|
|------------|-----------------|
|+|Adição|
|-|Subtração|
|/|Divisão|
|*|Multiplicação|
|^|Energia|
|!|Fatorial|
|.|Decimal|
|()|Agrupamento de precedência|
|[]|Função|

Uma expressão matemática pode conter as seguintes constantes:

|Símbolo|Descrição|
|------------|-----------------|
|Pi|3,14159...|
|Grau|Grau|
|i|Número imaginário|
|e|e, 2,71828...|
|GoldenRatio|Proporção áurea, 1,61803...|

Uma expressão matemática pode conter as seguintes funções:

|Símbolo|Descrição|
|------------|-----------------|
|Sort|Raiz quadrada|
|Sin[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|Funções trigonométricas (com argumentos em radianos)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Funções trigonométricas inversas (fornecendo os resultados em radianos)|
|Exp[x], E^x|Função exponencial|
|Log[x]|Logaritmo natural|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Funções hiperbólicas|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Funções hiperbólicas inversas|

Expressões matemáticas que contenham variáveis (por exemplo, 4x+6=18, em que x é a variável) não têm suporte.

## <a name="timezone-answer"></a>Resposta de TimeZone

Se o usuário inserir uma consulta de data ou hora, a resposta poderá conter uma resposta [TimeZone](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone). Essa resposta é compatível com consultas implícitas ou explícitas. Uma consulta implícita, como *Que horas são?*, retorna a hora local com base na localização do usuário. Uma consulta explícita, como *Que horas são em Seattle?*, retorna a hora local para Seattle, WA.

A resposta `timeZone` fornece o nome do local, a data e a hora UTC atual no local especificado, e a diferença UTC. Se o limite do local estiver dentro de vários fusos horários, a resposta conterá a data e a hora UTC atual de todos os fusos horários dentro dos limites. Por exemplo, como o estado da Flórida está dentro de dois fusos horários, a resposta conteria a data e hora local em ambos os fusos horários.  

Se a consulta solicitar a hora de um Estado ou país/região, o Bing determinará a cidade principal dentro do limite geográfico do local e a retornará no `primaryCityTime` campo. Se o limite contiver vários fusos horários, os fusos horários restantes serão retornados no campo `otherCityTimes`.

O exemplo a seguir mostra consultas de exemplo que retornam a resposta `timeZone`.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Resposta de SpellSuggestion

Se o Bing determinar que o usuário pode ter pretendido pesquisar algo diferente, a resposta incluirá um objeto [SpellSuggestions](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions). Por exemplo, se o usuário pesquisar *carlos pensa*, o Bing pode determinar que o usuário provavelmente pretendia pesquisar Carlos Pena em vez disso (com base em pesquisas anteriores de *carlos pensa* feitas por outras pessoas). Veja a seguir um exemplo de resposta de ortografia.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Cabeçalhos de resposta

As respostas da API de Pesquisa na Web do Bing podem conter os seguintes cabeçalhos:

| parâmetro | Descrição |
|-|-|
|`X-MSEdge-ClientID`|A ID exclusiva que o Bing atribuiu ao usuário|
|`BingAPIs-Market`|O mercado que foi usado para atender à solicitação|
|`BingAPIs-TraceId`|A entrada de log no servidor da API do Bing para esta solicitação (para suporte)|

É particularmente importante persistir a ID do cliente e retorná-la com solicitações subsequentes. Quando você fizer isso, a pesquisa usará o contexto passado na classificação dos resultados da pesquisa e também fornecerá uma experiência de usuário consistente.

No entanto, quando você chama a API de Pesquisa na Web do Bing a partir do JavaScript, os recursos de segurança incorporados do navegador (CORS) podem impedi-lo de acessar os valores desses cabeçalhos.

Para obter acesso aos cabeçalhos, é possível fazer a solicitação da API de Pesquisa na Web do Bing por meio de um proxy CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra os cabeçalhos de resposta e os torna disponíveis para o JavaScript.

É fácil instalar um proxy CORS para permitir que nosso [aplicativo de tutorial](tutorial-bing-web-search-single-page-app.md) acesse os cabeçalhos opcionais do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, insira o comando a seguir em um prompt de comando.

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto de extremidade API de Pesquisa na Web do Bing no arquivo HTML para: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Por fim, inicie o proxy CORS com o seguinte comando:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto você usa o aplicativo de tutorial, já que se fechar a janela irá parar o proxy. Na seção Cabeçalhos HTTP expansíveis abaixo dos resultados da pesquisa, é possível ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta na produção

A abordagem do proxy CORS descrita na resposta anterior é apropriada para desenvolvimento, teste e aprendizado.

Em um ambiente de produção, você deve hospedar um script do lado do servidor no mesmo domínio da página da Web que usa a API de Pesquisa na Web do Bing. O script deve fazer as chamadas à API, mediante solicitação, do JavaScript da página da Web e transmitir todos os resultados, incluindo cabeçalhos, de volta para o cliente. Como os dois recursos (página e script) compartilham uma origem, o CORS não é usado e os cabeçalhos especiais podem ser acessados pelo JavaScript na página da Web.

Essa abordagem também protege a chave de API da exposição ao público, já que apenas o script do lado do servidor precisa dela. O script pode usar outro método para garantir que a solicitação seja autorizada.

O exemplo a seguir mostra como o Bing usa a sugestão de ortografia.

![Exemplo de sugestão de ortografia do Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Próximas etapas  

* Leia a documentação de [Limitação da solicitação](throttling-requests.md).  

## <a name="see-also"></a>Veja também  

* [Referência da API de Pesquisa na Web do Bing](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)