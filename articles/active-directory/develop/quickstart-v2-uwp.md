---
title: 'Início Rápido: Conectar usuários e chamar o Microsoft Graph em um aplicativo da Plataforma Universal do Windows | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, saiba como um aplicativo UWP (Plataforma Universal do Windows) pode obter um token de acesso e chamar uma API protegida pela plataforma de identidade da Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 88fd6260df0039e960ac87b4b83d43ea497a58b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658502"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Início Rápido: Chamar a API do Microsoft Graph de um aplicativo da UWP (Plataforma Universal do Windows)

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo UWP (Plataforma Universal do Windows) pode conectar usuários e obter um token de acesso para chamar a API do Microsoft Graph. 

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> [!div renderon="docs" class="sxs-lookup"]
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução, siga estas etapas:
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `UWP-App-calling-MsGraph`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Na seção **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .
> 1. Selecione **Registrar** para criar o aplicativo e registre a **ID do Aplicativo (cliente)** para uso em uma etapa posterior.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Selecione **Adicionar uma plataforma** > **Aplicativos móveis e de área de trabalho**.
> 1. Em **URIs de redirecionamento**, selecione `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 1. Selecione **Configurar**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Etapa 1: Configurar o aplicativo
> Para que o exemplo de código deste guia de início rápido funcione, adicione um **URI de Redirecionamento** igual a `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-uwp/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-visual-studio-project"></a>Etapa 2: Baixar o projeto do Visual Studio

> [!div renderon="docs"]
> [Baixar o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Execute o projeto usando o Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos seu projeto com os valores das propriedades do aplicativo e ele está pronto para ser executado.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Etapa 3: Configurar o projeto do Visual Studio
>
> 1. Extraia o arquivo .zip para uma pasta local próxima à raiz da unidade. Por exemplo, em **C:\Azure-Samples**.
> 1. Abra o projeto no Visual Studio. Instale a carga de trabalho de **desenvolvimento da Plataforma Universal do Windows** e componentes de SDK individuais, se solicitado.
> 1. Em *MainPage.Xaml.cs*, altere o valor da variável `ClientId` para a **ID do Aplicativo (Cliente)** do aplicativo registrado anteriormente.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    Você pode encontrar a **ID do Aplicativo (cliente)** no painel **Visão geral** no portal do Azure (**Azure Active Directory** > **Registros de aplicativo** >  *{seu registro de aplicativo}* ).
> 1. Crie e selecione um novo certificado de teste autoassinado para o pacote:
>     1. No **Gerenciador de Soluções**, clique duas vezes no arquivo *Package.appxmanifest*.
>     1. Selecione **Empacotamento** > **Escolher Certificado...**  > **Criar...** .
>     1. Digite uma senha e selecione **OK**.
>     1. Selecione **Selecionar do arquivo...** e, em seguida, selecione o arquivo *Native_UWP_V2_TemporaryKey.pfx* que você acabou de criar e selecione **OK**.
>     1. Feche o arquivo *Package.appxmanifest* (selecione **OK** se for solicitado a salvar o arquivo).
>     1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Native_UWP_V2** e selecione **Propriedades**.
>     1. Selecione **Assinatura** e selecione o .pfx criado no menu suspenso **Escolha um arquivo de chave de nome forte**.

#### <a name="step-4-run-the-application"></a>Etapa 4: Executar o aplicativo

Para executar o aplicativo de exemplo no computador local:

1. Na barra de ferramentas do Visual Studio, escolha a plataforma certa (provavelmente **x64** ou **x86**, não ARM). O dispositivo de destino deve ser alterado de *Dispositivo* para *Computador Local*.
1. Selecione **Depurar** > **Iniciar Sem Depuração**.
    
    Se for solicitado que você faça isso, primeiro será necessário habilitar **Modo de Desenvolvedor** e, em seguida, **Iniciar Sem Depurar** novamente para iniciar o aplicativo.

Quando a janela do aplicativo for exibida, você poderá selecionar o botão **Chamar a API do Microsoft Graph**, insira suas credenciais e dê consentimento às permissões solicitadas pelo aplicativo. Se tiver êxito, o aplicativo exibirá algumas informações de token e os dados obtidos da chamada à API do Microsoft Graph.

## <a name="how-the-sample-works"></a>Como o exemplo funciona

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca usada para conectar usuários e solicitar tokens de segurança. Os tokens de segurança são usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Você pode instalar a MSAL executando o comando abaixo no *Console do Gerenciador de Pacotes* do Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, a MSAL é inicializada usando o seguinte código:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

O valor de `ClientId` é a **ID do Aplicativo (cliente)** do aplicativo registrado no portal do Azure. Você pode encontrar esse valor na página **Visão Geral** do aplicativo no portal do Azure.

### <a name="requesting-tokens"></a>Solicitando tokens

A MSAL tem dois métodos para adquirir tokens em um aplicativo UWP: `AcquireTokenInteractive` e `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Algumas situações exigem forçar os usuários a interagir com a plataforma de identidade da Microsoft por meio de uma janela pop-up para validar suas credenciais ou para dar consentimento. Alguns exemplos incluem:

- A primeira vez que os usuários entram no aplicativo
- Quando os usuários precisam reinserir suas credenciais porque a senha expirou
- Quando seu aplicativo está solicitando acesso a um recurso com o qual o usuário precisa consentir
- Quando a autenticação de dois fatores é necessária

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

O parâmetro `scopes` contém os escopos que estão sendo solicitados, como `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para APIs da Web personalizadas.

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

Use o método `AcquireTokenSilent` para obter tokens para acessar recursos protegidos após o método inicial `AcquireTokenInteractive`. Não convém exigir que o usuário valide suas credenciais sempre que precisar acessar um recurso. Na maioria das vezes, você quer aquisições e renovação de tokens sem nenhuma interação do usuário

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` contém os escopos que estão sendo solicitados, por exemplo, `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as APIs Web personalizadas.
* `firstAccount` especifica a primeira conta de usuário no cache (a MSAL é compatível com vários usuários em um único aplicativo).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial de área de trabalho do Windows para ver um guia passo a passo completo sobre a criação de aplicativos e novos recursos, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [Tutorial UWP – Chamar a API do Graph](tutorial-v2-windows-uwp.md)
