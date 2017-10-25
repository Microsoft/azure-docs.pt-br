---
title: Azure Active Directory B2C | Microsoft Docs
description: Como compilar um aplicativo Web .NET e chamar uma API Web usando os tokens de acesso do OAuth 2.0 e Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 48452eb68f826d1c7aa61d5e5531f941ac1422b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: chamar uma API Web de um aplicativo Web .NET

Usando o Azure AD B2C, você pode adicionar recursos poderosos de gerenciamento de identidades a seus aplicativos Web e APIs Web. Este artigo descreve como solicitar tokens de acesso e fazer chamadas de um aplicativo Web de “lista de tarefas pendentes” do .NET para uma API Web .NET.

Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Se você ainda não fez isto, você deverá:

* Começar com um [aplicativo Web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Começar com uma [API Web .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Pré-requisito

Para criar um aplicativo Web que chame uma API Web, você deve:

1. [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md).
2. [Registrar uma api Web](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Registrar um aplicativo Web](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Configurar políticas](active-directory-b2c-reference-policies.md).
5. [Conceder permissões para usar a API Web ao aplicativo Web](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> O aplicativo cliente e a API Web devem usar o mesmo diretório do Azure AD B2C.
>

## <a name="download-the-code"></a>Baixar o código

O código para este tutorial é mantido no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Você pode clonar a amostra executando:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. Agora, sua solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp` é um aplicativo Web MVC com o qual o usuário interage. `TaskService` é API Web back-end do aplicativo que armazena a lista de tarefas de cada usuário. Este artigo não abrange a criação do aplicativo Web `TaskWebApp` nem da API Web `TaskService`. Para saber como criar um aplicativo Web .NET usando o Azure AD B2C, confira [nosso tutorial do aplicativo Web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Para saber como criar uma API Web .NET protegida usando o Azure AD B2C, confira [nosso tutorial da API Web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Atualizar a configuração do Azure AD B2C

Nossa amostra é configurada para usar as políticas e a ID do cliente de nosso locatário de demonstração. Se você quiser usar seu próprio locatário:

1. Abra `web.config` no projeto `TaskService` e substitua os valores de

    * `ida:Tenant` pelo nome do locatário
    * `ida:ClientId` pela ID do aplicativo da API Web
    * `ida:SignUpSignInPolicyId` pelo nome da política "Inscrever-se ou Entrar"

2. Abra `web.config` no projeto `TaskWebApp` e substitua os valores de

    * `ida:Tenant` pelo nome do locatário
    * `ida:ClientId` pela ID de aplicativo do aplicativo Web
    * `ida:ClientSecret` pela chave de segredo do aplicativo Web
    * `ida:SignUpSignInPolicyId` pelo nome da política "Inscrever-se ou Entrar"
    * `ida:EditProfilePolicyId` pelo nome de política "Editar Perfil"
    * `ida:ResetPasswordPolicyId` pelo nome de política "Redefinir Senha"



## <a name="requesting-and-saving-an-access-token"></a>Solicitar e salvar um token de acesso

### <a name="specify-the-permissions"></a>Especificar as permissões

Para fazer a chamada para a API Web, você precisa autenticar o usuário (usando a política de inscrever-se ou entrar) e [receber um token de acesso](active-directory-b2c-access-tokens.md) do Azure AD B2C. Para receber um token de acesso, você deve primeiro especificar as permissões que você deseja que sejam concedidas pelo token de acesso. As permissões são especificadas no parâmetro `scope` quando você faz a solicitação para o ponto de extremidade `/authorize`. Por exemplo, para obter um token de acesso com a permissão de "leitura" para o aplicativo de recurso que tem o URI de ID do aplicativo de `https://contoso.onmicrosoft.com/tasks`, o escopo seria `https://contoso.onmicrosoft.com/tasks/read`.

Para especificar o escopo em nosso exemplo, abra o arquivo `App_Start\Startup.Auth.cs` e defina a variável `Scope` em OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Permutar o código de autorização de um token de acesso

Após um usuário concluir a experiência de inscrição ou entrada, seu aplicativo receberá um código de autorização do Azure AD B2C. O middleware OWIN OpenID Connect armazenará o código, mas não o permutará por um token de acesso. Você pode usar a [biblioteca MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) para realizar a permuta. Em nosso exemplo, configuramos um retorno de chamada de notificação para o middleware OpenID Connect sempre que um código de autorização é recebido. No retorno de chamada, usamos o MSAL para permutar o código por um token e salvar o token no cache.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Chamar a API da web

Esta seção discute como usar o token recebido durante a inscrição/entrada com o Azure AD B2C para acessar a API Web.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Recuperar o token salvo nos controladores

O `TasksController` é responsável pela comunicação com a API Web e por enviar solicitações HTTP à API para ler, criar e excluir tarefas. Já que a API é protegida pelo Azure AD B2C, primeiro você precisa recuperar o token que salvou na etapa anterior.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Ler tarefas da API Web

Quando você tiver um token, poderá anexá-lo à solicitação `GET` HTTP no cabeçalho `Authorization` para chamar com segurança o `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Criar e excluir tarefas na API Web

Siga o mesmo padrão ao enviar solicitações de `POST` e `DELETE` à API Web, usando o MSAL para recuperar o token de acesso do cache.

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Finalmente, compile e execute ambos os aplicativos. Inscreva-se e entre e crie tarefas para o usuário conectado. Saia e entre como outro usuário. Crie tarefas para esse usuário. Observe como as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token que recebe. Além disso, experimente mudar os escopos. Remova a permissão de "gravação" e, em seguida, tente adicionar uma tarefa. Apenas certifique-se de sair sempre que você alterar o escopo.

