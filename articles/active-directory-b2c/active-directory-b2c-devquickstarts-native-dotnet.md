---
title: Azure Active Directory B2C | Microsoft Docs
description: "Como compilar um aplicativo Web com gerenciamento de entrada, de inscrição e de perfis usando o Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e2b5c704230ee2ba1395dc76a1551aaa8e7af7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: criar um aplicativo da área de trabalho do Windows
Ao usar o Azure Active Directory B2C (Azure AD), você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seu aplicativo da área de trabalho em poucas etapas. Este artigo mostra como criar um aplicativo “lista de tarefas pendentes” do WPF (Windows Presentation Foundation) do .NET que inclui a inscrição, a entrada e o gerenciamento de perfil de usuário. O aplicativo inclui suporte para a inscrição e a entrada usando um nome de usuário ou um email. Ele também incluirá o suporte para a inscrição e a entrada usando contas sociais como o Facebook e o Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário.  Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar um aplicativo
Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md).  É necessário que você:

* Inclua um **cliente nativo** no aplicativo.
* Copie o **URI de redirecionamento** `urn:ietf:wg:oauth:2.0:oob`. É a URL padrão deste exemplo de código.
* Copie a **ID do Aplicativo** atribuída ao seu aplicativo. Você precisará dela mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas
No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: perfil de inscrição, entrada e edição. Você precisa criar uma política para cada tipo, como descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Ao criar as três políticas, não se esqueça de:

* Escolher a **Inscrição de ID de usuário** ou a **Inscrição de email** na folha de provedores de identidade.
* Escolher **Nome de exibição** e outros atributos de inscrição na política de inscrição.
* Escolher as declarações **Nome de exibição** e **ID de objeto** como declarações de aplicativo para cada política. Você pode escolher outras declarações também.
* Copie o **Nome** de cada política depois de criá-la. Ele deve ter o prefixo `b2c_1_`.  Você precisará esses nomes de política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar suas três políticas, você estará pronto para compilar o aplicativo.

## <a name="download-the-code"></a>Baixar o código
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para compilar o exemplo à medida que avançar, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

O aplicativo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. O projeto do `TaskClient` é o aplicativo de área de trabalho do WPF com o qual usuário interage. Para os fins deste tutorial, ele chama uma API Web de tarefa de back-end, hospedada no Azure, que armazena a lista de tarefas de cada usuário.  Você não precisa criar a API Web; ela já está em execução para você.

Para saber como a API Web autentica solicitações com segurança usando o Azure AD B2C, confira nosso [artigo de introdução à API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Executar políticas
Seu aplicativo se comunica com o Azure AD B2C enviando mensagens de autenticação que especificam a política que desejam executar como parte da solicitação HTTP. Para aplicativos da área de trabalho do .NET, você pode usar a visualização da MSAL (Biblioteca de Autenticação da Microsoft) para enviar mensagens de autenticação OAuth 2.0, executar políticas e obter tokens que chamam APIs Web.

### <a name="install-msal"></a>Instalar MSAL
Adicione MSAL ao projeto `TaskClient` usando o Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Insira seus detalhes B2C
Abra o arquivo `Globals.cs` e substitua cada um dos valores da propriedade pelos seus próprios. Essa classe é usada em todo o `TaskClient` para referenciar os valores comumente usados.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Criar o PublicClientApplication
A classe primária da MSAL é `PublicClientApplication`. Essa classe representa o aplicativo no sistema Azure AD B2C. Quando o aplicativo é inicializado, cria uma instância de `PublicClientApplication` em `MainWindow.xaml.cs`. Isso pode ser usado em toda a janela.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Iniciar um fluxo de inscrição
Quando um usuário opta por se inscrever, você deseja iniciar um fluxo de inscrição que use a política de inscrição criada. Usando a MSAL, você apenas chama `pca.AcquireTokenAsync(...)`. Os parâmetros que você passa para o `AcquireTokenAsync(...)` determinam qual token receber, a política usada na solicitação de autenticação e assim por diante.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Iniciar um fluxo de entrada
Você pode iniciar um fluxo de entrada da mesma maneira que inicia um fluxo de inscrição. Quando um usuário entrar, faça a mesma chamada a MSAL, dessa vez usando a política de entrada:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Iniciar um fluxo de edição de perfil
Novamente, você pode executar uma política de edição de perfil da mesma maneira:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Em todos esses casos, MSAL retornará um token em `AuthenticationResult` ou gerará uma exceção. Sempre que obtiver um token do MSA, você poderá usar o `AuthenticationResult.User` objeto para atualizar os dados do usuário no aplicativo, como a interface do usuário. A ADAL também armazena o token em cache para uso em outras partes do aplicativo.

### <a name="check-for-tokens-on-app-start"></a>Verificar se há tokens na inicialização do aplicativo
Você também pode usar a MSAL para controlar o estado de entrada do usuário.  Neste aplicativo, queremos que o usuário permaneça conectado mesmo depois que fechar o aplicativo e o abrir novamente.  De volta à substituição `OnInitialized`, use o método `AcquireTokenSilent` da MSAL para verificar se há tokens em cache:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Chamar a API de tarefa
Agora você já usou MSAL para executar políticas e para obter tokens.  Quando você quiser usar um desses tokens para chamar a API de tarefa, você pode usar novamente da MSAL `AcquireTokenSilent` método para verificar tokens armazenados em cache:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Quando a chamada para `AcquireTokenSilentAsync(...)` é bem-sucedida e um token é encontrado no cache, você pode adicionar o token ao cabeçalho `Authorization` da solicitação HTTP. A API Web da tarefa usará esse cabeçalho para autenticar a solicitação para ler a lista de tarefas pendentes do usuário:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Desconectar o usuário
Finalmente, você pode usar MSAL para encerrar a sessão do usuário com o aplicativo, quando o usuário seleciona **Sair**.  Ao se usar MSAL, isso é feito por meio da limpeza de todos os tokens no cache de tokens:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo
Por fim, compile e execute o exemplo.  Inscreva-se no aplicativo usando um endereço de email ou um nome de usuário. Saia e entre novamente como o mesmo usuário. Edite perfil do usuário. Saia e entre novamente como outro usuário.

## <a name="add-social-idps"></a>Adicionar IDPs sociais
Atualmente, o aplicativo só dá suporte à inscrição e à entrada do usuário com **contas locais**. Essas são as contas armazenadas em seu diretório do B2C que usam um nome de usuário e senha. Com o Azure AD B2C, você pode adicionar suporte a outros provedores de identidade (IDPs), sem alterar qualquer código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas nestes artigos. Para cada IDP ao qual deseja oferecer suporte, você precisa registrar um aplicativo no sistema e obter uma ID de cliente.

* [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar o LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Após a adição dos provedores de identidade ao seu diretório B2C, você precisará editar cada uma das suas três políticas para incluir os novos IDPs, como descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar as políticas, execute o aplicativo novamente. Você deve ver os novos IDPs adicionados como opções de entrada e de inscrição em cada experiência de identidade.

Você pode fazer experiências com suas políticas e observar os efeitos no aplicativo de exemplo. Adicione ou remova IDPs, manipule declarações de aplicativo ou altere os atributos de inscrição. Experimente até conseguir entender como as políticas, as solicitações de autenticação e MSAL funcionam juntos.

Para referência, o exemplo completo [é fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
