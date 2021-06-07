---
title: Acessando informações do usuário no serviço Aplicativos Web Estáticos
description: Saiba como ler dados do usuário retornados pelo provedor de autorização.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: d5a1d810c357aa83b8069023b00d76352da124df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94844788"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Acessando informações do usuário no serviço Aplicativos Web Estáticos do Azure (versão prévia)

O serviço Aplicativos Web Estáticos do Azure fornece informações do usuário relacionadas à autenticação via um [ponto de extremidade de acesso direto](#direct-access-endpoint) e para [funções da API](#api-functions).

Muitas interfaces do usuário dependem expressivamente dos dados de autenticação de usuário. O ponto de extremidade de acesso direto é uma API utilitária que expõe informações do usuário sem a necessidade de implementar uma função personalizada. Além da conveniência, o ponto de extremidade de acesso direto não está sujeito a atrasos de inicialização a frios associados à arquitetura sem servidor.

## <a name="client-principal-data"></a>Dados da entidade de segurança do cliente.

O objeto de dados da entidade de segurança do cliente expõe informações de identificação do usuário para seu aplicativo. As propriedades a seguir são apresentadas no objeto de entidade de segurança do cliente:

| Propriedade  | Descrição |
|-----------|---------|
| `identityProvider` | O nome do [provedor de identidade](authentication-authorization.md). |
| `userId` | Um identificador exclusivo específico do serviço Aplicativo Web Estáticos do Azure do usuário. <ul><li>O valor é exclusivo por aplicativo. Por exemplo, o mesmo usuário retorna um valor `userId` diferente sobre um recurso diferente do serviço Aplicativos Web Estáticos.<li>O valor persiste pelo tempo de vida de um usuário. Se você excluir e adicionar o mesmo usuário no aplicativo, um novo `userId` será gerado.</ul>|
| `userDetails` | Nome ou endereço de email do usuário. Alguns provedores retornam o [endereço de email do usuário](authentication-authorization.md), enquanto outros enviam o [identificador do usuário](authentication-authorization.md). |
| `userRoles`     | Uma matriz das [funções atribuídas do usuário](authentication-authorization.md). |

O exemplo a seguir é de um objeto de entidade de segurança do cliente:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Ponto de extremidade de acesso direto

Você pode enviar uma solicitação de `GET` para a rota `/.auth/me` e receber acesso direto aos dados da entidade de segurança do cliente. Se o estado da sua exibição depender dos dados de autorização, use essa abordagem para obter o melhor desempenho.

Para usuários conectados, a resposta contém um objeto JSON da entidade de segurança do cliente. As solicitações de usuários não autenticados retornam `null`.

Com a API [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup>, você pode acessar os dados da entidade de segurança do cliente usando a sintaxe a seguir.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>Funções da API

As funções de API disponíveis em aplicativos Web estáticos por meio do back-end Azure Functions têm acesso às mesmas informações de usuário que um aplicativo cliente. Embora a API receba informações de identificação do usuário, ela não executa suas próprias verificações se o usuário é autenticado ou se corresponde a uma função necessária. As regras de controle de acesso são definidas no [`routes.json`](routes.md) arquivo.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os dados da entidade de segurança do cliente são passados para funções da API no cabeçalho da solicitação `x-ms-client-principal`. Os dados da entidade de segurança do cliente são enviados como uma cadeia de caracteres de codificação [Base64](https://www.wikipedia.org/wiki/Base64) que contém um objeto JSON serializado.

A função de exemplo a seguir mostra como ler e retornar informações do usuário.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Supondo que a função acima seja denominada `user`, você pode usar a API de navegador [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> para acessar a resposta da API usando a sintaxe a seguir.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

Em uma função C#, as informações do usuário estão disponíveis no `x-ms-client-principal` cabeçalho que pode ser desserializado em um `ClaimsPrincipal` objeto ou seu próprio tipo personalizado. O código a seguir demonstra como desempacotar o cabeçalho em um tipo intermediário, `ClientPrincipal` que é então transformado em uma `ClaimsPrincipal` instância do.

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var principal = new ClientPrincipal();

        if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
        {
            var data = header[0];
            var decoded = Convert.FromBase64String(data);
            var json = Encoding.ASCII.GetString(decoded);
            principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
        }

        principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

        if (!principal.UserRoles?.Any() ?? true)
        {
            return new ClaimsPrincipal();
        }

        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> A API [fetch](https://caniuse.com/#feat=fetch) e o operador [await](https://caniuse.com/#feat=mdn-javascript_operators_await) não são compatíveis com o Internet Explorer.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Definir as configurações do aplicativo](application-settings.md)
