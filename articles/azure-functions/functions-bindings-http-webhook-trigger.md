---
title: Gatilho de HTTP do Azure Functions
description: Saiba como chamar uma função do Azure via HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4550be7ae8c543eea1bdfa085db6f23fe668a121
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025689"
---
# <a name="azure-functions-http-trigger"></a>Gatilho de HTTP do Azure Functions

Um gatilho de HTTP permite invocar uma função com uma solicitação HTTP. Você pode usar um gatilho de HTTP para criar APIs sem servidor e responder a webhooks.

O valor de retorno padrão para uma função disparada por HTTP é:

- `HTTP 204 No Content` com um corpo vazio no Functions 2.x e superior
- `HTTP 200 OK` com um corpo vazio no Functions 1.x

Para modificar a resposta de HTTP, configure uma [associação de saída](./functions-bindings-http-webhook-output.md).

Para obter mais informações sobre associações HTTP, confira a [visão geral](./functions-bindings-http-webhook.md) e as [referências de associação de saída](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que procura por um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP. Observe que o valor de retorno é usado para a associação de saída, mas um atributo de valor de retorno não é necessário.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

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

A seção [configuração](#configuration) explica essas propriedades.

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
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Você pode vincular a um objeto personalizado em vez de `HttpRequest`. Esse objeto é criado a partir do corpo da solicitação e analisado como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída da resposta HTTP e isso será retornado como o corpo da resposta, com um código de status `200`.

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

# <a name="java"></a>[Java](#tab/java)

* [Ler o parâmetro da cadeia de consulta](#read-parameter-from-the-query-string)
* [Ler o corpo de uma solicitação POST](#read-body-from-a-post-request)
* [Ler o parâmetro de uma rota](#read-parameter-from-a-route)
* [Ler o corpo de POJO de uma solicitação POST](#read-pojo-body-from-a-post-request)

Os exemplos a seguir mostram a associação de gatilho HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Ler o parâmetro da cadeia de consulta

Este exemplo lê um parâmetro denominado `id` da cadeia de caracteres de consulta e o utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo `application/json`.

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

#### <a name="read-body-from-a-post-request"></a>Ler o corpo de uma solicitação POST

Este exemplo lê o corpo de uma solicitação POST, como uma `String`, e o utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo `application/json`.

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

#### <a name="read-parameter-from-a-route"></a>Ler o parâmetro de uma rota

Este exemplo lê um parâmetro obrigatório, denominado `id`, e um parâmetro opcional `name` do caminho da rota e os utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo `application/json`. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Ler o corpo de POJO de uma solicitação POST

Este é o código para a classe `ToDoItem`, referenciada neste exemplo:

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

Este exemplo lê o corpo de uma solicitação POST. O corpo da solicitação é desserializado automaticamente em um objeto `ToDoItem` e é retornado ao cliente, com o tipo de conteúdo `application/json`. O parâmetro `ToDoItem` é serializado pelo runtime do Functions quando é atribuído para a propriedade `body` da classe `HttpMessageResponse.Builder`.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

A seção [configuração](#configuration) explica essas propriedades.

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra uma associação de gatilho em um *function.jsno* arquivo e uma [função do PowerShell](functions-reference-node.md). A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

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
            "name": "$return"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

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

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

Em [Bibliotecas de classe C#](functions-dotnet-class-library.md) e Java, o atributo `HttpTrigger` está disponível para configurar a função.

Você pode definir os métodos HTTP permitidos e o nível de autorização nos parâmetros do construtor do atributo, um modelo de rota e o tipo de webhook. Para obter mais informações sobre esses parâmetros, confira a [configuração](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Este exemplo demonstra como usar o atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs).

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para ver um exemplo completo, confira o [exemplo de gatilho](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Este exemplo demonstra como usar o atributo [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java).

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Para ver um exemplo completo, confira o [exemplo de gatilho](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `HttpTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório – deve ser definido como `httpTrigger`. |
| **direction** | n/d| Obrigatório – deve ser definido como `in`. |
| **name** | n/d| Obrigatório – o nome da variável usado no código da função da solicitação ou do corpo da solicitação. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Nenhuma chave API é obrigatória.</li><li><code>function</code>&mdash;Uma chave de API específica de função é obrigatória. Esse será o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;A chave mestra é obrigatória.</li></ul> Para saber mais informações, veja a seção sobre [chaves de autorização](#authorization-keys). |
| **methods** |**Métodos** | Uma matriz dos métodos HTTP para a qual a função responde. Se não for especificada, a função responderá a todos os métodos HTTP. Confira [personalização do ponto de extremidade HTTP](#customize-the-http-endpoint). |
| **route** | **Route** | Define o modelo da rota, controlando para quais URLs de solicitação sua função responde. O valor padrão se nenhum for fornecido será `<functionname>`. Para saber mais informações, confira [personalização do ponto de extremidade HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Com suporte apenas na versão 1.x do runtime._<br/><br/>Configure o gatilho HTTP para atuar como um receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para o provedor especificado. Não defina a propriedade `methods` se você definir essa propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto de extremidade de webhook de finalidade geral sem lógica para um provedor específico. Essa configuração restringe as solicitações àquelas que usam HTTP POST e com o tipo de conteúdo `application/json`.</li><li><code>github</code>&mdash;A função responde a [webhooks do GitHub](https://developer.github.com/webhooks/). Não use a propriedade _authLevel_ com os webhooks do GitHub. Para saber mais informações, consulte a seção sobre webhooks do GitHub posteriormente neste artigo.</li><li><code>slack</code>&mdash;A função responde a [webhooks do Slack](https://api.slack.com/outgoing-webhooks). Não use a propriedade _authLevel_ com os webhooks do Slack. Para saber mais informações, consulte a seção sobre webhooks do Slack posteriormente neste artigo.</li></ul>|

## <a name="payload"></a>Carga útil

O tipo de entrada do gatilho é declarado como `HttpRequest` ou um tipo personalizado. Ao escolher `HttpRequest`, você obterá acesso completo ao objeto de solicitação. Para um tipo personalizado, o runtime tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto.

## <a name="customize-the-http-endpoint"></a>Personalização do ponto de extremidade HTTP

Por padrão quando você cria uma função para um gatilho HTTP, a função é endereçável com uma rota do formulário:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

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
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Essa configuração permite que o código de função dê suporte a dois parâmetros no endereço, na _categoria_ e na _ID_. Para obter mais informações sobre como os parâmetros de rota são indexados em uma URL, consulte [Routing in ASP.NET Core](/aspnet/core/fundamentals/routing#route-constraint-reference).

# <a name="c"></a>[C#](#tab/csharp)

Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="java"></a>[Java](#tab/java)

O contexto de execução da função é propriedades como declaradas no atributo `HttpTrigger`. O atributo permite que você defina parâmetros de rota, níveis de autorização, verbos HTTP e a instância de solicitação de entrada.

Os parâmetros de rota são definidos por meio do atributo `HttpTrigger`.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

No nó, o runtime do Functions fornece o corpo da solicitação do objeto `context`. Para saber mais informações, consulte o [exemplo de gatilho do JavaScript](#example).

O seguinte exemplo mostra como ler parâmetros de rota de `context.bindingData`.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os parâmetros de rota declarados no *function.jsno* arquivo são acessíveis como uma propriedade do `$Request.Params` objeto.

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

O contexto de execução da função é exposto por meio de um parâmetro declarado como `func.HttpRequest`. Essa instância permite que uma função acesse parâmetros de rota de dados, valores de cadeia de caracteres de consulta e métodos que permitem retornar respostas HTTP.

Uma vez definidas, os parâmetros de rota estão disponíveis para a função chamando o método `route_params`.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

Por padrão, todas as rotas de função são prefixadas com *api*. Você também pode personalizar ou remover o prefixo usando a propriedade `extensions.http.routePrefix` em seu arquivo [host.json](functions-host-json.md). O exemplo a seguir remove o prefixo de rota *api* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host.json*.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Como usar parâmetros de rota

Os parâmetros de rota que definiram o padrão `route` de uma função estão disponíveis para cada associação. Por exemplo, se você tiver uma rota definida como `"route": "products/{id}"`, uma associação de armazenamento de tabela poderá usar o valor do parâmetro `{id}` na configuração de associação.

A configuração a seguir mostra como o parâmetro `{id}` é passado para a `rowKey` da associação.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

Quando você usa parâmetros de rota, um `invoke_URL_template` é criado automaticamente para sua função. Os clientes podem usar o modelo de URL para entender os parâmetros que precisam passar na URL ao chamar sua função usando sua URL. Navegue até uma das funções disparadas por HTTP na [portal do Azure](https://portal.azure.com) e selecione **obter URL da função**.

Você pode acessar programaticamente o usando `invoke_URL_template` as APIs de Azure Resource Manager para [funções de lista](/rest/api/appservice/webapps/listfunctions) ou [obter função](/rest/api/appservice/webapps/getfunction).

## <a name="working-with-client-identities"></a>Trabalhando com identidades de clientes

Se seu aplicativo de função estiver usando [Serviço de Aplicativo de Autenticação / Autorização](../app-service/overview-authentication-authorization.md), você poderá visualizar informações sobre clientes autenticados a partir de seu código. Essas informações estão disponíveis como [headers de solicitação injetados pela plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims).

Você também pode ler essas informações a partir de dados vinculados. Esse recurso está disponível apenas para o runtime do Functions nas versões 2.x e superior. Atualmente, também está disponível apenas para idiomas .NET.

# <a name="c"></a>[C#](#tab/csharp)

As informações sobre clientes autenticados estão disponíveis como um [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

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

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

As informações sobre clientes autenticados estão disponíveis como um [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

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

# <a name="java"></a>[Java](#tab/java)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Chaves de acesso de função

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Obtendo chaves

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e criptografadas em repouso. Para exibir suas chaves, criar novas ou reverter chaves para novos valores, navegue até uma das funções disparadas por HTTP na [portal do Azure](https://portal.azure.com) e selecione **as teclas de função**.

Você também pode gerenciar chaves de host. Navegue até o aplicativo de funções no [portal do Azure](https://portal.azure.com) e selecione **chaves de aplicativo**.

Você pode obter as chaves de função e host programaticamente usando as APIs de Azure Resource Manager. Há APIs para [listar](/rest/api/appservice/webapps/listfunctionkeys) chaves de função [e chaves de host de lista](/rest/api/appservice/webapps/listhostkeys)e, ao usar slots de implantação, as APIs equivalentes são [listar chaves de função slot](/rest/api/appservice/webapps/listfunctionkeysslot) e [listar chaves de host slot](/rest/api/appservice/webapps/listhostkeysslot).

Você também pode criar uma nova função e chaves de host programaticamente usando o [segredo da função criar ou atualizar](/rest/api/appservice/webapps/createorupdatefunctionsecret), [criar ou atualizar o slot secreto da função](/rest/api/appservice/webapps/createorupdatefunctionsecretslot), [criar ou atualizar o segredo do host](/rest/api/appservice/webapps/createorupdatehostsecret) e [criar ou atualizar APIs do slot de segredo do host](/rest/api/appservice/webapps/createorupdatehostsecretslot) .

As chaves de host e de função podem ser excluídas programaticamente usando o [segredo da função Delete](/rest/api/appservice/webapps/deletefunctionsecret), [excluir o slot secreto da função](/rest/api/appservice/webapps/deletefunctionsecretslot), excluir o segredo do [host](/rest/api/appservice/webapps/deletehostsecret)e excluir APIs do slot de [segredo do host](/rest/api/appservice/webapps/deletehostsecretslot) .

Você também pode usar as [APIs de gerenciamento de chaves herdadas para obter as chaves de função](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), mas é recomendável usar as apis de Azure Resource Manager em vez disso.

## <a name="api-key-authorization"></a>Autorização da chave de API

A maioria dos modelos de gatilho HTTP exigem uma chave de API na solicitação. Portanto, a solicitação HTTP geralmente será como a seguinte URL:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

A chave pode ser incluída em uma variável de cadeia de consulta chamada `code`, como acima. Ela também pode ser incluída em um cabeçalho HTTP `x-functions-key`. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode permitir solicitações anônimas, o que não exige chaves. Você também pode exigir que a chave mestra seja usada. Altere o nível de autorização padrão usando a propriedade `authLevel` no JSON de associação. Para saber mais informações, consulte [Gatilho - configuração](#configuration).

> [!NOTE]
> Durante a execução local de funções, a autorização fica desabilitada, independentemente da configuração de nível de autorização especificada. Após a publicação no Azure, a configuração `authLevel` no gatilho é imposta. As chaves ainda são necessárias ao executar [localmente em um contêiner](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Proteger um ponto de extremidade HTTP em produção

Para proteger totalmente os pontos de extremidade de função na produção, considere a implementação de uma das seguintes opções de segurança no nível do aplicativo de funções. Ao usar um dos seguintes métodos de segurança no nível do aplicativo de funções, você deve definir o nível de autorização da função disparada por HTTP como `anonymous`.

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>Implante o aplicativo de funções isoladamente

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> O modo de webhook só está disponível para a versão 1.x do runtime do Functions. Essa alteração foi feita para melhorar o desempenho de gatilhos HTTP na versão 2.x e superior.

Na versão 1.x, os modelos de webhook fornecem validação adicional para conteúdo de webhook. Na versão 2.x e superior, o gatilho HTTP base ainda funciona e é a abordagem recomendada para webhooks. 

### <a name="github-webhooks"></a>Webhooks do GitHub

Para responder a webhooks do GitHub, primeiramente crie sua função com um Gatilho HTTP e defina a propriedade **webHookType** como `github`. Em seguida, copie a URL e a chave de API na página **Adicionar webhook** do seu repositório GitHub. 

![Captura de tela que mostra como adicionar um webhook para sua função.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Webhooks do Slack

O webhook do Slack gera um token em vez de permitir que você o especifique, de modo que é preciso configurar uma chave específica de função com o token do Slack. Consulte [Chaves de autorização](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooks e chaves

A autorização de webhook é tratada pelo componente receptor do webhook, parte do gatilho HTTP e o mecanismo varia com base no tipo de webhook. Cada mecanismo conta com uma chave. Por padrão, a chave de função chamada "default" será usada. Para usar uma chave diferente, configure o provedor do webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

* **Cadeia de caracteres de consulta**: o provedor passa o nome da chave no parâmetro de cadeia de caracteres de consulta `clientid`, como `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Cabeçalho da solicitação**: o provedor passa o nome da chave no cabeçalho `x-functions-clientid`.

## <a name="content-types"></a>Tipos de conteúdo

A passagem de dados binários e de formulário para uma função não C # requer que você use o cabeçalho Content-Type apropriado. Os tipos de conteúdo com suporte incluem `octet-stream` dados binários e [tipos com várias partes](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart).

### <a name="known-issues"></a>Problemas conhecidos

Em funções não C #, as solicitações enviadas com o tipo de conteúdo `image/jpeg` resultam em um `string` valor passado para a função. Em casos como esses, você pode converter manualmente o `string` valor em uma matriz de bytes para acessar os dados binários brutos.

## <a name="limits"></a>limites

O tamanho da solicitação HTTP é limitado a 100 MB (104.857.600 bytes) e o tamanho da URL é limitado a 4 KB (4.096 bytes). Esses limites são especificados pelo `httpRuntime` elemento do [arquivo Web.config](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config) do runtime.

Se uma função que usa o gatilho HTTP não for concluída em aproximadamente 230 segundos, o [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) atingirá o tempo limite e retornará o erro de HTTP 502. A função continuará em execução, mas não poderá retornar uma resposta HTTP. Para funções de longa execução, é recomendável que você siga os padrões async e retorna um local onde você pode executar ping do status da solicitação. Para obter informações sobre o tempo que uma função pode executar, consulte [Dimensionamento e hospedagem - planejar o consumo](functions-scale.md#timeout).


## <a name="next-steps"></a>Próximas etapas

- [Retornar uma resposta HTTP de uma função](./functions-bindings-http-webhook-output.md)