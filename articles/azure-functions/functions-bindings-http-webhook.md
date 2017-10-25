---
title: "Associações HTTP e de webhook do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações HTTP e de webhook no Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Associações HTTP e de webhook do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com gatilhos e associações HTTP no Azure Functions.
Com isso, você pode usar o Azure Functions para criar APIs sem servidor e responder a webhooks.

O Azure Functions fornece as seguintes associações:
- Um [gatilho HTTP](#httptrigger) permite invocar uma função com uma solicitação HTTP. Ele pode ser personalizado para responder a [webhooks](#hooktrigger).
- Uma [associação de saída HTTP](#output) permite responder à solicitação.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Gatilho HTTP
O gatilho HTTP executará sua função em resposta a uma solicitação HTTP. Você pode personalizá-lo para responder a uma URL específica ou a um conjunto de métodos HTTP. Um gatilho HTTP também pode ser configurado para responder a webhooks. 

Usando o portal de Functions, você também pode começar agora mesmo usando um modelo predefinido. Selecione **Nova função** e escolha "API e Webhooks" na lista suspensa **Cenário**. Selecione um dos modelos e clique em **Criar**.

Por padrão, um gatilho HTTP responderá à solicitação com um código de status HTTP 200 OK e um corpo vazio. Para modificar a resposta, configure uma [associação de saída HTTP](#output)

### <a name="configuring-an-http-trigger"></a>Configuração de um gatilho HTTP
Um gatilho HTTP é definido por um objeto JSON na matriz `bindings` de function.json, como mostrado na imagem a seguir:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
A associação dá suporte às seguintes propriedades:

|Propriedade  |Descrição  |
|---------|---------|
| **name** | Obrigatório – o nome da variável usado no código da função da solicitação ou do corpo da solicitação. Veja [Como trabalhar com um gatilho HTTP no código](#httptriggerusage). |
| **tipo** | Obrigatório – deve ser definido como `httpTrigger`. |
| **direction** | Obrigatório – deve ser definido como `in`. |
| **authLevel** | Determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. O valor pode ser um dos seguintes: <ul><li><code>anonymous</code>&mdash;Nenhuma chave API é obrigatória.</li><li><code>function</code>&mdash;Uma chave de API específica de função é obrigatória. Esse será o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;A chave mestra é obrigatória.</li></ul> Para saber mais, consulte [Trabalhando com chaves](#keys). |
| **methods** | Uma matriz dos métodos HTTP para a qual a função responde. Se não for especificada, a função responderá a todos os métodos HTTP. Veja [Personalização do ponto de extremidade HTTP](#url). |
| **route** | Define o modelo da rota, controlando para quais URLs de solicitação sua função responde. O valor padrão se nenhum for fornecido será `<functionname>`. Para mais informações, consulte [Personalização do ponto de extremidade HTTP](#url). |
| **webHookType** | Configura o gatilho HTTP para atuar como um receptor de webhook para o provedor especificado. Não use a propriedade _methods_ ao usar essa configuração. O valor pode ser um dos seguintes:<ul><li><code>genericJson</code>&mdash;Um ponto de extremidade de webhook de finalidade geral sem lógica para um provedor específico.</li><li><code>github</code>&mdash;A função responde a webhooks do GitHub. Não use a propriedade _authLevel_ ao usar esse valor.</li><li><code>slack</code>&mdash;A função responde a webhooks do Slack. Não use a propriedade _authLevel_ ao usar esse valor.</li></ul> Para obter mais informações, consulte [Respondendo a webhooks](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Como trabalhar com um gatilho HTTP no código
Para funções C# e F#, você pode declarar o tipo de entrada do gatilho como sendo `HttpRequestMessage` ou um tipo .NET personalizado. Ao escolher `HttpRequestMessage`, você obterá acesso completo ao objeto de solicitação. Para um tipo .NET personalizado, o Functions tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto. 

Para as funções do Node.js, o tempo de execução do Functions fornece o corpo da solicitação em vez de o objeto da solicitação. Para obter mais informações, consulte [Exemplos do gatilho HTTP](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>Associação de saída de resposta HTTP
Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Essa associação requer um gatilho HTTP e permite que você personalize a resposta associada à solicitação do gatilho. Se a associação de saída HTTP não for fornecida, um gatilho HTTP retornará HTTP 200 OK com um corpo vazio. 

### <a name="configuring-an-http-output-binding"></a>Configuração de uma associação de saída HTTP
Uma associação de saída HTTP é definida por um objeto JSON na matriz `bindings` de function.json, como mostrado na imagem a seguir:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
A associação dá suporte às seguintes propriedades obrigatórias:

|Propriedade  |Descrição  |
|---------|---------|
|**name** | O nome da variável usada no código de função para a resposta. Veja [Como trabalhar com uma associação de saída no código](#outputusage). |
| **tipo** |Deve ser definido como `http`. |
| **direction** | Deve ser definido como `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Como trabalhar com uma associação de saída no código
Você pode usar o parâmetro de saída para responder ao chamador http ou webhook. Você também pode usar os padrões de resposta de padrão do idioma. Para respostas de exemplo, consulte [Exemplos de gatilho HTTP](#httptriggersample) e [Exemplos de gatilho de webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Resposta a webhooks
Um gatilho HTTP com a propriedade _webHookType_ será configurado para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). A configuração básica usa a configuração "genericJson". Isso restringe as solicitações àquelas que usam HTTP POST e com o tipo de conteúdo `application/json`.

O gatilho também pode ser personalizado para um provedor de webhook específico (por exemplo, [GitHub](https://developer.github.com/webhooks/) ou [Slack](https://api.slack.com/outgoing-webhooks)). Se um provedor for especificado, o tempo de execução de Functions poderá tratar da lógica de validação do provedor para você.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configurando o GitHub como um provedor de webhook
Para responder a webhooks do GitHub, primeiramente crie sua função com um Gatilho HTTP e defina a propriedade **webHookType** como `github`. Em seguida, copie a [URL](#url) e a [chave de API](#keys) na página **Adicionar webhook** do seu repositório GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Para um exemplo, consulte [Criar uma função disparada pelo webhook do GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>Configuração do Slack como um provedor de webhook
O webhook do Slack gera um token em vez de permitir que você o especifique, de modo que é preciso configurar uma chave específica de função com o token do Slack. Veja [Como trabalhar com chaves](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Personalização do ponto de extremidade HTTP
Por padrão, quando você cria uma função para um gatilho HTTP ou WebHook, a função é endereçável com uma rota do formulário:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Você pode personalizar essa rota usando a propriedade `route` opcional na associação de entrada do gatilho HTTP. Por exemplo, o seguinte arquivo *function.json* define uma propriedade `route` para um gatilho HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Usando esta configuração, a função é agora endereçável com a seguinte rota em vez da rota original.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Isso permite que o código de função dê suporte a dois parâmetros no endereço, _category_ e _id_. Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Aqui está o código de função do Node.js para usar os mesmos parâmetros de rota.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Por padrão, todas as rotas de função são prefixadas com *api*. Você também pode personalizar ou remover o prefixo usando a propriedade `http.routePrefix` em seu arquivo *host.json*. O exemplo a seguir remove o prefixo de rota *api* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Para obter informações detalhadas sobre como atualizar o arquivo *host.json* de sua função, consulte [Como atualizar os arquivos de aplicativo de funções](functions-reference.md#fileupdate). 

Para obter informações sobre outras propriedades que você pode configurar no seu arquivo *host.json*, consulte [referência host.json](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Como trabalhar com chaves
Os gatilhos HTTP permitem que você use chaves para aumentar a segurança. Um gatilho HTTP padrão pode usá-las como uma chave de API, exigindo que a chave esteja presente na solicitação. Os webhooks podem usar chaves para autorizar solicitações de várias maneiras, dependendo daquilo a que o provedor oferece suporte.

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e criptografadas em repouso. Para exibir suas chaves, criar novas ou reverter chaves para novos valores, navegue para uma de suas funções dentro do portal e selecione "Gerenciar". 

Há dois tipos de chave:
- **Chaves de host**: essas chaves são compartilhadas por todas as funções do aplicativo de funções. Quando usadas como uma chave de API, elas permitem acesso a qualquer função no aplicativo de funções.
- **Chaves de função**: essas chaves se aplicam apenas às funções específicas sob as quais elas foram definidas. Quando usadas como uma chave de API, elas permitem acesso apenas às funções em questão.

Cada chave é nomeada para referência e há uma chave padrão (chamada "default") no nível de função e de host. A **chave mestra** é uma chave de host padrão chamada "_master", definida para cada aplicativo de funções. Não é possível revogar essa chave. Ela fornece acesso administrativo às APIs de tempo de execução. O uso de `"authLevel": "admin"` no JSON de associação exigirá que essa chave seja apresentada na solicitação. Qualquer outra chave resultará em falha na autorização.

> [!IMPORTANT]  
> Devido às permissões elevadas concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em aplicativos cliente nativos. Tenha cuidado ao escolher o nível de autorização do administrador.

### <a name="api-key-authorization"></a>Autorização da chave de API
Por padrão, um gatilho HTTP requer uma chave de API na solicitação HTTP. Portanto, sua solicitação HTTP geralmente se parecerá como esta:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

A chave pode ser incluída em uma variável de cadeia de caracteres de consulta chamada `code`, como acima, ou pode ser incluída em um cabeçalho HTTP `x-functions-key`. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode permitir solicitações anônimas, o que não exige chaves. Você também pode exigir que a chave mestra seja usada. Altere o nível de autorização padrão usando a propriedade `authLevel` no JSON de associação. Para obter mais informações, consulte [Gatilho HTTP](#httptrigger).

### <a name="keys-and-webhooks"></a>Chaves e webhooks
A autorização de webhook é tratada pelo componente receptor do webhook, parte do gatilho HTTP e o mecanismo varia com base no tipo de webhook. No entanto, cada mecanismo depende de uma chave. Por padrão, a chave de função chamada "default" será usada. Para usar uma chave diferente, configure o provedor do webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

- **Cadeia de caracteres de consulta**: o provedor passa o nome da chave no parâmetro de cadeia de caracteres de consulta `clientid`, como `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Cabeçalho da solicitação**: o provedor passa o nome da chave no cabeçalho `x-functions-clientid`.

> [!NOTE]
> As chaves de função têm precedência sobre as chaves de host. Quando duas chaves forem definidas com o mesmo nome, a chave de função sempre será usada.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>Exemplos de gatilho HTTP
Suponha que você tenha o seguinte gatilho HTTP na matriz `bindings` de function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Veja o exemplo específico de linguagem que procura um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>Exemplo de gatilho HTTP em C# #
```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

Você também pode associar a um objeto .NET em vez de **HttpRequestMessage**. Esse objeto é criado do corpo da solicitação, analisada como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída da resposta HTTP e isso será retornado como o corpo da resposta, com um código de status 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Exemplo de gatilho HTTP em F# #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Você precisa de um arquivo `project.json` que usa o NuGet para fazer referência aos assemblies `FSharp.Interop.Dynamic` e `Dynamitey`, como o seguinte:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Isso usará o NuGet para buscar suas dependências e fará referência a elas em seu script.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Exemplo de gatilho HTTP em Node.JS
```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Exemplos de webhook
Suponha que você tenha o seguinte gatilho de webhook na matriz `bindings` de function.json:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Veja o exemplo específico de linguagem que registra comentários de problemas do GitHub.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Exemplo de webhook em C# #
```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Exemplo de webhook em F# #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Exemplo de webhook em Node.JS
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

