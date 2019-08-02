---
title: Navegadores da Web na biblioteca de autenticação da Microsoft para .NET | Azure
description: Saiba mais sobre considerações específicas ao usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb04a30719f7603610b323a4bb271666371ba97
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276862"
---
# <a name="using-web-browsers-in-msalnet"></a>Usando navegadores da Web no MSAL.NET
Os navegadores da Web são necessários para a autenticação interativa. Por padrão, o MSAL.NET dá suporte ao [navegador da Web do sistema](#system-web-browser-on-xamarinios-xamarinandroid) em Xamarin. Ios e Xamarin. Android. Mas [você também pode habilitar o navegador da Web incorporado](#enable-embedded-webviews-on-ios-and-android) dependendo de seus requisitos (UX, necessidade de logon único (SSO), segurança) em aplicativos [xamarin. Ios](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [xamarin. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) . E você pode até mesmo [escolher dinamicamente](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) qual navegador da Web usar com base na presença do Chrome ou em um navegador com suporte a guias personalizadas do Chrome no Android. O MSAL.NET só dá suporte ao navegador do sistema em aplicativos de área de trabalho do .NET Core.

## <a name="web-browsers-in-msalnet"></a>Navegadores da Web no MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>A interação ocorre em um navegador da Web

É importante entender que, ao adquirir um token interativamente, o conteúdo da caixa de diálogo não é fornecido pela biblioteca, mas pelo STS (serviço de token de segurança). O ponto de extremidade de autenticação envia de volta um HTML e JavaScript que controla a interação, que é renderizada em um navegador da Web ou controle da Web. Permitir que o STS manipule a interação HTML tem muitas vantagens:

- A senha (se tiver sido digitada) nunca é armazenada pelo aplicativo, nem pela biblioteca de autenticação.
- Permite redirecionamentos para outros provedores de identidade (por exemplo, logon com uma conta de estudante corporativa ou uma conta pessoal com MSAL ou com uma conta social com Azure AD B2C).
- Permite que o STS controle o acesso condicional, por exemplo, fazendo com que o usuário faça a autenticação de vários fatores (MFA) durante a fase de autenticação (inserindo um PIN do Windows Hello ou seja chamado em seu telefone ou em um aplicativo de autenticação em seu telefone). Nos casos em que a autenticação multifator necessária não estiver configurada ainda, o usuário poderá configurá-lo just in time na mesma caixa de diálogo.  O usuário insere seu número de telefone celular e é guiado para instalar um aplicativo de autenticação e digitalizar uma marca QR para adicionar sua conta. Essa interação baseada em servidor é uma ótima experiência!
- Permite que o usuário altere sua senha nessa mesma caixa de diálogo quando a senha tiver expirado (fornecendo campos adicionais para a senha antiga e a nova senha).
- Habilita a identidade visual do locatário ou o aplicativo (imagens) controlado pelo proprietário do aplicativo/administrador de locatário do Azure AD.
- Permite que os usuários consentim permitir que o aplicativo acesse os recursos/escopos em seu nome logo após a autenticação.

### <a name="embedded-vs-system-web-ui"></a>Interface do usuário da Web do vs integrado

MSAL.NET é uma biblioteca de várias estruturas e tem código específico da estrutura para hospedar um navegador em um controle de interface do usuário (por exemplo, no .net clássico ele usa WinForms, no Xamarin, ele usa controles móveis nativos, etc.). Esse controle é chamado `embedded` de interface do usuário da Web. Como alternativa, o MSAL.NET também é capaz de iniciar o navegador do sistema operacional.

Em geral, é recomendável que você use o padrão de plataforma, e isso normalmente é o navegador do sistema. O navegador do sistema é melhor para lembrar os usuários que fizeram logon antes. Se você precisar alterar esse comportamento, use`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Visão rápida

| Framework        | Caractere | Sistema | Padrão |
| ------------- |-------------| -----| ----- |
| Clássico do .NET     | Sim | Sim ^ | Caractere |
| .NET Core     | Não | Sim ^ | Sistema |
| .NET Standard | Não | Sim ^ | Sistema |
| UWP | Sim | Não | Caractere |
| Xamarin.Android | Sim | Sim  | Sistema |
| Xamarin.iOS | Sim | Sim  | Sistema |
| Xamarin.Mac| Sim | Não | Caractere |

^ Requer URI http://localhost de redirecionamento ""

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Navegador da Web do sistema no Xamarin. iOS, Xamarin. Android

Por padrão, o MSAL.NET dá suporte ao navegador da Web do sistema no Xamarin. iOS, Xamarin. Android e no .NET Core. Para todas as plataformas que fornecem a interface do usuário (ou seja, não o .NET Core), uma caixa de diálogo é fornecida pela biblioteca que incorpora um controle de navegador da Web. O MSAL.NET também usa uma exibição da Web inserida para a área de trabalho do .NET e o WAB para a plataforma UWP. No entanto, ele aproveita por padrão o **navegador da Web do sistema** para aplicativos xamarin Ios e xamarin Android. No iOS, ele escolhe até mesmo a exibição da Web a ser usada dependendo da versão do sistema operacional (iOS12, iOS11 e anterior).

Usar o navegador do sistema tem a vantagem significativa de compartilhar o estado do SSO com outros aplicativos e com aplicativos Web sem a necessidade de um agente (Portal/autenticador da empresa). O navegador do sistema foi usado, por padrão, em MSAL.NET para as plataformas Xamarin iOS e Xamarin Android porque, nessas plataformas, o navegador da Web do sistema ocupa toda a tela e a experiência do usuário é melhor. A exibição da Web do sistema não é distinguíveis de uma caixa de diálogo. No iOS, no entanto, o usuário pode ter que dar consentimento para o navegador chamar de volta o aplicativo, o que pode ser irritante.

## <a name="system-browser-experience-on-net-core"></a>Experiência do navegador do sistema no .NET Core

No .NET Core, o MSAL.NET iniciará o navegador do sistema como um processo separado. O MSAL.NET não tem controle sobre este navegador, mas quando o usuário conclui a autenticação, a página da Web é redirecionada de forma que MSAL.NET possa interceptar o URI.

Você também pode configurar aplicativos escritos para .NET clássico para usar este navegador, especificando

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET não pode detectar se o usuário sai ou simplesmente fecha o navegador. Os aplicativos que usam essa técnica são incentivados a definir um `CancellationToken`tempo limite (via). Recomendamos um tempo limite de pelo menos alguns minutos para levar em conta os casos em que o usuário é solicitado a alterar a senha ou executar a autenticação multifator.

### <a name="how-to-use-the-default-os-browser"></a>Como usar o navegador do sistema operacional padrão

O MSAL.NET precisa escutar `http://localhost:port` e interceptar o código que o AAD envia quando o usuário conclui a autenticação (consulte o código de [autorização](v2-oauth2-auth-code-flow.md) para obter detalhes)

Para habilitar o navegador do sistema:

1. Durante o registro do aplicativo `http://localhost` , configure como um URI de redirecionamento (não suportado atualmente pelo B2C)
2. Ao construir seu PublicClientApplication, especifique este URI de redirecionamento:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se você configurar `http://localhost`, o MSAL.net internamente encontrará uma porta aberta aleatória e a usará.

### <a name="linux-and-mac"></a>Linux e MAC

No Linux, o MSAL.NET abrirá o navegador de sistema operacional padrão usando a ferramenta xdg-open. Para solucionar problemas, execute a ferramenta em um terminal, por exemplo,`xdg-open "https://www.bing.com"`  
No Mac, o navegador é aberto invocando-se`open <url>`

### <a name="customizing-the-experience"></a>Personalizando a experiência

> [!NOTE]
> A personalização está disponível no MSAL.NET 4.1.0 ou posterior.

MSAL.NET é capaz de responder com uma mensagem HTTP quando um token é recebido ou em caso de erro. Você pode exibir uma mensagem HTML ou redirecionar para uma URL de sua escolha:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Abrindo um navegador específico (experimental)

Você pode personalizar a maneira como o MSAL.NET abre o navegador. Por exemplo, em vez de usar qualquer navegador que seja o padrão, você pode forçar a abrir um navegador específico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>A UWP não usa o sistema WebView

No entanto, para aplicativos de área de trabalho, iniciar um sistema WebView leva a uma experiência de usuário média, pois o usuário vê o navegador, onde eles podem já ter outras guias abertas. E quando a autenticação tiver ocorrido, os usuários receberão uma página solicitando que fechem esta janela. Se o usuário não pagar atenção, ele poderá fechar todo o processo (incluindo outras guias, que não estão relacionadas à autenticação). Aproveitar o navegador do sistema na área de trabalho também exigiria a abertura de portas locais e a escuta delas, o que pode exigir permissões avançadas para o aplicativo. Você, como desenvolvedor, usuário ou administrador, pode ser relutado sobre esse requisito.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Habilitar webviews inseridos no iOS e no Android

Você também pode habilitar webviews incorporados em aplicativos Xamarin. iOS e Xamarin. Android. A partir do MSAL.NET 2.0.0-Preview, o MSAL.NET também dá  suporte ao uso da opção de WebView inserida. Para ADAL.NET, o WebView incorporado é a única opção com suporte.

Como desenvolvedor que usa o MSAL.NET para o Xamarin, você pode optar por usar as exibições ou os navegadores de sistema incorporados. Essa é sua opção, dependendo da experiência do usuário e das questões de segurança que você deseja direcionar.

Atualmente, o MSAL.NET ainda não dá suporte aos agentes Android e iOS. Portanto, se você precisar fornecer SSO (logon único), o navegador do sistema ainda poderá ser uma opção melhor. O suporte a agentes com o navegador da Web incorporado está na pendência de MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Diferenças entre o WebView e o navegador do sistema inseridos
Há algumas diferenças visuais entre o WebView e o navegador do sistema incorporados no MSAL.NET.

**Entrada interativa com MSAL.NET usando o WebView inserido:**

![caractere](media/msal-net-web-browsers/embedded-webview.png)

**Entrada interativa com MSAL.NET usando o navegador do sistema:**

![Navegador do sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opções do desenvolvedor

Como desenvolvedor usando o MSAL.NET, você tem várias opções para exibir a caixa de diálogo interativa do STS:

- **Navegador do sistema.** O navegador do sistema é definido por padrão na biblioteca do. Se estiver usando o Android, leia navegadores do [sistema](msal-net-system-browser-android-considerations.md) para obter informações específicas sobre quais navegadores têm suporte para autenticação. Ao usar o navegador do sistema no Android, recomendamos que o dispositivo tenha um navegador que dê suporte a guias personalizadas do Chrome.  Caso contrário, a autenticação poderá falhar.
- **WebView inserido.** Para usar apenas o WebView incorporado em MSAL.NET `AcquireTokenInteractively` , o construtor de `WithUseEmbeddedWebView()` parâmetros contém um método.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Escolhendo entre navegador da Web incorporado ou navegador do sistema no Xamarin. iOS

Em seu aplicativo IOS, no `AppDelegate.cs` , você pode inicializar `ParentWindow` o `null`para. Não é usado no iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Escolhendo entre navegador da Web incorporado ou navegador do sistema no Xamarin. Android

Em seu aplicativo Android, no `MainActivity.cs` , você pode definir a atividade pai para que o resultado da autenticação volte a ela:

```csharp
 App.ParentWindow = this;
```

Em seguida, `MainPage.xaml.cs`no:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detectando a presença de guias personalizadas no Xamarin. Android

Se você quiser usar o navegador da Web do sistema para habilitar o SSO com os aplicativos em execução no navegador, mas estiver preocupado com a experiência do usuário para dispositivos Android que não têm um navegador com suporte à guia personalizado, você tem a opção `IsSystemWebViewAvailable()` de decidir chamando o método em < c 2 > `IPublicClientApplication` . Esse método retornará `true` se o PackageManager detectar guias personalizadas e `false` se elas não forem detectadas no dispositivo.

Com base no valor retornado por esse método e seus requisitos, você pode tomar uma decisão:

- Você pode retornar uma mensagem de erro personalizada para o usuário. Por exemplo:  "Instale o Chrome para continuar com a autenticação"-ou-
- Você pode fazer fallback para a opção de WebView inserida e iniciar a interface do usuário como uma exibição da Web incorporada.

O código a seguir mostra a opção de WebView inserida:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>O .NET Core não dá suporte à autenticação interativa com um navegador incorporado

Para o .NET Core, a aquisição de tokens interativamente está disponível apenas por meio do navegador da Web do sistema, não com exibições da Web inseridas. Na verdade, o .NET Core não fornece a interface do usuário ainda.
Se você quiser personalizar a experiência de navegação com o navegador da Web do sistema, poderá implementar a interface [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e até mesmo fornecer seu próprio navegador.
