---
title: "Introdução à API Web .NET do Azure AD | Microsoft Docs"
description: "Como compilar uma API Web do .NET MVC que se integre ao Azure AD para autenticação e autorização."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: f44d75f45073a5d9aa9b1863ed227aba4efcf785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="help-protect-a-web-api-by-using-bearer-tokens-from-azure-ad"></a>Proteger uma API Web usando os tokens de portador do Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Se você estiver criando um aplicativo que fornece acesso a recursos protegidos, você precisará saber como proteger esses recursos de acessos não garantidos.
O Azure AD (Azure Active Directory) faz com que seja simples e direto ajudar a proteger uma API Web usando tokens de acesso de portador do OAuth 2.0 com apenas algumas linhas de código.

Em aplicativos Web ASP.NET, você pode executar essa proteção usando a implementação da Microsoft do middleware OWIN voltado à comunidade, incluído no .NET Framework 4.5. Aqui, você usará o OWIN para compilar uma API Web de “Lista de Tarefas Pendentes” que:

* Designa quais APIs estão protegidas.
* Valida que as chamadas à API Web contêm um token de acesso válido.

Para criar a API de lista de tarefas pendentes, você precisa primeiro:

1. Registrar um aplicativo com o Azure AD.
2. Configurar o aplicativo para usar o pipeline de autenticação OWIN.
3. Configurar um aplicativo cliente para chamar a API Web.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Cada um é uma solução do Visual Studio 2013. Você também precisa de um locatário do Azure AD no qual registrar seu aplicativo. Se você ainda não tiver um locatário, [saiba como obter um](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Etapa 1: registrar um aplicativo com o Azure AD
Para proteger seu aplicativo, você primeiro precisará criar um aplicativo em seu locatário e fornecer algumas informações cruciais ao Azure AD.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Na barra superior, clique em sua conta. Na lista **Diretório**, escolha o locatário do Azure AD no qual você quer registrar seu aplicativo.

3. Clique em **Mais Serviços** no painel esquerdo e selecione **Azure Active Directory**.

4. Clique em **Registros do aplicativo** e, em seguida, selecione **Adicionar**.

5. Siga os prompts e crie um novo **aplicativo Web e/ou API Web**.
  * **Nome** descreve seu aplicativo para os usuários. Digite **Serviço de Lista de Tarefas Pendentes**.
  * O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o Azure AD usa para retornar tokens solicitados pelo aplicativo. Digite `https://localhost:44321/` para esse valor.

6. Na página **Configurações** -> **Propriedades** do aplicativo, atualize o URI da ID do Aplicativo. Insira um identificador específico ao locatário. Por exemplo, insira: `https://contoso.onmicrosoft.com/TodoListService`.

7. Salve a configuração. Deixe o portal aberto, pois você também precisará registrar seu aplicativo cliente em breve.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Etapa 2: configurar o aplicativo para usar o pipeline de autenticação OWIN
Para validar tokens e solicitações de entrada, você precisa configurar seu aplicativo para se comunicar com o Azure AD.

1. Para começar, abra a solução e adicione os pacotes do NuGet de middleware OWIN ao projeto TodoListService usando o Console do Gerenciador de Pacotes.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Adicionar uma classe de inicialização OWIN no projeto TodoListService chamado `Startup.cs`.  Clique com o botão direito do mouse no projeto, selecione **Adicionar** > **Novo Item** e pesquise por **OWIN**. O middleware OWIN invocará o método `Configuration(…)` quando seu aplicativo for iniciado.

3. Altere a declaração de classe para `public partial class Startup`. Já implementamos parte dessa classe para você em outro arquivo. No método `Configuration(…)`, faça uma chamada para `ConfgureAuth(…)` para configurar a autenticação para seu aplicativo Web.

    ```C#
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(…)`. Os parâmetros que você fornecer em `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servirão como coordenadas para seu aplicativo para se comunicar com o Azure AD.

    ```C#
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Agora você pode usar atributos `[Authorize]` para proteger os controladores e ações com a autenticação de portador JWT (Token Web JSON). Decore a classe `Controllers\TodoListController.cs` com uma marca de autorização. Isso forçará o usuário a entrar antes de acessar essa página.

    ```C#
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Quando um chamador autorizado invoca com êxito uma das `TodoListController` APIs, a ação pode precisar ter acesso às informações sobre o chamador. O OWIN fornece acesso às declarações dentro do token de portador por meio do objeto `ClaimsPrincpal` .  

6. Um requisito comum para APIs Web é validar os “escopos” presentes no token. Isso garante que o usuário consentiu em conceder as permissões necessárias para acessar o serviço de Lista de Tarefas Pendentes.

    ```C#
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Abra o arquivo `web.config` na raiz do projeto TodoListService e insira os valores de configuração na seção `<appSettings>`.
  * `ida:Tenant` é o nome do seu locatário do Azure AD – por exemplo, contoso.onmicrosoft.com.
  * `ida:Audience` é o URI de ID do aplicativo que você inseriu no Portal do Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Etapa 3: Configurar um aplicativo cliente e executar o serviço
Antes de poder ver o serviço de Lista de Tarefas Pendentes funcionando, você precisa configurar o cliente de Lista de Tarefas Pendentes para que ele possa obter tokens do Azure AD e fazer chamadas para o serviço.

1. Retorne ao [Portal do Azure](https://portal.azure.com).

2. Crie um novo aplicativo no locatário do AD do Azure e selecione **aplicativo cliente nativo** no prompt resultante.
  * **Nome** descreve seu aplicativo para os usuários.
  * Digite `http://TodoListClient/` para o valor **URI de redirecionamento** .

3. Depois de concluir o registro, o Azure AD atribui uma identificação exclusiva do aplicativo ao seu aplicativo. Você precisará desse valor nas próximas etapas, portanto, copie-o da página do aplicativo.

4. Na página **Configurações**, selecione **Permissões necessárias** e escolha **Adicionar**. Localize e selecione o serviço de Lista de Tarefas Pendentes, adicione a permissão **Acessar TodoListService** em **Permissões Delegadas** e clique em **Concluído**.

5. No Visual Studio, abra `App.config` no projeto TodoListClient e insira seus valores de configuração na seção `<appSettings>`.

  * `ida:Tenant` é o nome do seu locatário do Azure AD – por exemplo, contoso.onmicrosoft.com.
  * `ida:ClientId` é a ID do aplicativo que você copiou do Portal do Azure.
  * `todo:TodoListResourceId` é o URI da ID do aplicativo de serviço de Lista de Tarefas Pendentes que você inseriu no Portal do Azure.

## <a name="next-steps"></a>Próximas etapas
Por fim, limpe, compile e execute cada projeto. Se você ainda não fez isso, agora é o momento de criar um novo usuário em seu locatário com um domínio *.onmicrosoft.com. Entre no cliente de lista de tarefas com esse usuário e adicione algumas tarefas na lista de tarefas pendentes do usuário.

Para referência, a amostra concluída (sem seus valores de configuração) está disponível no [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Agora, você pode seguir para cenários de identidade.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
