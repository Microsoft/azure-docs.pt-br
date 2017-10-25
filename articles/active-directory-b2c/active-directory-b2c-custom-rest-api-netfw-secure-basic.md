---
title: "Azure Active Directory B2C: proteja seus serviços RESTful usando autenticação básica HTTP"
description: "Exemplo de como proteger suas trocas de declarações da API REST personalizadas no Azure AD B2C usando a autenticação básica HTTP"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C: proteja seus serviços RESTful usando autenticação básica HTTP
Em um [artigo relacionado](active-directory-b2c-custom-rest-api-netfw.md), criamos um serviço RESTful (API da Web) que se integra aos percursos do usuário do Azure AD B2C sem autenticação. Este artigo mostra como adicionar autenticação básica HTTP ao serviço RESTful para que somente usuários verificados, incluindo B2C, possam acessar a API. Com a autenticação básica HTTP, você pode definir as credenciais do usuário (ID do aplicativo e segredo do aplicativo) em sua política personalizada para usar as credenciais. 

> [!NOTE]
>
>O artigo a seguir descreve como fazer a [Autenticação Básica na API Web ASP.NET](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication) em mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos
Conclua as etapas anteriores no artigo [Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md)

## <a name="step-1-add-authentication-support"></a>Etapa 1: Adicionar suporte à autenticação

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>Etapa 1.1 Adicionar configurações do aplicativo ao arquivo web.config do projeto
1. Abra seu projeto do Visual Studio criado anteriormente 
2. Adicione as seguintes configurações do aplicativo ao arquivo web.config no elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Crie uma senha e defina o valor `WebApp:ClientSecret`

    Para gerar senha complexa, você pode executar o PowerShell a seguir. Mas você pode usar qualquer valor arbitrário.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Etapa 1.2 Instalar bibliotecas OWIN
Para começar, adicione os pacotes do NuGet de middleware do OWIN ao projeto usando o Console do Gerenciador de Pacotes do Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-authentication-middleware-class"></a>Etapa 1.3 Adicionar classe do middleware de autenticação
Adicione a classe `ClientAuthMiddleware.cs` na pasta `App_Start`. Clique com o botão direito do mouse na pasta `App_Start`, selecione **Adicionar** e, em seguida, selecione **classe**

![Adicionar classe ClientAuthMiddleware.cs na pasta App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

Digite `ClientAuthMiddleware.cs` para o nome de classe

![Criar nova classe C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

Abra o arquivo `App_Start\ClientAuthMiddleware.cs` e substitua o conteúdo do arquivo pelo código a seguir:


```C#

using Microsoft.Owin;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Principal;
using System.Text;
using System.Threading.Tasks;
using System.Web;

namespace Contoso.AADB2C.API
{
    /// <summary>
    /// Class to create a custom owin middleware to check for client authentication
    /// </summary>
    public class ClientAuthMiddleware
    {
        private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
        private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

        /// <summary>
        /// Gets or sets the next owin middleware
        /// </summary>
        private Func<IDictionary<string, object>, Task> Next { get; set; }

        /// <summary>
        /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
        /// </summary>
        /// <param name="next"></param>
        public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
        {
            this.Next = next;
        }

        /// <summary>
        /// Invoke client authentication middleware during each request.
        /// </summary>
        /// <param name="environment">Owin environment</param>
        /// <returns></returns>
        public Task Invoke(IDictionary<string, object> environment)
        {
            // Get wrapper class for the environment
            var context = new OwinContext(environment);

            // Check whether the authorization header is available. This contains the credentials.
            var authzValue = context.Request.Headers.Get("Authorization");
            if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
            {
                // Process next middleware
                return Next(environment);
            }

            // Get credentials
            var creds = authzValue.Substring("Basic ".Length).Trim();
            string clientId;
            string clientSecret;

            if (RetrieveCreds(creds, out clientId, out clientSecret))
            {
                // Set transaction authenticated as client
                context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
            }

            return Next(environment);
        }

        /// <summary>
        /// Retrieve credentials from header
        /// </summary>
        /// <param name="credentials">Authorization header</param>
        /// <param name="clientId">Client identifier</param>
        /// <param name="clientSecret">Client secret</param>
        /// <returns>True if valid credentials were presented</returns>
        private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
        {
            string pair;
            clientId = clientSecret = string.Empty;

            try
            {
                pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
            }
            catch (FormatException)
            {
                return false;
            }
            catch (ArgumentException)
            {
                return false;
            }

            var ix = pair.IndexOf(':');
            if (ix == -1)
            {
                return false;
            }

            clientId = pair.Substring(0, ix);
            clientSecret = pair.Substring(ix + 1);

            // Return whether credentials are valid
            return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
        }
    }
}
```

### <a name="step-14-add-an-owin-startup-class"></a>Etapa 1.4 Adicionar uma classe de inicialização da OWIN
Adicione uma classe de inicialização OWIN para a API chamada `Startup.cs`. Clique com o botão direito do mouse no projeto, selecione **Adicionar** e **Novo Item** e pesquise OWIN.

![Adicionar uma classe de inicialização da OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

Abra o arquivo `Startup.cs` e substitua o conteúdo do arquivo pelo código a seguir:

```C#
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
namespace Contoso.AADB2C.API
{
    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
                app.Use<ClientAuthMiddleware>();
        }
    }
}
```

### <a name="step-15-protect-identity-api-class"></a>Etapa 1.5 Proteger a classe de API de Identidade
Abra Controllers\IdentityController.cs e adicione a marca `[Authorize]` à classe do controlador. A marca `[Authorize]` restringe o acesso ao controlador para usuários que atendem ao requisito de autorização.

![adicione a marca [Autorizar] ao controlador](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Etapa 2: Publicar no Azure
Para publicar seu projeto, no **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Contoso.AADB2C.API** e selecione **Publicar**.

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>Etapa 3: Adicionar o segredo de aplicativo e a ID do aplicativo de serviços RESTful ao Azure AD B2C
Depois que seu serviço RESTful estiver protegido pela ID do cliente (nome de usuário) e o segredo do cliente, você precisará armazenar as credenciais no seu locatário do Azure AD B2C. Sua política personalizada fornece as credenciais enquanto invoca seus serviços RESTful.  

### <a name="step-31-add-restful-services-client-id"></a>Etapa 3.1 Adicionar ID de cliente de serviços RESTful
1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Manual**.
5.  Para o **Nome**, use `B2cRestClientId`.  
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.
6.  Na caixa **Segredo**, digite a ID do aplicativo definida anteriormente
7.  Para o **Uso da chave**, use **Segredo**.
8.  Clique em **Criar**
9.  Confirme que você criou a chave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-restful-services-client-secret"></a>Etapa 3.2 Adicionar segredo do cliente de serviços RESTful
1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Manual**.
5.  Para o **Nome**, use `B2cRestClientSecret`.  
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.
6.  Na caixa **Segredo**, digite o segredo do aplicativo definido anteriormente
7.  Para o **Uso da chave**, use **Segredo**.
8.  Clique em **Criar**
9.  Confirme que você criou a chave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>Etapa 4: Altere `TechnicalProfile` para dar suporte à autenticação básica em sua política de extensão
1.  Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho.
2.  Localize o nó `<TechnicalProfile>` que é incluído com `Id="REST-API-SignUp"`
3.  Localize o elemento `<Metadata>`
4.  Altere `AuthenticationType` para `Basic`
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  Adicione o seguinte trecho XML imediatamente após o fechamento do elemento `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
Depois de adicionar trechos XML, seu `TechnicalProfile` deve se parecer com:

![Adicionar elementos XML de autenticação básica](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Etapa 5: Carregar a política para o seu locatário

1.  No [Portal do Azure](https://portal.azure.com), mude para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e clique em **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Clique em **Todas as Políticas**.
4.  Selecione **Carregar Política**
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Etapa 6: Testar a política personalizada usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >
    >    A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. 
    >    Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Tente digitar "Teste" no campo **Nome**; o B2C exibe a mensagem de erro na parte superior da página

    ![Testar sua API de identidade](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Tente digitar um nome (diferente de "teste") no campo **Nome**. O B2C inscreve o usuário e, em seguida, envia loyaltyNumber para seu aplicativo. Observe o número neste JWT no exemplo.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="next-steps"></a>Próximas etapas
* [Usar certificados de cliente para proteger a API RESTful](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Baixar os arquivos e código da política completa
* Recomendamos a criação de seu cenário usando seus próprios arquivos de política Personalizada após a conclusão das instruções passo a passo Introdução às políticas personalizadas, em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* Você pode baixar o código completo aqui [Amostra de solução do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)