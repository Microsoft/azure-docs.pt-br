---
title: "Introdução à Área de Trabalho do Windows no Azure AD v2 – Uso | Microsoft Docs"
description: "Como os aplicativos .NET da Área de Trabalho do Windows (XAML) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usar a MSAL (Biblioteca de Autenticação da Microsoft) para obter um token para a API do Microsoft Graph

Esta seção mostra como usar a MSAL para obter um token da API do Microsoft Graph.

1.  Em `MainWindow.xaml.cs`, adicione a referência para a biblioteca MSAL à classe:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Substitua o código da classe <code>MainWindow</code> por:
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
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
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações
#### <a name="getting-a-user-token-interactive"></a>Obtendo um token de usuário interativo
A chamada ao método `AcquireTokenAsync` resulta em uma janela que solicita a conexão do usuário. Em geral, os aplicativos exigem que um usuário se conecte de forma interativa na primeira vez que precisam acessar um recurso protegido ou quando uma operação silenciosa para a aquisição de um token falha (por exemplo, a senha do usuário expirou).

#### <a name="getting-a-user-token-silently"></a>Obtendo um token de usuário no modo silencioso
`AcquireTokenSilentAsync` manipula as aquisições e a renovação de tokens sem nenhuma interação do usuário. Depois que `AcquireTokenAsync` é executado pela primeira vez, `AcquireTokenSilentAsync` é o método comum usado para obter tokens para acessar recursos protegidos nas próximas chamadas – já que as chamadas para solicitar ou renovar tokens são feitas no modo silencioso.
Em certas ocasiões, `AcquireTokenSilentAsync` falhará – por exemplo, o usuário saiu do serviço ou alterou sua senha em outro dispositivo. Quando a MSAL detectar que o problema pode ser resolvido com a solicitação de uma ação interativa, ela disparará uma `MsalUiRequiredException`. O aplicativo pode tratar essa exceção de duas maneiras:

1.  Fazer uma chamada a `AcquireTokenAsync` imediatamente, o que resultará na solicitação de conexão do usuário. Esse padrão geralmente é usado em aplicativos online em que não há nenhum conteúdo offline no aplicativo disponível para o usuário. A amostra gerada por esta instalação guiada usa esse padrão: você pode vê-lo em ação na primeira vez que executar a amostra: como nenhum usuário nunca usou o aplicativo, `PublicClientApp.Users.FirstOrDefault()` conterá um valor nulo e uma exceção `MsalUiRequiredException` será gerada. Em seguida, o código na amostra trata a exceção chamando `AcquireTokenAsync`, o que resulta na solicitação de conexão do usuário.

2.  Os aplicativos também podem fazer uma indicação visual para o usuário de que uma conexão interativa é necessária e, portanto, o usuário pode escolher o momento certo para se conectar ou o aplicativo pode tentar `AcquireTokenSilentAsync` novamente mais tarde. Normalmente, isso é usado quando o usuário consegue usar outras funcionalidades do aplicativo sem ser interrompido – por exemplo, há conteúdo offline disponível no aplicativo. Nesse caso, o usuário pode decidir quando deseja se conectar para acessar o recurso protegido ou atualizar as informações desatualizadas ou o aplicativo pode decidir tentar `AcquireTokenSilentAsync` novamente quando a rede é restaurada depois de ficar temporariamente indisponível.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chamar a API do Microsoft Graph usando o token obtido recentemente

1. Adicione o novo método abaixo ao `MainWindow.xaml.cs`. O método é usado para fazer uma solicitação `GET` na API do Graph usando um cabeçalho de autorização:

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
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste aplicativo de exemplo, o método `GetHttpContentWithToken` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token e, em seguida, retornar o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. Para esta amostra, o recurso é o ponto de extremidade *me* da API do Microsoft Graph – que exibe as informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

1. Adicione o seguinte método ao `MainWindow.xaml.cs` para desconectar o usuário:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
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
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Mais informações sobre a Saída

`SignOutButton_Click` remove o usuário do cache de usuário da MSAL – efetivamente, isso informará a MSAL para esquecer o usuário atual; portanto, uma solicitação futura de aquisição de um token terá êxito apenas se for feita para ser interativa.
Embora o aplicativo nesta amostra dê suporte a um único usuário, a MSAL dá suporte a cenários em que várias contas podem estar conectadas ao mesmo tempo – um exemplo é um aplicativo de email no qual um usuário tem várias contas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Exibir informações básicas de token

1. Adicione o seguinte método ao `MainWindow.xaml.cs` para exibir informações básicas sobre o token:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>Mais informações

Os tokens adquirido por meio do *OpenID Connect* também contêm um pequeno subconjunto de informações pertinentes ao usuário. `DisplayBasicTokenInfo` exibe informações básicas contidas no token: por exemplo, o nome de exibição do usuário e a ID, bem como a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Essas informações são exibidas para sua visualização. Pressione o botão *Chamar API do Microsoft Graph* várias vezes e veja que o mesmo token foi reutilizado em solicitações posteriores. Veja também a data de expiração que está sendo estendida quando a MSAL decide a hora de renovar o token.
<!--end-collapse-->

