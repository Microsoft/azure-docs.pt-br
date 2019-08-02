---
title: Proteger seus serviços RESTful usando autenticação básica HTTP no Azure Active Directory B2C | Microsoft Docs
description: Proteger suas trocas de declarações da API REST personalizadas no Azure AD B2C usando a autenticação básica HTTP.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8c1251056ad816af664f95abcd18d50ceca4619d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835275"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Proteja seus serviços RESTful usando a autenticação básica HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Em um [artigo relacionado do Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md), você criará um serviço RESTful (API Web) que se integra aos percursos do usuário do Azure AD B2C (Azure Active Directory B2C) sem autenticação.

Este artigo mostra como adicionar autenticação básica HTTP ao serviço RESTful para que somente usuários verificados, incluindo o B2C, possam acessar a API. Com a autenticação básica HTTP, é possível definir as credenciais do usuário (ID e segredo do aplicativo) em sua política personalizada.

Para obter mais informações, consulte [Autenticação básica na ASP.NET Web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Pré-requisitos

Execute as etapas descritas no artigo [Integrar as trocas de declarações da API REST ao percurso do usuário do Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md).

## <a name="step-1-add-authentication-support"></a>Etapa 1: Adicionar suporte a autenticação

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Etapa 1.1: Adicionar configurações de aplicativo ao arquivo web.config do projeto

1. Abra o projeto do Visual Studio criado anteriormente.

2. Adicione as seguintes configurações de aplicativo ao arquivo web.config no elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Crie uma senha e defina o valor `WebApp:ClientSecret`.

    Para gerar senha complexa, execute o código do PowerShell a seguir. É possível usar qualquer valor arbitrário.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Etapa 1.2: Instalar bibliotecas OWIN

Para começar, adicione os pacotes do NuGet de middleware do OWIN ao projeto usando o Console do Gerenciador de Pacotes do Visual Studio:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Etapa 1.3: Adicionar uma classe do middleware de autenticação

Adicione a classe `ClientAuthMiddleware.cs` à pasta *App_Start*. Para fazer isso:

1. Clique com o botão direito do mouse na pasta *App_Start*, selecione **Adicionar** e selecione **Classe**.

   ![Adicionar classe ClientAuthMiddleware.cs à pasta App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Na caixa **Nome**, digite **ClientAuthMiddleware.cs**.

   ![Criando um novo C# classe na caixa de diálogo Adicionar Novo Item no Visual Studio](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Abra o arquivo *App_Start\ClientAuthMiddleware.cs* e substitua o conteúdo do arquivo pelo código a seguir:

    ```csharp

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

### <a name="step-14-add-an-owin-startup-class"></a>Etapa 1.4: Adicionar uma classe de inicialização da OWIN

Adicione uma classe de inicialização OWIN chamada `Startup.cs` à API. Para fazer isso:
1. Clique com o botão direito do mouse no projeto, selecione **Adicionar** > **Novo Item** e pesquise por **OWIN**.

   ![Criando a classe de inicialização OWIN na caixa de diálogo Adicionar Novo Item no Visual Studio](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Abra o arquivo *Startup.cs* e substitua o conteúdo do arquivo pelo código a seguir:

    ```csharp
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

### <a name="step-15-protect-the-identity-api-class"></a>Etapa 1.5: Proteger a classe de API de Identidade

Abra Controllers\IdentityController.cs e adicione a marca `[Authorize]` à classe do controlador. Essa marca restringe o acesso ao controlador para usuários que atendem os requisitos de autorização.

![Adicionar a marca Autorizar ao controlador](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Etapa 2: Publicar no Azure

Para publicar seu projeto, no Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Contoso.AADB2C.API** e selecione **Publicar**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Etapa 3: Adicionar a ID e o segredo do aplicativo dos serviços RESTful ao Azure AD B2C

Depois que o serviço RESTful estiver protegido pela ID (nome de usuário) e segredo do cliente, você precisará armazenar as credenciais no seu locatário do Azure AD B2C. Sua política personalizada fornece as credenciais ao invocar seus serviços RESTful.

### <a name="step-31-add-a-restful-services-client-id"></a>Etapa 3.1: Adicionar uma ID de cliente dos serviços RESTful

1. No seu locatário do Azure AD B2C, selecione **Configurações de B2C** > **Estrutura de Experiência de Identidade**.


2. Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.

3. Selecione **Adicionar**.

4. Em **Opções** selecione **Manual**.

5. Para **Nome**, digite **B2cRestClientId**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.

6. Na caixa **Segredo**, digite a ID do aplicativo definida anteriormente.

7. Para **Uso de chave**, selecione **Assinatura**.

8. Selecione **Criar**.

9. Confirme que você criou a chave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-a-restful-services-client-secret"></a>Etapa 3.2: Adicionar o segredo do cliente aos serviços RESTful

1. No seu locatário do Azure AD B2C, selecione **Configurações de B2C** > **Estrutura de Experiência de Identidade**.

2. Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.

3. Selecione **Adicionar**.

4. Em **Opções** selecione **Manual**.

5. Para **Nome**, digite **B2cRestClientSecret**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.

6. Na caixa **Segredo**, digite o segredo do aplicativo definido anteriormente.

7. Para **Uso de chave**, selecione **Assinatura**.

8. Selecione **Criar**.

9. Confirme que você criou a chave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Etapa 4: Alterar o perfil técnico para dar suporte à autenticação básica em sua política de extensão

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).

2. Pesquise o nó `<TechnicalProfile>` que inclui `Id="REST-API-SignUp"`.

3. Localize o elemento `<Metadata>`.

4. Altere o *AuthenticationType* para *Basic* da seguinte maneira:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Adicione o seguinte snippet de código XML logo após fechar o elemento `<Metadata>`:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Depois de adicionar o snippet de código, seu perfil técnico será semelhante ao seguinte código XML:

    ![Adicionar elementos XML de autenticação básica ao TechnicalProfile](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Etapa 5: Carregar a política ao seu locatário

1. No [Portal do Azure](https://portal.azure.com), alterne para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra **Azure AD B2C**.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Abra **Todas as Políticas**.

4. Selecione **Carregar Política**.

5. Marque a caixa de seleção **Substituir a política caso ela exista**.

6. Carregue o arquivo *TrustFrameworkExtensions.xml* e verifique se a validação dele é aprovada.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Etapa 6: Testar a política personalizada usando a opção Executar Agora

1. Abra **Configurações do Azure AD B2C** e selecione **Estrutura de Experiência de Identidade**.

    >[!NOTE]
    >Executar Agora exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) carregada e selecione **Executar agora**.

3. Teste o processo digitando **Teste** na caixa **Nome**.
    O Azure AD B2C exibirá uma mensagem de erro na parte superior da janela.

    ![Testando a validação de entrada de determinado nome em sua API de identidade](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Na caixa **Nome**, digite um nome (diferente de "Teste").
    O Azure AD B2C inscreve o usuário e envia um número de fidelidade para o aplicativo. Observe o número neste exemplo:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Baixar os arquivos e o código da política completos

* Depois de concluir o passo a passo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), recomendamos que você crie o cenário usando seus próprios arquivos de política personalizados. Fornecemos os [Arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic) como referência.
* É possível baixar o código completo em [Solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Próximas etapas

* [Usar certificados de cliente para proteger a API RESTful](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
