---
title: Azure AD B2C | Microsoft Docs
description: "Como compilar uma API Web do .NET usando o Azure Active Directory B2C, protegido com tokens de acesso OAuth 2.0 para autenticação."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 78a165d831796bb6bb23e51f415383eb925115ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: criar uma API Web do .NET

Com o Active Directory B2C do Azure (AD do Azure), você pode proteger uma API Web usando tokens de acesso do OAuth 2.0. Esses tokens permitem que os aplicativos cliente se autentiquem na API. Este artigo mostra como criar uma API de "lista de tarefas pendentes" MVC .NET que permite que os usuários do aplicativo cliente executem CRUD para as tarefas. A API Web é protegida usando o Azure AD B2C e permite que apenas usuários autenticados gerenciem sua lista de tarefas pendentes.

## <a name="create-an-azure-ad-b2c-directory"></a>Criar um diretório do AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

> [!NOTE]
> O aplicativo cliente e a API Web devem usar o mesmo diretório do Azure AD B2C.
>

## <a name="create-a-web-api"></a>Criar uma API da Web

Em seguida, você precisa criar um aplicativo de API Web no diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

* Inclua um **aplicativo Web** ou uma **API Web** no aplicativo.
* Use o **URI de redirecionamento** `https://localhost:44332/` para o aplicativo Web. Esse é o local padrão do aplicativo Web cliente para este exemplo de código.
* Copie a **ID de aplicativo** atribuída ao aplicativo. Você precisará dela mais tarde.
* Insira um identificador de aplicativo em **URI da ID do aplicativo**. Copie o **URI da ID do Aplicativo** completo. Você precisará dela mais tarde.
* Adicione permissões por meio do menu **Escopos publicados**.

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Você precisará criar uma política para se comunicar com o Azure AD B2C. É recomendável usar a política combinada de inscrição/entrada, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Ao criar a política, não se esqueça de:

* Escolher o **Nome de exibição** e outros atributos de inscrição na política.
* Escolher as declarações **Nome de exibição** e **ID de objeto** como declarações de aplicativo para cada política. Você pode escolher outras declarações também.
* Copie o **Nome** de cada política depois de criá-la. Posteriormente, você precisará do nome da política.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Após criar a política com êxito, você estará pronto para compilar o aplicativo.

## <a name="download-the-code"></a>Baixar o código

O código para este tutorial é mantido no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Você pode clonar a amostra executando:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. Agora, sua solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp` é um aplicativo Web de MVC com o qual o usuário interage. `TaskService` é API Web back-end do aplicativo que armazena a lista de tarefas de cada usuário. Este artigo discutirá apenas o aplicativo `TaskService`. Para saber como criar `TaskWebApp` usando o Azure AD B2C, confira [nosso tutorial do aplicativo Web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Atualizar a configuração do Azure AD B2C

O exemplo é configurado para usar as políticas e a ID do cliente da demonstração. Se quiser usar seu próprio locatário, você precisará fazer o seguinte:

1. Abra `web.config` no projeto `TaskService` e substitua os valores de
    * `ida:Tenant` pelo nome do locatário
    * `ida:ClientId` com a ID do aplicativo de API da Web
    * `ida:SignUpSignInPolicyId` pelo nome da política "Inscrever-se ou Entrar"

2. Abra `web.config` no projeto `TaskWebApp` e substitua os valores de
    * `ida:Tenant` pelo nome do locatário
    * `ida:ClientId` pela ID de aplicativo do aplicativo Web
    * `ida:ClientSecret` pela chave de segredo do aplicativo Web
    * `ida:SignUpSignInPolicyId` pelo nome da política "Inscrever-se ou Entrar"
    * `ida:EditProfilePolicyId` pelo nome de política "Editar Perfil"
    * `ida:ResetPasswordPolicyId` pelo nome de política "Redefinir Senha"
    * `api:ApiIdentifier` com o “URI de ID do aplicativo"


## <a name="secure-the-api"></a>Proteger a API

Quando tem um cliente que chama a API, você pode proteger a API (por exemplo `TaskService`) usando os tokens de portador OAuth 2.0. Isso garante que cada solicitação à API seja válida apenas se a solicitação tiver um token de portador. Sua API pode aceitar e validar tokens de portador usando a biblioteca OWIN (Open Web Interface para .NET) da Microsoft.

### <a name="install-owin"></a>Instalar a OWIN

Comece instalando o pipeline de autenticação OWIN OAuth usando o Visual Studio Package Manager Console.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Isso instalará o middleware OWIN, que aceitará e validará os tokens de portador.

### <a name="add-an-owin-startup-class"></a>Adicionar uma classe de inicialização da OWIN

Adicione uma classe de inicialização OWIN para a API chamada `Startup.cs`.  Clique com o botão direito do mouse no projeto, selecione **Adicionar** e **Novo Item** e pesquise OWIN. O middleware OWIN invocará o método `Configuration(…)` quando seu aplicativo for iniciado.

Em nossa amostra, alteramos a declaração de classe para `public partial class Startup` e implementamos a outra parte da classe em `App_Start\Startup.Auth.cs`. No método `Configuration`, adicionamos uma chamada para `ConfigureAuth`, que é definida em `Startup.Auth.cs`. Após as modificações, `Startup.cs` é semelhante ao seguinte:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurar a autenticação OAuth 2.0

Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`. Por exemplo, ele pode ser semelhante ao seguinte:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Proteger o controlador de tarefa

Após a configuração do aplicativo para usar a autenticação OAuth 2.0, você pode proteger a API Web adicionando uma marca `[Authorize]` ao controlador de tarefa. Este é o controlador onde ocorre toda a manipulação da lista de tarefas, portanto, proteja todo o controlador no nível da classe. Você também pode adicionar a marca `[Authorize]` a ações individuais para obter um controle mais refinado.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obter informações de usuário do token

`TasksController` armazena tarefas em um banco de dados, no qual cada tarefa tem um usuário associado que é o "proprietário" da tarefa. O proprietário é identificado pela **ID de objeto**do usuário. (É por isso que você precisa adicionar a ID do objeto como uma declaração de aplicativo em todas as suas políticas.)

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Validar as permissões no token

Um requisito comum para APIs Web é validar os “escopos” presentes no token. Isso garante que o usuário consentiu em conceder as permissões necessárias para acessar o serviço de lista de tarefas pendentes.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Por fim, compile e execute `TaskWebApp` e `TaskService`. Crie algumas tarefas na lista de tarefas do usuário e observe como elas são persistentes na API, mesmo depois que você para e reinicia o cliente.

## <a name="edit-your-policies"></a>Editar suas políticas

Depois de proteger uma API usando o AD B2C do Azure, experimente a política de Inscrição/Entrada e veja o efeito (ou a falta dele) na API. Você pode manipular as declarações do aplicativo nas políticas e alterar as informações do usuário que estão disponíveis na API Web. Quaisquer declarações que você adicionar estarão disponíveis para a API Web de MVC do .NET no objeto `ClaimsPrincipal` , conforme descrito acima.
