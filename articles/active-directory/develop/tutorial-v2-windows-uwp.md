---
title: 'Tutorial: Criar um aplicativo UWP (Plataforma Universal do Windows) que usa a plataforma de identidade da Microsoft para autenticação | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você criará um aplicativo UWP que usa a plataforma de identidade da Microsoft para conectar usuários e receberá um token de acesso para chamar a API do Microsoft Graph em nome deles.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 6383f63d2118d8618f07bf3cb6cd08a0b16140f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102641"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Tutorial: Chamar a API do Microsoft Graph de um aplicativo da UWP (Plataforma Universal do Windows)

Neste tutorial, você criará um aplicativo UWP (Plataforma Universal do Windows) nativo que conecta usuários e obtém um token de acesso para chamar a API do Microsoft Graph. 

No final deste guia, seu aplicativo chama uma API protegida usando contas pessoais. Os exemplos são outlook.com, live.com e outros. Seu aplicativo também chama contas corporativas e de estudante de qualquer empresa ou organização que tem um Azure AD (Azure Active Directory).

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto *UWP (Plataforma Universal do Windows)* no Visual Studio
> * Registrar o aplicativo no portal do Azure
> * Adicionar código para entrada e saída do usuário
> * Adicionar código para chamar a API do Microsoft Graph
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho de [desenvolvimento da Plataforma Universal do Windows](/windows/uwp/get-started/get-set-up) instalada

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Este guia cria um aplicativo da UWP de exemplo que consulta a API do Microsoft Graph. Para esse cenário, um token é adicionado às solicitações HTTP usando o cabeçalho de Autorização. A Biblioteca de Autenticação da Microsoft lida com as aquisições e a renovações de tokens.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa o seguinte pacote NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)| Biblioteca de Autenticação da Microsoft|
|[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)|Biblioteca de Clientes do Microsoft Graph|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta seção fornece instruções passo a passo para integrar um aplicativo .NET da área de trabalho do Windows (XAML) ao Entrar com a Microsoft. Em seguida, o aplicativo pode consultar APIs Web que exigem um token, como a API do Microsoft Graph.

Este guia cria um aplicativo que exibe um botão que consulta a API do Microsoft Graph e um botão para sair. Ele também exibe caixas de texto que contêm os resultados das chamadas.

> [!Tip]
> Para ver uma versão completa do projeto que você criará neste tutorial, [baixe-o do GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip).

### <a name="create-your-application"></a>Criar o aplicativo

1. Abra o Visual Studio e selecione **Criar um projeto**.
1. Em **Criar um novo projeto**, escolha **Aplicativo em Branco (Universal do Windows)**  para C# e selecione **Avançar**.
1. Em **Configurar seu novo projeto**, nomeie o aplicativo e selecione **Criar**.
1. Se solicitado, em **Novo Projeto da Plataforma Universal do Windows**, selecione qualquer versão para **Destino** e **Mínima** e selecione **OK**.

   ![Versões mínima e de destino](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-the-microsoft-authentication-library-to-your-project"></a>Adicionar a Biblioteca de Autenticação da Microsoft ao projeto

1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
1. Copie e cole os seguintes comandos na janela **Console do Gerenciador de Pacotes**:

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > O primeiro comando instala a [MSAL.NET (Biblioteca de Autenticação da Microsoft)](https://aka.ms/msal-net). A MSAL.NET adquire, armazena em cache e atualiza os tokens de usuário que acessam APIs protegidas pela plataforma de identidade da Microsoft. O segundo comando instala a [Biblioteca de Clientes .NET do Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-dotnet) para autenticar solicitações para o Microsoft Graph e fazer chamadas para o serviço.

### <a name="create-your-applications-ui"></a>Criar a interface do usuário do aplicativo

O Visual Studio cria *MainPage.xaml* como parte de seu modelo de projeto. Abra esse arquivo e substitua o nó **Grade** do aplicativo pelo seguinte código:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-the-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Usar a Biblioteca de Autenticação da Microsoft para obter um token para a API do Microsoft Graph

Esta seção mostra como usar a Biblioteca de Autenticação da Microsoft para obter um token para a API do Microsoft Graph. Faça alterações no arquivo *MainPage.xaml.cs*.

1. Em *MainPage.xaml.cs*, adicione as seguintes referências:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Substitua a classe `MainPage` pelo seguinte código:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente<a name="more-information"></a>

O método `AcquireTokenInteractive` resulta em uma janela que solicita a conexão do usuário. Os aplicativos geralmente exigem que os usuários entrem interativamente na primeira vez para acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar. Por exemplo, quando a senha do usuário expirou.

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `AcquireTokenSilent` manipula as aquisições e renovações de tokens sem nenhuma interação do usuário. Após `AcquireTokenInteractive` ser executado pela primeira vez e solicitar credenciais ao usuário, use o método `AcquireTokenSilent` para solicitar tokens para chamadas posteriores. Esse método adquire os tokens silenciosamente. A Biblioteca de Autenticação da Microsoft lida com armazenamento em cache e renovação de tokens.

Por fim, o método `AcquireTokenSilent` falhará. A falha pode ser decorrente de os usuários terem se desconectado ou alterado a senha em outro dispositivo. Quando a Biblioteca de Autenticação da Microsoft detecta que o problema requer uma ação interativa, ela dispara uma exceção `MsalUiRequiredException`. O aplicativo pode tratar essa exceção de duas maneiras:

* Seu aplicativo chama `AcquireTokenInteractive` imediatamente. Essa chamada resulta na solicitação de entrada do usuário. Normalmente, use essa abordagem para aplicativos online em que não há nenhum conteúdo offline disponível para o usuário. O exemplo gerado por essa instalação orientada segue esse padrão. Você pode vê-lo em ação na primeira vez que executar o exemplo.

   Como nenhum usuário usou o aplicativo, `accounts.FirstOrDefault()` conterá um valor nulo e lançará uma exceção `MsalUiRequiredException`.

   Em seguida, o código no exemplo manipula a exceção chamando `AcquireTokenInteractive`. Essa chamada resulta na solicitação de entrada do usuário.

* Seu aplicativo apresenta uma indicação visual para os usuários de eles precisam se conectar. Em seguida, eles podem selecionar o momento certo para entrar. O aplicativo pode tentar `AcquireTokenSilent` novamente mais tarde. Use essa abordagem quando os usuários puderem usar outras funcionalidades do aplicativo sem interrupções. Um exemplo é quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam se conectar. O aplicativo pode tentar `AcquireTokenSilent` novamente após a rede ter ficado não disponível temporariamente.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>Instanciar o cliente do serviço Microsoft Graph obtendo o token do método SignInUserAndGetTokenUsingMSAL

Adicione o seguinte novo método a *MainPage.xaml.cs*:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste aplicativo de exemplo, o método `GetGraphServiceClient` instancia `GraphServiceClient` usando um token de acesso. Em seguida, `GraphServiceClient` é usado para obter as informações de perfil do usuário do ponto de extremidade **me**.

### <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

Para desconectar o usuário, adicione o seguinte método a *MainPage.xaml.cs*:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

MSAL.NET usa métodos assíncronos para adquirir tokens ou manipular contas. Como tal, dê suporte a ações da interface do usuário no thread da IU. Esse é o motivo para a chamada `Dispatcher.RunAsync` e as precauções para chamar `ConfigureAwait(false)`.

#### <a name="more-information-about-signing-out"></a>Mais informações sobre sair<a name="more-information-on-sign-out"></a>

O método `SignOutButton_Click` remove o usuário do cache de usuários da Biblioteca de Autenticação da Microsoft. Esse método instrui efetivamente a Biblioteca de Autenticação da Microsoft a esquecer o usuário atual. Uma solicitação futura para adquirir um token só terá êxito se for interativa.

O aplicativo neste exemplo oferece suporte a um único usuário. A Biblioteca de Autenticação da Microsoft dá suporte a cenários em que o usuário pode entrar por mais de uma conta. Um exemplo é um aplicativo de email em que um usuário tem diversas contas.

### <a name="display-basic-token-information"></a>Exibir informações básicas de token

Adicione o seguinte método a *MainPage.xaml.cs* para exibir informações básicas sobre o token:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>Mais informações<a name="more-information-1"></a>

Os tokens de ID adquiridos usando **OpenID Connect** também contêm um pequeno subconjunto de informações pertinentes ao usuário. `DisplayBasicTokenInfo` exibe informações básicas contidas no token. Essas informações incluem o nome de exibição e a ID do usuário. Elas também incluem a data de validade do token e a cadeia de caracteres que representa o token de acesso em si. Caso selecione o botão **Chamar API do Microsoft Graph** diversas vezes, você verá que o mesmo token foi reutilizado em solicitações posteriores. Veja também que a data de validade é estendida quando a Biblioteca de Autenticação da Microsoft decide que é a hora de renovar o token.

### <a name="display-message"></a>Exibir mensagem

Adicione o seguinte novo método a *MainPage.xaml.cs*:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Registre seu aplicativo

Agora, registre o seu aplicativo:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo, por exemplo, `UWP-App-calling-MSGraph`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Em **Tipos de conta com suporte**, escolha **Contas em qualquer diretório organizacional (Qualquer diretório do Azure Active Directory – Multilocatário) e contas Microsoft pessoais (por exemplo, Skype, Xbox)** . 
1. Selecione **Registrar**.
1. Na página de visão geral, localize o valor da **ID do Aplicativo (cliente)** e copie-o. Volte para o Visual Studio, abra *MainPage.xaml.cs* e substitua o valor de `ClientId` por esse valor.

Configurar a autenticação para o aplicativo:

1. De volta ao <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>, em **Gerenciar**, selecione **Autenticação** > **Adicionar uma plataforma** e escolha **Aplicativos móveis e da área de trabalho**.
1. Na seção **URIs de Redirecionamento**, insira `https://login.microsoftonline.com/common/oauth2/nativeclient`.
1. Selecione **Configurar**.

Configurar permissões da API para seu aplicativo:

1. Em **Gerenciar**, selecione **Permissões de API** > **Adicionar uma permissão**.
1. Selecione **Microsoft Graph**.
1. Selecione **Permissões delegadas**, pesquise por *User.Read* e verifique se **User.Read** está selecionado.
1. Se tiver feito alterações, selecione **Adicionar permissões** para salvá-las.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Habilitar a autenticação integrada em domínios federados (opcional)

Para habilitar a Autenticação Integrada do Windows quando ela é usada com um domínio federado do Azure AD, o manifesto do aplicativo precisará habilitar funcionalidades adicionais. Volte para seu aplicativo no Visual Studio.

1. Abra *Package.appxmanifest*.
1. Selecione **Funcionalidades** e habilite as seguintes configurações:

   * **Autenticação de Empresa**
   * **Redes Privadas (Cliente e Servidor)**
   * **Certificados Compartilhados de Usuário**

> [!IMPORTANT]
> A [Autenticação Integrada do Windows](https://aka.ms/msal-net-iwa) não está configurada por padrão nesse exemplo. Os aplicativos que solicitam funcionalidades de `Enterprise Authentication` ou `Shared User Certificates` exigem um nível mais alto de verificação pela Microsoft Store. Além disso, nem todos os desenvolvedores desejam executar o nível mais alto de verificação. Habilite essa configuração somente se você precisar da Autenticação Integrada do Windows com um domínio federado do Azure AD.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>Abordagem alternativa para usar WithDefaultRedirectURI()

No exemplo atual, o método `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` é usado. Para usar `WithDefaultRedirectURI()`, conclua estas etapas:

1. Em *MainPage.XAML.cs*, substitua `WithRedirectUri` por `WithDefaultRedirectUri`:

   **Código atual**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Código atualizado**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Localize o URI de retorno de chamada para seu aplicativo adicionando o campo `redirectURI` em *MainPage.xaml.cs* e configurando um ponto de interrupção nele:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Execute o aplicativo e copie separadamente o valor de `redirectUri` quando o ponto de interrupção for atingido. O valor deve ter uma aparência semelhante à do seguinte valor: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    É possível remover a linha de código posteriormente, porque ela é necessária apenas uma vez, para buscar o valor.

3. No portal de registro de aplicativo, adicione o valor retornado em **RedirectUri** no painel **Autenticação**.

## <a name="test-your-code"></a>Testar seu código

Para testar o aplicativo, selecione a tecla **F5** para executar o projeto no Visual Studio. A janela principal será exibida:

![Interface do usuário do aplicativo](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Quando estiver pronto para testar, selecione **Chamar a API do Microsoft Graph**. Em seguida, use uma conta organizacional do Azure AD ou uma conta Microsoft, como live.com ou outlook.com, para entrar. Na primeira vez que o usuário executa esse teste, o aplicativo exibe uma janela solicitando que ele entre.

### <a name="consent"></a>Consentimento

Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à da imagem a seguir. Selecione **Sim** para consentir explicitamente com o acesso:

![Tela de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Resultados esperados

São exibidas as informações de perfil do usuário retornadas pela chamada à API do Microsoft Graph na tela **Resultados da Chamada à API**:

![Tela Resultados da Chamada à API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Também serão exibidas informações básicas sobre o token adquirido por meio de `AcquireTokenInteractive` ou `AcquireTokenSilent` na caixa **Informações de Token**:

|Propriedade  |Formatar  |Descrição |
|---------|---------|---------|
|`Username` |`user@domain.com` |É o nome de usuário que identifica o usuário.|
|`Token Expires` |`DateTime` |A hora em que o token expira. A Biblioteca de Autenticação da Microsoft estende a data de validade renovando o token conforme necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo `user.read` para ler um perfil do usuário. Esse escopo é adicionado, por padrão, a cada aplicativo que é registrado no Portal de Registro de Aplicativo. Outras APIs do Microsoft Graph e APIs personalizadas do servidor de back-end podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo `Calendars.Read` para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada `Calendars.Read` às informações de registro de aplicativo. Em seguida, adicione o escopo `Calendars.Read` à chamada `acquireTokenSilent`.

Talvez os usuários precisem fornecer autorizações adicionais à medida que o número de escopos aumenta.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1

Você recebe uma das seguintes mensagens de erro ao entrar em seu aplicativo em um domínio federado do Azure AD:

* "Nenhum certificado do cliente válido encontrado na solicitação."
* "Nenhum certificado válido encontrado no repositório de certificados do usuário."
* "Tente novamente escolhendo um método de autenticação diferente."

**Causa:** Os recursos corporativos e de certificado não estão habilitados.

**Solução:** Siga as etapas em [Habilitar autenticação integrada em domínios federados (opcional)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Você habilitar a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tentar usar o Windows Hello em um computador com Windows 10 para entrar em um ambiente com a autenticação multifator configurada. A lista de certificados é exibida. Se você escolher usar seu PIN, a janela PIN nunca será exibida.

**Causa:** Esse problema é uma limitação conhecida do agente de autenticação da Web em aplicativos UWP executando em desktops com Windows 10. Ele funciona corretamente no Windows 10 Mobile.

**Solução alternativa:** Selecione **Entrar com outras opções**. Depois selecione **Entrar com nome de usuário e senha**. Selecione **Forneça sua senha**. Depois passe pelo processo de autenticação de telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar a MSAL (Biblioteca de Autenticação da Microsoft) para autorização e autenticação em aplicativos .NET:

> [!div class="nextstepaction"]
> [Visão geral da MSAL (Biblioteca de Autenticação da Microsoft)](msal-overview.md)
