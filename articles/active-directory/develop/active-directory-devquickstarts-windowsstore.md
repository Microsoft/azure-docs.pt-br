---
title: "Introdução à Windows Store no Azure AD | Microsoft Docs"
description: Crie aplicativos da Windows Store que se integrem ao Azure AD para entrar e chame APIs protegidas do Azure AD usando OAuth.
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6b5189dc06d7f8b0ed4426944948b904feba847e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-windows-store-apps"></a>Integrar o Azure AD com aplicativos da Windows Store
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Não há suporte para projetos da Windows Store 8.1 e de versões anteriores no Visual Studio 2017.  Para obter mais informações, consulte [Direcionamento e compatibilidade da plataforma Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

Se você estiver desenvolvendo aplicativos para a Windows Store, o Azure AD (Azure Active Directory) tornará simples e direto autenticar os usuários com suas contas do Active Directory. Ao integrar-se com o Azure AD, um aplicativo pode consumir com segurança qualquer API Web protegida pelo Azure AD, por exemplo as APIs do Office 365 ou a API do Azure.

Para aplicativos de área de trabalho da Windows Store que precisam acessar recursos protegidos, o Azure AD fornece a ADAL (biblioteca de autenticação do Active Directory). A única finalidade da ADAL é tornar mais fácil para o aplicativo obter tokens de acesso. Para demonstrar como isso é fácil, este artigo mostra como criar um aplicativo da Windows Store DirectorySearcher que:

* Obtém tokens de acesso para chamar a API do Graph do Azure AD usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Pesquisa por usuários com um determinado nome UPN em um diretório.
* Desconecta usuários.

## <a name="before-you-get-started"></a>Antes de começar
* Baixe um [projeto de esqueleto](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) ou baixe a [amostra concluída](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Cada download é uma solução do Visual Studio 2015.
* Você também precisará de um locatário do Azure AD no qual criar usuários e registrar o aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

Quando estiver pronto, siga os procedimentos nas próximas três seções.

## <a name="step-1-register-the-directorysearcher-app"></a>Etapa 1: registrar o aplicativo DirectorySearcher
Para habilitar o aplicativo para obter tokens, primeiro será necessário registrá-lo no seu locatário do Azure AD e conceder permissão para acessar a API do Graph do Azure AD. Faça assim:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Em seguida, na lista **Diretório**, selecione o locatário do Active Directory em que você deseja registrar o aplicativo.
3. Clique em **Mais Serviços** no painel esquerdo e selecione **Azure Active Directory**.
4. Clique em **Registros do aplicativo** e, em seguida, selecione **Adicionar**.
5. Siga os prompts para criar um **aplicativo cliente nativo**.
  * **Nome** descreve o aplicativo aos usuários.
  * O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o Azure AD usará para retornar respostas de tokens. Insira um valor de espaço reservado por enquanto (por exemplo, **http://DirectorySearcher**). Substituiremos este valor posteriormente.
6. Depois de você concluir o registro, o Azure AD atribui uma ID do aplicativo exclusiva ao aplicativo. Copie o valor na guia **Aplicativo**, pois você precisará dele mais tarde.
7. Na página **Configurações**, selecione **Permissões necessárias** e escolha **Adicionar**.
8. Para o aplicativo do **Azure Active Directory**, selecione **Microsoft Graph** como a API.
9. Em **Permissões Delegadas**, adicione a permissão **Acessar o diretório como o usuário conectado**. Fazer isso permite que o aplicativo consulte a API do Graph para usuários.

## <a name="step-2-install-and-configure-adal"></a>Etapa 2: instalar e configurar a ADAL
Agora que você tem um aplicativo no Azure AD, você pode instalar a ADAL e escrever seu código relacionado à identidade. Para permitir que a ADAL se comunique com o Azure AD, dê a ela algumas informações sobre o registro do aplicativo.

1. Adicione a ADAL ao projeto DirectorySearcher usando o Console do Gerenciador de Pacotes.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. No projeto DirectorySearcher, abra MainPage.xaml.cs.
3. Substitua os valores na região **Config Values** pelos valores que você inseriu no Portal do Azure. Seu código fará referência a esses valores sempre que ele usar a ADAL.
  * O *tenant* é o domínio do seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com.
  * A *clientId* é a ID do cliente do aplicativo, que você copiou do portal.
4. Agora você precisa descobrir o URI de retorno de chamada para seu aplicativo da Windows Store. Defina um ponto de interrupção nessa linha no método `MainPage` :
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. Compile a solução, certificando-se de que todas as referências do pacote sejam restauradas. Se houver pacotes ausentes, abra o Gerenciador de Pacotes NuGet e restaure-os.
6. Execute o aplicativo e copie separadamente o valor de `redirectUri` quando o ponto de interrupção for atingido. O valor deve ter uma aparência semelhante à seguinte:

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. De volta à guia **Configurações** do seu aplicativo no Portal do Azure, adicione um **RedirectUri** com o valor precedente.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Etapa 3: usar a ADAL para obter tokens do Azure AD
O princípio básico da ADAL é que sempre que o aplicativo precisa de um token de acesso, ele simplesmente chama `authContext.AcquireToken(…)` e a ADAL faz o resto.  

1. Inicialize o aplicativo `AuthenticationContext`, que é a classe primária da ADAL. Essa ação passa à ADAL as coordenadas necessárias para se comunicar com o Azure AD e informar a ele como armazenar tokens em cache.

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Localize o método `Search(...)`, que é invocado quando os usuários clicam no botão **Pesquisa** na interface do usuário do aplicativo. Esse método faz uma solicitação get para que a API do Graph do Azure AD consulte usuários cujo UPN comece com o termo de pesquisa fornecido. Para consultar a API do Graph, inclua um token de acesso no cabeçalho **Authorization** da solicitação. É aí que a ADAL entra em cena.

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    Quando o aplicativo solicita um token chamando `AcquireTokenAsync(...)`, a ADAL tenta retornar um token sem pedir as credenciais ao usuário. Se a ADAL determinar que o usuário precisa entrar para obter um token, ela exibirá uma caixa de diálogo de entrada, coletará as credenciais do usuário e retornar um token após uma autenticação bem-sucedida. Se a ADAL não puder retornar um token por qualquer motivo, o status *AuthenticationResult* será um erro.
3. Agora é hora de usar o token de acesso que você acabou de adquirir. Além disso, no método `Search(...)`, anexe o token para a solicitação get de API do Graph no cabeçalho **Authorization**:

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. Você pode usar o objeto `AuthenticationResult` para exibir informações sobre o usuário no aplicativo, por exemplo a ID do usuário:

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. Você também pode usar a ADAL para fazer com que os usuários saiam do aplicativo. Quando o usuário clica no botão **Sair**, certifique-se de que a próxima chamada para `AcquireTokenAsync(...)` mostre um modo de exibição de entrada. Com a ADAL, essa ação é tão fácil quanto limpar o cache de tokens:

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>O que vem a seguir
Agora você tem um aplicativo da Windows Store que pode autenticar usuários, chamar APIs Web com segurança usando OAuth 2.0 e obter informações básicas sobre o usuário.

Se você ainda não fez isso, agora é o momento de popular seu locatário com alguns usuários.
1. Execute o aplicativo DirectorySearcher e então entre com um dos usuários.
2. Procure por outros usuários com base em seus UPNs.
3. Feche o aplicativo e execute-o novamente. Observe como a sessão do usuário permanece intacta.
4. Saia clicando com o botão direito do mouse para mostrar a barra inferior e entre novamente como outro usuário.

A ADAL facilita a incorporar todos esses recursos de identidade comuns no aplicativo. Ele se encarrega de todo o trabalho difícil para você, por exemplo, gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma interface do usuário de logon ao usuário e atualização de tokens expirados. Você precisa conhecer apenas uma única chamada à API, `authContext.AcquireToken*(…)`.

Para referência, baixe a [amostra concluída](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (sem seus valores de configuração).

Agora você pode passar para cenários de identidade adicionais. Por exemplo, experimente [Proteger uma API Web .NET com o Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
