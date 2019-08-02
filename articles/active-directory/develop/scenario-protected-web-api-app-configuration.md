---
title: API da web - configuração de código de aplicativo protegida | Azure
description: Saiba como compilar uma API web protegida e configurar o código do seu aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536898"
---
# <a name="protected-web-api-code-configuration"></a>API web protegida: Configuração do código

Para configurar o código para sua API web protegida, você precisa entender o que define as APIs como protegido, como configurar um token de portador e como validar o token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>O que define as APIs do Core ASP.NET/ASP.NET como protegido?

Como os aplicativos web, APIs da web Core ASP.NET/ASP.NET são "protegido" porque suas ações do controlador são prefixadas com o `[Authorize]` atributo. Portanto, as ações do controlador podem ser chamadas somente se a API é chamada com uma identidade que está autorizada.

Considere as seguintes perguntas:

- Como a API da web sabe a identidade do aplicativo que faz a chamada? (Apenas um aplicativo pode chamar uma API da web).
- Se o aplicativo chamado a API da web em nome do usuário, o que é a identidade do usuário?

## <a name="bearer-token"></a>Token de portador

As informações sobre a identidade do aplicativo e sobre o usuário (a menos que o aplicativo web aceita chamadas de serviço a serviço de um aplicativo daemon), é mantido no token de portador é definido no cabeçalho quando o aplicativo é chamado.

Aqui está um C# exemplo de código que mostra um cliente chamar a API depois que ele adquire um token com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> O token de portador foi solicitado por um aplicativo cliente para o ponto de extremidade de plataforma do Microsoft identity *para a API web*. A API da web é o único aplicativo que deve verificar o token e exibir as declarações que ele contém. Aplicativos cliente nunca devem tentar inspecionar as declarações em tokens. (A API da web pode exigir, no futuro, que o token seja criptografado. Esse requisito impediria o acesso para aplicativos cliente que pode exibir os tokens de acesso.)

## <a name="jwtbearer-configuration"></a>Configuração de JwtBearer

Esta seção descreve como configurar um token de portador.

### <a name="config-file"></a>Arquivo de configuração

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Código de inicialização

Quando um aplicativo é chamado em uma ação do controlador que contém um `[Authorize]` atributo, Core ASP.NET/ASP.NET examina o token de portador no cabeçalho de autorização da solicitação de chamada e extrai o token de acesso. O token é então encaminhado para o middleware JwtBearer, que chama IdentityModel extensões da Microsoft para .NET.

No ASP.NET Core, este middleware é inicializado no arquivo Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API da web por essa instrução:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos do ASP.NET Core criam APIs que a entrada da web de Azure Active Directory (Azure AD) em que os usuários em sua organização ou por qualquer organização, não com contas pessoais. Mas você pode alterá-los para usar o ponto de extremidade de plataforma do Microsoft identity, adicionando este código ao arquivo Startup.cs facilmente:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Validação de token

O middleware JwtBearer, como o middleware OpenID Connect em aplicativos web, é direcionado por `TokenValidationParameters` para validar o token. O token for descriptografado (conforme necessário), as declarações são extraídas e a assinatura é verificada. O middleware valida o token verificando se esses dados:

- Ele é direcionado para a web API (público).
- Ele foi emitido para um aplicativo que tem permissão para chamar a API (sub) da web.
- Ele foi emitido por um serviço de token de segurança (STS) (emissor).
- Seu tempo de vida está no intervalo (validade).
- Ele não foi violado (assinatura).

Também pode haver validação especial. Por exemplo, é possível validar que as chaves de assinatura (quando inserido em um token) são confiáveis e que o token não está sendo repetido. Por fim, alguns protocolos exigem validações específicas.

### <a name="validators"></a>Validadores

As etapas de validação são capturadas nos validadores, o qual estão todos na [IdentityModel extensões da Microsoft para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteca de código-fonte aberto, em um arquivo de origem: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Os validadores são descritos nesta tabela:

| Validador | DESCRIÇÃO |
|---------|---------|
| `ValidateAudience` | Garante que o token é o aplicativo valida o token (para mim). |
| `ValidateIssuer` | Garante que o token foi emitido por um STS confiáveis (de alguém que confio). |
| `ValidateIssuerSigningKey` | Garante que o aplicativo validar as relações de confiança de token a chave que foi usada para assinar o token. (Caso especial em que a chave é inserida no token. Não costuma ser obrigatório.) |
| `ValidateLifetime` | Garante que o token é válido ainda (ou já). O validador verifica se o tempo de vida do token (`notbefore` e `expires` declarações) está no intervalo. |
| `ValidateSignature` | Garante que o token não foi adulterado. |
| `ValidateTokenReplay` | Garante que o token não é reproduzido. (Caso especial para alguns protocolos de uso onetime). |

Os validadores são todos associados a propriedades do `TokenValidationParameters` classe em si inicializado da configuração ASP.NET/ASP.NET Core. Na maioria dos casos, você não precisará alterar os parâmetros. Há uma exceção, para aplicativos que não são de locatário único. (Ou seja, aplicativos que aceitam os usuários de qualquer organização ou de contas pessoais da Microsoft de web). Nesse caso, o emissor deve ser validado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Verifique se a escopos e funções de aplicativo no seu código](scenario-protected-web-api-verification-scope-app-roles.md)
