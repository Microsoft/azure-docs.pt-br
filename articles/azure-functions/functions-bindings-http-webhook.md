---
title: Gatilhos e associações HTTP do Azure Functions
description: Entenda como usar gatilhos e associações HTTP no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 53626c1d8fe0b9301883280a9f0925eb38ad1d99
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480445"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Gatilhos e associações HTTP do Azure Functions

Este artigo explica como trabalhar com gatilhos e associações de saída HTTP no Azure Functions.

Um gatilho de HTTP pode ser personalizado para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

O código nesse artigo tem como padrão a sintaxe Funções 2.x que usa o .NET Core. Para obter informações sobre a sintaxe 1.x, consulte os [modelos de funções 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações de HTTP são fornecidas no pacote NuGet, versão 1.x. [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http). O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

As associações de HTTP são fornecidas no pacote NuGet, versão 3.x. [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http). O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/).

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Gatilho

Um gatilho de HTTP permite invocar uma função com uma solicitação HTTP. Você pode usar um gatilho de HTTP para criar APIs sem servidor e responder a webhooks.

Por padrão, um gatilho HTTP retorna HTTP 200 OK com um corpo vazio em funções de 1. x ou HTTP 204 sem conteúdo com um corpo vazio em funções 2. x. Para modificar a resposta, configure uma [associação de saída de HTTP](#output).

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#trigger---c-example)
* [Script do C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que procura por um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP. Observe que o valor de retorno é usado para a associação de saída, mas um atributo de valor de retorno não é necessário.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função de script de C#](functions-reference-csharp.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código de script C# que associa a um `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Você pode vincular a um objeto personalizado em vez de `HttpRequest`. Esse objeto é criado a partir do corpo da solicitação e analisado como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída da resposta HTTP e isso será retornado como o corpo da resposta, com um código de status 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>Gatilho - exemplo F#

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função F#](functions-reference-fsharp.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

O código F# é o seguinte:

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

Você precisa de um arquivo `project.json` que usa o NuGet para fazer referência aos assemblies `FSharp.Interop.Dynamic` e `Dynamitey`, como mostrado no exemplo a seguir:

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

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
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

### <a name="trigger---python-example"></a>Gatilho – Exemplo do Python

O exemplo a seguir mostra uma associação de gatilhos em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui está o arquivo *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>Gatilho - exemplos Java

* [Ler o parâmetro da cadeia de consulta](#read-parameter-from-the-query-string-java)
* [Ler o corpo de uma solicitação POST](#read-body-from-a-post-request-java)
* [Ler o parâmetro de uma rota](#read-parameter-from-a-route-java)
* [Ler o corpo de POJO de uma solicitação POST](#read-pojo-body-from-a-post-request-java)

O exemplo a seguir mostra uma associação de gatilho HTTP em um arquivo *function.json* e as [funções Java](functions-reference-java.md) respectivas que usam a associação. 

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>Ler o parâmetro da cadeia de consulta (Java)  

Este exemplo lê um parâmetro denominado ```id``` da cadeia de caracteres de consulta e o utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo ```application/json```. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Ler o corpo de uma solicitação POST (Java)  

Este exemplo lê o corpo de uma solicitação POST, como uma ```String```, e o utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo ```application/json```.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>Ler o parâmetro de uma rota (Java)  

Este exemplo lê um parâmetro obrigatório, denominado ```id```, e um parâmetro opcional ```name``` do caminho da rota e os utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo ```application/json```. T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>Ler o corpo de POJO de uma solicitação POST (Java)  

Este é o código para a classe ```ToDoItem```, referenciada neste exemplo:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Este exemplo lê o corpo de uma solicitação POST. O corpo da solicitação é desserializado automaticamente em um objeto ```ToDoItem``` e é retornado ao cliente, com o tipo de conteúdo ```application/json```. O parâmetro ```ToDoItem``` é serializado pelo tempo de execução do Functions quando é atribuído para a propriedade ```body``` da classe ```HttpMessageResponse.Builder```.

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classes de C#](functions-dotnet-class-library.md), utilize o atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs).

Você pode definir os métodos HTTP permitidos e o nível de autorização nos parâmetros do construtor do atributo, e existem propriedades para o modelo de rota e o tipo de webhook. Para saber mais informações sobre essas configurações, consulte [Gatilho - configuração](#trigger---configuration). Aqui está um atributo `HttpTrigger` em uma assinatura de método:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para ver um exemplo completo, consulte [Gatilho – exemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `HttpTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
| **type** | n/d| Obrigatório – deve ser definido como `httpTrigger`. |
| **direction** | n/d| Obrigatório – deve ser definido como `in`. |
| **name** | n/d| Obrigatório – o nome da variável usado no código da função da solicitação ou do corpo da solicitação. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Nenhuma chave API é obrigatória.</li><li><code>function</code>&mdash;Uma chave de API específica de função é obrigatória. Esse será o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;A chave mestra é obrigatória.</li></ul> Para saber mais informações, veja a seção sobre [chaves de autorização](#authorization-keys). |
| **methods** |**Métodos** | Uma matriz dos métodos HTTP para a qual a função responde. Se não for especificada, a função responderá a todos os métodos HTTP. Consulte [personalização do ponto de extremidade http](#customize-the-http-endpoint). |
| **route** | **Route** | Define o modelo da rota, controlando para quais URLs de solicitação sua função responde. O valor padrão se nenhum for fornecido será `<functionname>`. Para saber mais informações, consulte [personalização do ponto de extremidade http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Com suporte apenas na versão 1.x do tempo de execução._<br/><br/>Configure o gatilho HTTP para atuar como um receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para o provedor especificado. Não defina a propriedade `methods` se você definir essa propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto de extremidade de webhook de finalidade geral sem lógica para um provedor específico. Essa configuração restringe as solicitações àquelas que usam HTTP POST e com o tipo de conteúdo `application/json`.</li><li><code>github</code>&mdash;A função responde a [webhooks do GitHub](https://developer.github.com/webhooks/). Não use a propriedade _authLevel_ com os webhooks do GitHub. Para saber mais informações, consulte a seção sobre webhooks do GitHub posteriormente neste artigo.</li><li><code>slack</code>&mdash;A função responde a [webhooks do Slack](https://api.slack.com/outgoing-webhooks). Não use a propriedade _authLevel_ com os webhooks do Slack. Para saber mais informações, consulte a seção sobre webhooks do Slack posteriormente neste artigo.</li></ul>|

## <a name="trigger---usage"></a>Gatilho - uso

Para funções C# e F#, você pode declarar o tipo de entrada do gatilho para ser `HttpRequest` ou um tipo personalizado. Ao escolher `HttpRequest`, você obterá acesso completo ao objeto de solicitação. Para um tipo personalizado, o tempo de execução tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto.

Para as funções JavaScript, o tempo de execução do Functions fornece o corpo da solicitação em vez do objeto da solicitação. Para saber mais informações, consulte o [exemplo de gatilho do JavaScript](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Personalização do ponto de extremidade HTTP

Por padrão quando você cria uma função para um gatilho HTTP, a função é endereçável com uma rota do formulário:

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

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Isso permite que o código de função dê suporte a dois parâmetros no endereço, _category_ e _id_. Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

Aqui está o código de função do Node.js que usa os mesmos parâmetros de rota.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

Por padrão, todas as rotas de função são prefixadas com *api*. Você também pode personalizar ou remover o prefixo usando a propriedade `http.routePrefix` em seu arquivo [host.json](functions-host-json.md). O exemplo a seguir remove o prefixo de rota *api* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Trabalhando com identidades de clientes

Se seu aplicativo de função estiver usando [Serviço de Aplicativo de Autenticação / Autorização](../app-service/overview-authentication-authorization.md), você poderá visualizar informações sobre clientes autenticados a partir de seu código. Essas informações estão disponíveis como [headers de solicitação injetados pela plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Você também pode ler essas informações a partir de dados vinculados. Esse recurso está disponível apenas para o tempo de execução do Functions 2.x. Atualmente, também está disponível apenas para idiomas .NET.

Em linguagens .NET, essas informações estão disponíveis como [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Como alternativa, o ClaimsPrincipal pode ser simplesmente incluído como um parâmetro adicional na assinatura da função:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>Chaves de autorização

As funções permitem o uso de chaves para dificultar o acesso aos pontos de extremidade de função HTTP durante o desenvolvimento.  Um gatilho HTTP padrão pode exigir que uma chave de API esteja presente na solicitação. 

> [!IMPORTANT]
> Embora as chaves possam ajudar o ofuscar seus pontos de extremidade HTTP durante o desenvolvimento, elas não foram projetadas como uma maneira de proteger um gatilho HTTP em produção. Para obter mais informações, confira [Proteger um ponto de extremidade HTTP em produção](#secure-an-http-endpoint-in-production).

> [!NOTE]
> No tempo de execução 1.x do Functions, os provedores de webhook podem usar chaves para autorizar solicitações de várias maneiras, dependendo do suporte do provedor. Isso é abordado em [Webhooks e chaves](#webhooks-and-keys). A versão 2.x do tempo de execução não inclui suporte interno para provedores de webhook.

Há dois tipos de chave:

* **Chaves de host**: essas chaves são compartilhadas por todas as funções no aplicativo de funções. Quando usadas como uma chave de API, elas permitem acesso a qualquer função no aplicativo de funções.
* **Chaves de função**: essas chaves se aplicam apenas às funções específicas sob as quais elas foram definidas. Quando usadas como uma chave de API, elas permitem acesso apenas às funções em questão.

Cada chave é nomeada para referência e há uma chave padrão (chamada "default") no nível de função e de host. As chaves de função têm precedência sobre as chaves de host. Quando duas chaves forem definidas com o mesmo nome, a chave de função sempre será usada.

Cada aplicativo de funções também tem uma **chave mestra** especial. Essa chave é uma chave de host chamada `_master`, que fornece acesso administrativo às APIs de tempo de execução. Não é possível revogar essa chave. Quando você define o nível de autorização como `admin`, as solicitações precisam usar a chave mestra. Nesse caso, o uso de outra chave resulta em falha na autorização.

> [!CAUTION]  
> Devido às permissões elevadas no aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em aplicativos cliente nativos. Tenha cuidado ao escolher o nível de autorização do administrador.

### <a name="obtaining-keys"></a>Obtendo chaves

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e criptografadas em repouso. Para exibir suas chaves, criar chaves ou gerar novos valores para as chaves, navegue até uma das funções disparadas por HTTP no [portal do Azure](https://portal.azure.com) e selecione **Gerenciar**.

![Gerencie as chaves de função no portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Não há uma API com suporte para obter teclas de função de forma programática.

### <a name="api-key-authorization"></a>Autorização da chave de API

A maioria dos modelos de gatilho HTTP exigem uma chave de API na solicitação. Portanto, a solicitação HTTP geralmente será como a seguinte URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A chave pode ser incluída em uma variável de cadeia de consulta chamada `code`, como acima. Ela também pode ser incluída em um cabeçalho HTTP `x-functions-key`. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode permitir solicitações anônimas, o que não exige chaves. Você também pode exigir que a chave mestra seja usada. Altere o nível de autorização padrão usando a propriedade `authLevel` no JSON de associação. Para saber mais informações, consulte [Gatilho - configuração](#trigger---configuration).

> [!NOTE]
> Durante a execução local de funções, a autorização fica desabilitada, independentemente da configuração de nível de autenticação especificada. Após a publicação no Azure, a configuração `authLevel` no gatilho é imposta.



### <a name="secure-an-http-endpoint-in-production"></a>Proteger um ponto de extremidade HTTP em produção

Para proteger totalmente os pontos de extremidade de função na produção, considere a implementação de uma das seguintes opções de segurança no nível do aplicativo de funções:

* Ativar a autenticação/autorização do Serviço de Aplicativo para o aplicativo de funções. A plataforma do Serviço de Aplicativo permite usar o AAD (Azure Active Directory) e vários provedores de identidade de terceiros para autenticar clientes. Você pode usar essas opções para implementar regras de autorização personalizadas para suas funções e trabalhar com informações de usuário por meio do código da função. Para saber mais, consulte [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/overview-authentication-authorization.md) e [Trabalhando com identidades do cliente](#working-with-client-identities).

* Use o APIM (Gerenciamento de API do Azure) para autenticar solicitações. O APIM fornece uma variedade de opções de segurança de API para solicitações de entrada. Para saber mais, confira [Políticas de autenticação do Gerenciamento de API](../api-management/api-management-authentication-policies.md). Com o APIM em vigor, você pode configurar o aplicativo de funções para aceitar solicitações somente do endereço IP da sua instância do APIM. Para saber mais, confira [Restrições de endereço IP](ip-addresses.md#ip-address-restrictions).

* Implante seu aplicativo de funções em um ASE (Ambiente do Serviço de Aplicativo) do Azure. O ASE fornece um ambiente de hospedagem dedicado para que você execute suas funções. O ASE permite configurar um único gateway de front-end que você pode usar para autenticar todas as solicitações de entrada. Para obter mais informações, confira [Configurando um WAF (firewall do aplicativo Web) para o Ambiente do Serviço de Aplicativo](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Ao usar um dos seguintes métodos de segurança no nível do aplicativo de funções, você deve definir o nível de autenticação da função disparada por HTTP como `anonymous`.

### <a name="webhooks"></a>webhooks

> [!NOTE]
> O modo de webhook só está disponível para a versão 1.x do tempo de execução do Functions. Essa alteração foi feita para melhorar o desempenho de gatilhos HTTP na versão 2.x.

Na versão 1.x, os modelos de webhook fornecem validação adicional para conteúdo de webhook. Na versão 2.x, o gatilho HTTP base ainda funciona e é a abordagem recomendada para webhooks. 

#### <a name="github-webhooks"></a>Webhooks do GitHub

Para responder a webhooks do GitHub, primeiramente crie sua função com um Gatilho HTTP e defina a propriedade **webHookType** como `github`. Em seguida, copie a URL e a chave de API na página **Adicionar webhook** do seu repositório GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Webhooks do Slack

O webhook do Slack gera um token em vez de permitir que você o especifique, de modo que é preciso configurar uma chave específica de função com o token do Slack. Consulte [Chaves de autorização](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhooks e chaves

A autorização de webhook é tratada pelo componente receptor do webhook, parte do gatilho HTTP e o mecanismo varia com base no tipo de webhook. Cada mecanismo conta com uma chave. Por padrão, a chave de função chamada "default" será usada. Para usar uma chave diferente, configure o provedor do webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

* **Cadeia de caracteres de consulta**: o provedor passa o nome da chave no parâmetro de cadeia de caracteres de consulta `clientid`, como `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Cabeçalho da solicitação**: o provedor passa o nome da chave no cabeçalho `x-functions-clientid`.

## <a name="trigger---limits"></a>Gatilho - limites

O tamanho da solicitação HTTP é limitado a 100 MB (104.857.600 bytes) e o tamanho da URL é limitado a 4 KB (4.096 bytes). Esses limites são especificados pelo `httpRuntime` elemento do [arquivo Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) do tempo de execução.

Se uma função que usa o gatilho HTTP não for concluída em aproximadamente 2,5 minutos, o gateway atingirá o tempo limite e retornará o erro de HTTP 502. A função continuará em execução, mas não poderá retornar uma resposta HTTP. Para funções de longa execução, é recomendável que você siga os padrões async e retorna um local onde você pode executar ping do status da solicitação. Para obter informações sobre o tempo que uma função pode executar, consulte [Dimensionamento e hospedagem - planejar o consumo](functions-scale.md#consumption-plan).

## <a name="trigger---hostjson-properties"></a>Gatilho - propriedades de host.json

O arquivo [host.json](functions-host-json.md) contém configurações que controlam o comportamento de gatilho de HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Essa associação requer um gatilho HTTP e permite que você personalize a resposta associada à solicitação do gatilho. Se uma saída HTTP vinculada não é fornecida, um gatilho HTTP retorna HTTP 200 OK com um corpo vazio em funções de 1. x ou HTTP 204 sem conteúdo com um corpo vazio em funções 2. x.

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*. Para as bibliotecas de classes C#, não há propriedades de atributo que correspondem a essas propriedades do *function.json*.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
| **type** |Deve ser definido como `http`. |
| **direction** | Deve ser definido como `out`. |
|**name** | O nome da variável usada no código de função para a resposta, ou `$return` para usar o valor de retorno. |

## <a name="output---usage"></a>Saída - uso

Para enviar uma resposta HTTP, use os padrões de resposta padrão do idioma. Em c# ou script c#, faça o tipo de retorno da função retorno `IActionResult` ou `Task<IActionResult>`. Em c#, um atributo de valor de retorno não é necessário.

Por ver exemplos de respostas, confira o [exemplo de gatilho](#trigger---example).

## <a name="next-steps"></a>Próximas etapas

[Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
