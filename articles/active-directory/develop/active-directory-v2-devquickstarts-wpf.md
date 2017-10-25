---
title: Aplicativo nativo .NET do Azure Active Directory v2.0 | Microsoft Docs
description: "Como criar um aplicativo nativo .NET que conecte usuários com a conta pessoal da Microsoft e as contas corporativas ou de estudante."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7389f55ee6fef9548abb0ca4ac1bbd0399868d47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Adicionar credenciais a um aplicativo da Área de Trabalho do Windows
Com o ponto de extremidade v2.0, você pode adicionar autenticação rapidamente a seus aplicativos do área de trabalho com suporte a contas pessoais da Microsoft e contas corporativas ou de estudante.  Ele também permite que seu aplicativo se comunique de forma segura com uma API Web de back-end, bem como com o [Microsoft Graph](https://graph.microsoft.io) e algumas das [APIs Unificadas do Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory (AD) têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

Para [clientes nativos .NET que precisam executar um dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), o Azure AD fornece a Biblioteca de Autenticação do Microsoft Identity, ou MSAL.  Única finalidade da MSAL é tornar mais fácil a obtenção de tokens de acesso para seu aplicativo.  Para demonstrar como é fácil, vamos compilar aqui um aplicativo de Lista de Tarefas para .NET WPF que:

* Conecta o usuário e obtém tokens de acesso usando o [protocolo de autenticação OAuth 2.0](active-directory-v2-protocols.md).
* Chama com segurança um serviço da Web de Lista de Tarefas back-end, que também é protegido pelo OAuth 2.0.
* Desconecta o usuário.

## <a name="download-sample-code"></a>Baixar código de exemplo
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

O aplicativo concluído é fornecido também no final desse tutorial.

## <a name="register-an-app"></a>Registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Não se esqueça de:

* Copiar a **ID do Aplicativo** designada ao seu aplicativo, você precisará dela logo.
* Adicione a plataforma **Móvel** de seu aplicativo.

## <a name="install--configure-msal"></a>Instalar e Configurar o MSAL
Agora que você tem um aplicativo registrado na Microsoft, pode instalar o MSAL e gravar seu código relacionado à identidade.  Para que o MSAL possa comunicar o ponto de extremidade v2.0, forneça a ele algumas informações sobre o registro de seu aplicativo.

* Comece adicionando o MSAL ao projeto TodoListClient usando o Console do Gerenciador de Pacotes.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* No projeto TodoListClient, abra `app.config`.  Substitua os valores dos elementos na seção `<appSettings>` para refletir os valores inseridos no portal de registro do aplicativo.  Seu código fará referência a esses valores sempre que ele usar a MSAL.
  
  * O `ida:ClientId` é a **ID do Aplicativo** do seu aplicativo que você copiou do portal.
* No projeto do Serviço de Lista de Tarefas, abra `web.config` na raiz do projeto.  
  
  * Substitua o `ida:Audience` valor com a mesma **ID do Aplicativo** no portal.

## <a name="use-msal-to-get-tokens"></a>Usar MSAL para obter tokens
O princípio básico da ADAL é que sempre que seu aplicativo precisar de um token de acesso, você simplesmente chamará `app.AcquireToken(...)`e a MSAL fará o resto.  

* No projeto `TodoListClient`, abra `MainWindow.xaml.cs` e localize o método `OnInitialized(...)`.  A primeira etapa é inicializar o `PublicClientApplication` de seu aplicativo - classe principal da MSAL representando aplicativos nativos.  É aqui que você passa à MSAL as coordenadas necessárias para se comunicar com o Azure AD e informar a ele como armazenar tokens em cache.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Quando o aplicativo é iniciado, queremos verificar e ver se o usuário já está conectado ao aplicativo.  No entanto, não queremos chamar uma entrada na interface do usuário ainda – vamos fazer o usuário clicar em "Entrar" para fazer isso.  Também no método `OnInitialized(...)` :

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Se o usuário não está conectado e clica no botão "Entrar", queremos chamar uma interface de usuário de logon para que o usuário insira suas credenciais.  Implemente o manipulador do botão Entrar:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Se o usuário entrar com êxito, a MSAL receberá e armazenará em cache um token para você, e você poderá prosseguir e chamar o método `GetTodoList()` com confiança.  Tudo o que resta para obter as tarefas do usuário é implementar o `GetTodoList()` método.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Executar
Parabéns! Agora você tem um aplicativo WPF .NET de trabalho que pode autenticar usuários e chamar com segurança APIs Web usando Oauth  Execute os dois projetos e entre com uma conta da Microsoft pessoal ou uma conta corporativa ou de estudante.  Adicione tarefas à lista Tarefas Pendentes daquele usuário.   Saia e entre novamente como outro usuário para ver a lista Tarefas Pendentes.  Feche o aplicativo e execute-o novamente.  Observe que a sessão do usuário permanece intacta, isso ocorre porque o aplicativo armazena em cache tokens em um arquivo local.

A MSAL facilita a incorporação de recursos de identidade comuns em seu aplicativo, usando tanto a conta corporativa quanto a pessoal.  Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de logon ao usuário, atualização de tokens expirados e mais.  Tudo o que você realmente precisa saber é uma única chamada à API, `app.AcquireTokenAsync(...)`.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Próximas etapas
Agora você pode ir para tópicos mais avançados.  Você pode desejar experimentar:

* [Proteção da API Web TodoListService com o ponto de extremidade v2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Para obter recursos adicionais, consulte:  

* [Guia do desenvolvedor do v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Marca "msal" de StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos
Recomendamos que você obtenha notificações sobre a ocorrência de incidentes de segurança visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.

