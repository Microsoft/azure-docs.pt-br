---
title: Chamar uma API da Web de um aplicativo Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo Web que chama APIs da Web (chamando uma API Web protegida)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753293"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Um aplicativo Web que chama APIs da Web: chamar uma API da Web

Agora que você tem um token, você pode chamar uma API Web protegida. Normalmente, você chama uma API downstream do controlador ou das páginas do seu aplicativo Web.

## <a name="call-a-protected-web-api"></a>Chamar uma API Web protegida

Chamar uma API Web protegida depende do idioma e da estrutura de sua escolha:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ao usar *o Microsoft. Identity. Web*, você tem três opções de uso para chamar uma API:

- [Opção 1: chamar Microsoft Graph com o SDK do Microsoft Graph](#option-1-call-microsoft-graph-with-the-sdk)
- [Opção 2: chamar uma API da Web downstream com a classe auxiliar](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opção 3: chamar uma API da Web downstream sem a classe auxiliar](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opção 1: chamar Microsoft Graph com o SDK

Você deseja chamar Microsoft Graph. Nesse cenário, você adicionou `AddMicrosoftGraph` o *Startup.cs* conforme especificado na configuração de [código](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)e pode injetar diretamente o `GraphServiceClient` no seu controlador ou construtor de página para uso nas ações. A página Razor de exemplo a seguir exibe a foto do usuário conectado.

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

Você deseja chamar uma API da Web diferente de Microsoft Graph. Nesse caso, você adicionou o `AddDownstreamWebApi` *Startup.cs* conforme especificado na configuração de [código](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), e você pode injetar um `IDownstreamWebApi` serviço diretamente no seu controlador ou construtor de página e usá-lo nas ações:

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
}
```

O `CallWebApiForUserAsync` também tem substituições genéricas fortemente tipadas que permitem que você receba diretamente um objeto. Por exemplo, o método a seguir recebe uma `Todo` instância, que é uma representação fortemente tipada do JSON retornado pela API da Web.

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

Você decidiu adquirir um token manualmente usando o `ITokenAcquisition` serviço e agora precisa usar o token. Nesse caso, o código a seguir continua o código de exemplo mostrado em [um aplicativo Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-app-call-api-acquire-token.md). O código é chamado nas ações dos controladores de aplicativo Web.

Depois de adquirir o token, use-o como um token de portador para chamar a API downstream, neste caso Microsoft Graph.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> Você pode usar o mesmo princípio para chamar qualquer API da Web.
>
> A maioria das APIs da Web do Azure fornece um SDK que simplifica a chamada da API, como é o caso para Microsoft Graph. Consulte, por exemplo, [criar um aplicativo Web que autorize o acesso ao armazenamento de BLOBs com o Azure ad](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter um exemplo de um aplicativo Web usando Microsoft. Identity. Web e usando o SDK de armazenamento do Azure.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [vá para produção](scenario-web-app-call-api-production.md).