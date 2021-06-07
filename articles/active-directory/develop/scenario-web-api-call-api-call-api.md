---
title: API Web que chama APIs Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs da Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4dfd2c7f9dbdd111f70a5dd5a648c11eacbf7b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038536"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Uma API Web que chama APIs da Web: chamar uma API

Depois de ter um token, você pode chamar uma API Web protegida. Normalmente, você chama as APIs downstream do controlador ou das páginas da sua API Web.

## <a name="controller-code"></a>Código do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ao usar *o Microsoft. Identity. Web*, você tem três cenários de uso:

- [Opção 1: chamar Microsoft Graph com o SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Opção 2: chamar uma API da Web downstream com a classe auxiliar](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opção 3: chamar uma API da Web downstream sem a classe auxiliar](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opção 1: chamar Microsoft Graph com o SDK

Nesse cenário, você adicionou `.AddMicrosoftGraph()` em *Startup. cs* conforme especificado na configuração de [código](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)e pode injetar diretamente o `GraphServiceClient` no seu controlador ou construtor de página para uso nas ações. A página Razor de exemplo a seguir exibe a foto do usuário conectado.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Opção 2: chamar uma API da Web downstream com a classe auxiliar

Nesse cenário, você adicionou `.AddDownstreamWebApi()` em *Startup. cs* conforme especificado na configuração de [código](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), e você pode injetar um `IDownstreamWebApi` serviço diretamente no seu controlador ou construtor de página e usá-lo nas ações:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

O `CallWebApiForUserAsync` método também tem substituições genéricas fortemente tipadas que permitem que você receba diretamente um objeto. Por exemplo, o método a seguir recebeu uma `Todo` instância, que é uma representação fortemente tipada do JSON retornado pela API da Web.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Opção 3: chamar uma API da Web downstream sem a classe auxiliar

Se você decidiu adquirir um token manualmente usando o `ITokenAcquisition` serviço, agora precisará usar o token. Nesse caso, o código a seguir continua o código de exemplo mostrado em [uma API Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores de API. Ele chama uma API downstream chamada *ToDoList*.

 Depois de adquirir o token, use-o como um token de portador para chamar a API downstream.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

O código a seguir continua o código de exemplo mostrado em [uma API Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores de API. Ele chama o MS Graph da API downstream.

Depois de adquirir o token, use-o como um token de portador para chamar a API downstream.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Um exemplo que demonstra esse fluxo com o MSAL Python está disponível em [MS-Identity-Python-on-nome-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of).

---

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [vá para produção](scenario-web-api-call-api-production.md).
