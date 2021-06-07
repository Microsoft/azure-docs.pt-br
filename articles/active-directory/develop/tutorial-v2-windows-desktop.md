---
title: 'Tutorial: Criar um aplicativo WPF (Windows Presentation Foundation) que usa a plataforma de identidade da Microsoft para autenticação | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você criará um aplicativo WPF que usa a plataforma de identidade da Microsoft para conectar usuários e receberá um token de acesso para chamar a API do Microsoft Graph em nome deles.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 60a29efc4d2daa9d1bc90f00e71094da382a83b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686879"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Tutorial: Chamar a API do Microsoft Graph em um aplicativo da Área de Trabalho do Windows

Neste tutorial, você criará um aplicativo nativo .NET da Área de Trabalho do Windows (XAML) que conecta usuários e obtém um token de acesso para chamar a API do Microsoft Graph. 

Depois de concluir o guia, seu aplicativo poderá chamar uma API protegida que usa contas pessoais (incluindo o outlook.com, live.com e outros). O aplicativo também usará contas corporativas e de estudante de qualquer empresa ou organização que usa o Azure Active Directory.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto do *WPF (Windows Presentation Foundation)* no Visual Studio
> * Instalar a MSAL (Biblioteca de Autenticação da Microsoft) para .NET
> * Registrar o aplicativo no portal do Azure
> * Adicionar código para entrada e saída do usuário
> * Adicionar código para chamar a API do Microsoft Graph
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

O aplicativo de exemplo que você cria com este guia habilita um aplicativo da Área de Trabalho do Windows que consulta a API do Microsoft Graph ou uma API Web que aceita tokens de um ponto de extremidade da plataforma de identidade da Microsoft. Para esse cenário, você adiciona um token às solicitações HTTP por meio do cabeçalho de Autorização. A MSAL (Biblioteca de Autenticação da Microsoft) lida com a aquisição e a renovação de tokens.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Manipulando a aquisição de token para acessar APIs Web protegidas

Após a autenticação do usuário, o aplicativo de exemplo receberá um token que pode ser usado para consultar a API do Microsoft Graph ou uma API Web protegida pela plataforma de identidade da Microsoft.

APIs, como o Microsoft Graph, exigem um token para permitir o acesso a recursos específicos. Por exemplo, um token é necessário para ler o perfil ou acessar o calendário de um usuário, ou enviar um email. O aplicativo pode solicitar um token de acesso usando a MSAL para acessar esses recursos especificando escopos de API. Esse token de acesso é então adicionado ao cabeçalho de Autorização HTTP de cada chamada feita no recurso protegido.

A MSAL gerencia o armazenamento em cache e a atualização de tokens de acesso para você, de forma que o aplicativo não precise fazer isso.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft (MSAL.NET)|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Nesta seção, você cria um projeto para demonstrar como integrar um aplicativo .NET da Área de Trabalho do Windows (XAML) com a opção *Entrar com uma Conta da Microsoft*, de maneira que o aplicativo possa consultar APIs Web que exigem um token.

O aplicativo que você criar com este guia exibe um botão que é usado para chamar um gráfico, uma área para mostrar os resultados na tela e um botão de saída.

> [!NOTE]
> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip) e vá para a [Etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.
>

Para criar seu aplicativo, faça o seguinte:

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.
2. Em **Modelos**, selecione **Visual C#** .
3. Selecione **aplicativo WPF (.NET Framework)** , dependendo da versão do Visual Studio que você estiver usando.

## <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet**> **Console do Gerenciador de Pacotes**.
2. Na janela do Console do Gerenciador de Pacotes, cole o seguinte comando do Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Esse comando instala a Biblioteca de Autenticação da Microsoft. A MSAL lida com a aquisição, o armazenamento em cache e a atualização de tokens de usuário usados para acessar APIs protegidas pelo Azure Active Directory v2.0
    >

## <a name="register-your-application"></a>Registre seu aplicativo

Você pode registrar seu aplicativo de duas maneiras.

### <a name="option-1-express-mode"></a>Opção 1: Modo Expresso

Registre rapidamente seu aplicativo fazendo o seguinte:
1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
1. Insira um nome para seu aplicativo e selecione **Registrar**.
1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo Avançado

Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:
1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo, por exemplo, `Win-App-calling-MsGraph`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Na seção **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas Microsoft pessoais (por exemplo, Skype, Xbox)** .
1. Selecione **Registrar**.
1. Em **Gerenciar**, escolha **Autenticação** > **Adicionar uma plataforma**.
1. Selecione **Aplicativos móveis e da área de trabalho**.
1. Na seção **URIs de Redirecionamento**, selecione **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Selecione **Configurar**.
1. Vá para o Visual Studio, abra o arquivo *App.xaml.cs* e, depois, substitua `Enter_the_Application_Id_here` no snippet de código abaixo pela ID do aplicativo que você acabou de registrar e copiar.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Adicionar o código para inicializar a MSAL

Nesta etapa, você cria uma classe para lidar com a interação com a MSAL, por exemplo, com a manipulação de tokens.

1. Abra o arquivo *App.xaml.cs* e adicione a referência para a MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Atualize a classe app para o seguinte:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .WithDefaultRedirectUri()
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Criar a interface do usuário do aplicativo

Esta seção mostra como um aplicativo pode consultar um servidor de back-end protegido como o Microsoft Graph.

Um arquivo *MainWindow.xaml* deve ser criado automaticamente como parte de seu modelo de projeto. Abra esse arquivo e substitua o nó *\<Grid>* do aplicativo pelo seguinte código:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usar a MSAL para obter um token para a API do Microsoft Graph

Nesta seção, você usará a MSAL para obter um token da API do Microsoft Graph.

1. No arquivo *MainWindow.xaml.cs*, adicione a referência da MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Substitua o código de classe `MainWindow` pelo seguinte:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>Mais informações

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

A chamada ao método `AcquireTokenInteractive` resulta em uma janela que solicita a conexão do usuário. Os aplicativos geralmente exigem que os usuários façam logon interativamente na primeira vez que precisarem acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar (por exemplo, quando a senha do usuário tiver expirado).

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `AcquireTokenSilent` manipula as aquisições e renovações de tokens sem nenhuma interação do usuário. Depois que `AcquireTokenInteractive` for executado pela primeira vez, `AcquireTokenSilent` será o método comum usado para obter tokens que acessam recursos protegidos nas próximas chamadas – já que as chamadas para solicitar ou renovar tokens são feitas no modo silencioso.

Por fim, o método `AcquireTokenSilent` falhará. Os motivos da falha podem ser que o usuário se desconectou ou alterou a senha em outro dispositivo. Quando a MSAL detecta que o problema pode ser resolvido com a solicitação de uma ação interativa, ela dispara uma exceção `MsalUiRequiredException`. O aplicativo pode tratar essa exceção de duas maneiras:

* Ele pode fazer uma chamada para `AcquireTokenInteractive` imediatamente. Essa chamada resulta na solicitação de entrada do usuário. Esse padrão geralmente é usado nos aplicativos online em que não há nenhum conteúdo offline disponível para o usuário. O exemplo gerado por essa configuração interativa segue esse padrão, que você pode ver em ação na primeira vez que executar o exemplo.

* Como nenhum usuário usou o aplicativo, `PublicClientApp.Users.FirstOrDefault()` conterá um valor nulo e uma exceção `MsalUiRequiredException` será lançada.

* Em seguida, o código no exemplo trata a exceção chamando `AcquireTokenInteractive`, o que resulta na solicitação de entrada do usuário.

* Ele também pode apresentar uma indicação visual aos usuários informando que uma entrada interativa é necessária, para que eles possam selecionar a hora certa de entrar. O aplicativo também pode tentar novamente `AcquireTokenSilent` mais tarde. Esse padrão é usado com frequência quando os usuários podem usar outras funcionalidades do aplicativo sem interrupções, por exemplo, quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas. Como alternativa, o aplicativo pode optar por repetir `AcquireTokenSilent` quando a rede é restaurada depois de estar temporariamente indisponível.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar a API do Microsoft Graph usando o token obtido recentemente

Adicione o seguinte método a `MainWindow.xaml.cs`. O método é usado para fazer uma solicitação `GET` na API do Graph usando um cabeçalho de autorização:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste exemplo de aplicativo, use o método `GetHttpContentWithToken` para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token e, em seguida, retornar o conteúdo para o chamador. Esse método adiciona o token adquirido no cabeçalho de Autorização HTTP. Para este exemplo, o recurso é o ponto de extremidade *me* da API do Microsoft Graph – que exibe as informações de perfil do usuário.

## <a name="add-a-method-to-sign-out-a-user"></a>Adicionar um método para desconectar um usuário

Para desconectar um usuário, adicione o seguinte método ao arquivo `MainWindow.xaml.cs`:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>Mais informações sobre como desconectar o usuário

O método `SignOutButton_Click` acima remove os usuários do cache de usuário da MSAL, o que diz para a MSAL esquecer o usuário atual, de modo que uma solicitação futura de aquisição de um token seja bem sucedida apenas se for feita para ser interativa.

Embora o aplicativo neste exemplo ofereça suporte a um único usuário, a MSAL oferece suporte a cenários em que várias contas podem estar conectadas ao mesmo tempo. Um exemplo é um aplicativo de email onde um usuário tem várias contas.

## <a name="display-basic-token-information"></a>Exibir informações básicas de token

Para exibir informações básicas sobre o token, adicione o seguinte método ao arquivo *MainWindow.xaml.cs*:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>Mais informações

Além do token de acesso que é usado para chamar a API do Microsoft Graph, após o usuário se autenticar, a MSAL também obterá um token de ID. Esse token contém um pequeno subconjunto de informações pertinentes aos usuários. O método `DisplayBasicTokenInfo` exibe as informações básicas contidas no token. Por exemplo, ele mostra o nome de exibição do usuário e a ID, bem como a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Pressione o botão *Chamar API do Microsoft Graph* várias vezes e veja que o mesmo token foi reutilizado em solicitações posteriores. Veja também a data de expiração que está sendo estendida quando a MSAL decide a hora de renovar o token.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar aplicativos de área de trabalho que chamam APIs Web protegidas em nossa série de cenários com várias partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo da área de trabalho que chama APIs Web](scenario-desktop-overview.md)
