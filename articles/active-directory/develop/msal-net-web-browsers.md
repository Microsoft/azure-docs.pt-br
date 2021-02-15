---
title: Usar navegadores da Web (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as considerações específicas ao usar o Xamarin Android com a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 23ba50a6eca1e398b9d459153b84719909f2ecac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583732"
---
# <a name="using-web-browsers-msalnet"></a>Usar navegadores da Web (MSAL.NET)

É obrigatório o uso de navegadores da Web na autenticação interativa. Por padrão, a MSAL.NET é compatível com o [navegador da Web do sistema](#system-web-browser-on-xamarinios-xamarinandroid) no Xamarin.iOS e no Xamarin.Android. Mas [você também pode habilitar o navegador da Web incorporado](#enable-embedded-webviews-on-ios-and-android) dependendo de seus requisitos (UX, necessidade de SSO [logon único], segurança) em aplicativos do [Xamarin. iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e do [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid). E você pode até [escolher dinamicamente](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) qual navegador da Web usar com base na presença do Chrome ou em um navegador compatível com as guias personalizadas do Chrome no Android. A MSAL.NET só é compatível com o navegador do sistema em aplicativos de área de trabalho do .NET Core.

## <a name="web-browsers-in-msalnet"></a>Navegadores da Web na MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>A interação ocorre em um navegador da Web

É importante entender que, ao adquirir um token interativamente, o conteúdo da caixa de diálogo não é fornecido pela biblioteca, mas pelo STS (serviço de token de segurança). O ponto de extremidade de autenticação envia de volta um HTML e um JavaScript que controla a interação, que é renderizada em um navegador da Web ou controle da Web. Permitir que o STS manipule a interação HTML tem muitas vantagens:

- A senha (se tiver sido digitada) nunca é armazenada pelo aplicativo, nem pela biblioteca de autenticação.
- Permite redirecionamentos para outros provedores de identidade (por exemplo, logon com uma conta corporativa ou de estudante, uma conta pessoal com MSAL ou uma conta de rede social com o Azure AD B2C).
- Permite ao STS controlar o acesso condicional, por exemplo, obrigando o usuário a realizar a [MFA (autenticação multifator)](../authentication/concept-mfa-howitworks.md) durante a fase de autenticação (inserir um PIN do Windows Hello, receber uma chamada no celular ou um aplicativo de autenticação no celular). Nos casos em que a autenticação multifator necessária ainda não estiver configurada, o usuário poderá configurá-la just-in-time na mesma caixa de diálogo.  O usuário insere seu número de telefone celular e é orientado a instalar um aplicativo de autenticação e digitalizar uma marca QR para adicionar a conta. Essa interação baseada em servidor é uma ótima experiência!
- Permite que o usuário altere a senha nessa mesma caixa de diálogo quando ela tiver expirado (fornecendo campos adicionais para a senha antiga e a nova senha).
- Habilita a marca do locatário ou o aplicativo (imagens) controlado pelo proprietário do aplicativo/administrador de locatário do Azure AD.
- Habilita os usuários a permitir que o aplicativo acesse os recursos/escopos em nome deles logo após a autenticação.

### <a name="embedded-vs-system-web-ui"></a>Interface do usuário da Web do sistema x incorporada

A MSAL.NET é uma biblioteca de várias estruturas e tem código específico da estrutura para hospedar um navegador em um controle de interface do usuário (por exemplo, no .NET Classic, ela usa o WinForms, no Xamarin, ela usa controles móveis nativos etc.). Esse controle é chamado de interface do usuário da Web `embedded`. Como alternativa, a MSAL.NET também pode iniciar o navegador do sistema operacional.

Em geral, é recomendável usar o padrão de plataforma, que normalmente é o navegador do sistema. O navegador do sistema é melhor para lembrar os usuários que fizeram logon antes. Para alterar esse comportamento, use `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Visão rápida

| Framework        | Inserida | Sistema | Padrão |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Sim | Sim^ | Inserida |
| .NET Core     | Não | Sim^ | Sistema |
| .NET Standard | Não | Sim^ | Sistema |
| UWP | Sim | Não | Inserida |
| Xamarin.Android | Sim | Sim  | Sistema |
| Xamarin.iOS | Sim | Sim  | Sistema |
| Xamarin.Mac| Sim | Não | Inserida |

^ Exige o URI de redirecionamento "http://localhost"

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Navegador da Web do sistema no Xamarin.iOS, Xamarin.Android

Por padrão, a MSAL.NET é compatível com o navegador da Web do sistema no Xamarin.iOS, no Xamarin.Android e no .NET Core. Em todas as plataformas que fornecem a interface do usuário (ou seja, não o .NET Core), uma caixa de diálogo é fornecida pela biblioteca que incorpora um controle de navegador da Web. A MSAL.NET também usa uma exibição Web inserida para o .NET Desktop e o WAB para a plataforma UWP. No entanto, ela aproveita por padrão o **navegador da Web do sistema** para aplicativos Xamarin iOS e Xamarin Android. No iOS, ela escolhe até mesmo o modo de exibição da Web a ser usado dependendo da versão do sistema operacional (iOS12, iOS11 e anterior).

Usar o navegador do sistema tem uma vantagem significativa: compartilhar o estado do SSO com outros aplicativos e com aplicativos Web sem a necessidade de um agente (portal/autenticador da empresa). O navegador do sistema foi usado por padrão na MSAL.NET para as plataformas Xamarin iOS e Xamarin Android porque, nessas plataformas, o navegador da Web do sistema ocupa toda a tela e a experiência do usuário é melhor. O modo de exibição da Web do sistema não é distinguível de uma caixa de diálogo. No entanto, no iOS, o usuário pode ter que dar consentimento para o navegador chamar novamente o aplicativo, o que pode ser irritante.

## <a name="system-browser-experience-on-net-core"></a>Experiência do navegador do sistema no .NET Core

No .NET Core, a MSAL.NET iniciará o navegador do sistema como um processo separado. A MSAL.NET não tem controle sobre este navegador, mas quando o usuário conclui a autenticação, a página da Web é redirecionada de modo que a MSAL.NET possa interceptar o URI.

Você também pode configurar aplicativos escritos para o .NET Classic para usar este navegador especificando que

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

a MSAL.NET não pode detectar se o usuário sair ou simplesmente fechar o navegador. Os aplicativos que usam essa técnica são incentivados a definir um tempo limite (via `CancellationToken`). É recomendável definir um tempo limite de pelo menos alguns minutos para levar em conta os casos em que o usuário é solicitado a alterar a senha ou executar a autenticação multifator.

### <a name="how-to-use-the-default-os-browser"></a>Como usar o navegador do sistema operacional padrão

A MSAL.NET precisa escutar em `http://localhost:port` e interceptar o código que o AAD envia quando o usuário conclui a autenticação (confira [Código de autorização](v2-oauth2-auth-code-flow.md) para saber mais detalhes)

Para habilitar o navegador do sistema:

1. Durante o registro do aplicativo, configure `http://localhost` como um URI de redirecionamento (não é compatível com o B2C no momento)
2. Ao construir o PublicClientApplication, especifique este URI de redirecionamento:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se você configurar `http://localhost`, internamente, a MSAL.NET encontrará uma porta aberta aleatória e a usará.

### <a name="linux-and-mac"></a>Linux e MAC

No Linux, a MSAL.NET abrirá o navegador de sistema operacional padrão usando a ferramenta xdg-open. Para solucionar problemas, execute a ferramenta em um terminal, por exemplo, `xdg-open "https://www.bing.com"`. No Mac, o navegador é aberto invocando `open <url>`.

### <a name="customizing-the-experience"></a>Personalizar a experiência

> [!NOTE]
> A personalização está disponível na MSAL.NET 4.1.0 ou posterior.

A MSAL.NET pode responder com uma mensagem HTTP quando um token é recebido ou em caso de erro. Você pode exibir uma mensagem HTML ou redirecionar para uma URL de sua escolha:

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

### <a name="opening-a-specific-browser-experimental"></a>Abrir um navegador específico (experimental)

Você pode personalizar a maneira como a MSAL.NET abre o navegador. Por exemplo, em vez de usar qualquer navegador que seja o padrão, você pode forçar a abertura de um navegador específico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>A UWP não usa o modo de exibição da Web do sistema

No entanto, em aplicativos da área de trabalho, iniciar o modo de exibição da Web do sistema ocasiona uma experiência ruim para o usuário, pois ele vê o navegador e pode já ter aberto outras guias. Após a autenticação, os usuários verão uma página solicitando que fechem esta janela. Se o usuário não prestar atenção, ele poderá fechar todo o processo (incluindo outras guias, que não estão relacionadas à autenticação). Aproveitar o navegador do sistema na área de trabalho também exigiria a abertura de portas locais e a escuta delas, o que pode requerer permissões avançadas para o aplicativo. Você, como desenvolvedor, usuário ou administrador, pode ficar relutante quanto a esse requisito.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Habilitar modos de exibição da Web incorporados no iOS e no Android

Também é possível habilitar modos de exibição da Web incorporados em aplicativos do Xamarin.iOS e do Xamarin.Android. A partir da versão prévia MSAL.NET 2.0.0, a biblioteca também é compatível com a opção de modo de exibição da Web **incorporado**. Na ADAL.NET, o modo de exibição da Web incorporado é a única opção compatível.

Como desenvolvedor que usa a MSAL.NET para o Xamarin, você pode optar por usar os modos de exibição da Web incorporados ou os navegadores do sistema. Essa é sua opção, dependendo da experiência do usuário e das questões de segurança que você deseja resolver.

No momento, a MSAL.NET ainda não é compatível com os agentes do Android e do iOS. Portanto, para fornecer SSO (logon único), o navegador do sistema ainda pode ser uma opção melhor. A compatibilidade dos agentes com o navegador da Web incorporado está na lista de pendências da MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Diferenças entre o modo de exibição da Web incorporado e o navegador do sistema
Há algumas diferenças visuais entre o modo de exibição da Web incorporado e o navegador do sistema na MSAL.NET.

**Credenciais interativas com a MSAL.NET usando o modo de exibição da Web incorporado:**

![incorporado](media/msal-net-web-browsers/embedded-webview.png)

**Credenciais interativas com MSAL.NET usando o navegador do sistema:**

![Navegador do sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opções do desenvolvedor

Como desenvolvedor que usa a MSAL.NET, você tem várias opções para exibir a caixa de diálogo interativa do STS:

- **Navegador do sistema.** O navegador do sistema é definido por padrão na biblioteca. Se estiver usando o Android, leia [Navegadores do sistema](msal-net-system-browser-android-considerations.md) para obter informações específicas sobre quais navegadores são compatíveis com a autenticação. Ao usar o navegador do sistema no Android, é recomendável que o dispositivo tenha um navegador compatível com as guias personalizadas do Chrome.  Caso contrário, a autenticação poderá falhar.
- **Modo de exibição da Web incorporado.** Para usar somente o modo de exibição da Web incorporado na MSAL.NET, o construtor dos parâmetros `AcquireTokenInteractively` contém um método `WithUseEmbeddedWebView()`.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Escolher entre um navegador da Web incorporado e um navegador do sistema no Xamarin.iOS

Em seu aplicativo iOS, no `AppDelegate.cs`, você pode inicializar o `ParentWindow` para `null`. Não é usado no iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Escolher entre um navegador da Web incorporado e um navegador do sistema no Xamarin.Android

Em seu aplicativo Android, no `MainActivity.cs`, você pode definir a atividade pai para que o resultado da autenticação volte a ela:

```csharp
 App.ParentWindow = this;
```

Em seguida, no `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detectar a presença de guias personalizadas no Xamarin.Android

Se você quiser usar o navegador da Web do sistema para habilitar o SSO com os aplicativos em execução no navegador, mas estiver preocupado com a experiência do usuário para dispositivos Android que não têm um navegador compatível com as guias personalizadas, terá a opção de decidir chamando o método `IsSystemWebViewAvailable()` no `IPublicClientApplication`. Esse método retornará `true` se o PackageManager detectar guias personalizadas e `false` se elas não forem detectadas no dispositivo.

Com base no valor retornado por esse método e em seus requisitos, você pode tomar uma decisão:

- Você pode retornar uma mensagem de erro personalizada para o usuário. Por exemplo:  "Instale o Chrome para continuar com a autenticação" -OU-
- É possível voltar à opção de modo de exibição da Web incorporado e iniciar a interface do usuário dessa maneira.

O código a seguir mostra a opção de modo de exibição da Web incorporado:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>O .NET Core não é compatível com a autenticação interativa com um navegador incorporado

No .NET Core, a aquisição de tokens interativamente está disponível apenas por meio do navegador da Web do sistema, não com modos de exibição da Web incorporados. Na verdade, o .NET Core não fornece a interface do usuário ainda.
Se você quiser personalizar a experiência de navegação no navegador da Web do sistema, poderá implementar a interface [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e até mesmo fornecer seu próprio navegador.
