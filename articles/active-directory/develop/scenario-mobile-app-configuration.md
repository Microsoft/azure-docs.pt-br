---
title: Aplicativo móvel que chama APIs Web (configuração de código) – plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo móvel que chama APIs da Web (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bdf9210eb88b2057cf861b208f19d3e6f562e9a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414844"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Aplicativo móvel que chama as APIs da Web-configuração de código

Depois de criar seu aplicativo, você aprenderá a configurar o código a partir dos parâmetros do aplicativo que você obteve no registro do aplicativo. Os aplicativos móveis também têm algumas especificidades complexas, que precisam se ajustar à estrutura usada para criar esses aplicativos

## <a name="msal-libraries-supporting-mobile-apps"></a>Bibliotecas de MSAL que dão suporte a aplicativos móveis

As bibliotecas da Microsoft que dão suporte a aplicativos móveis são:

  Biblioteca MSAL | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Para desenvolver aplicativos portáteis. As plataformas com suporte do MSAL.NET para criar um aplicativo móvel são UWP, Xamarin. iOS e Xamarin. Android.
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Para desenvolver aplicativos iOS nativos com Objective C ou Swift
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Para desenvolver aplicativos Android nativos em Java para Android

## <a name="configuring-the-application"></a>Configurando o aplicativo

Os aplicativos móveis usam a classe `PublicClientApplication` do MSAL. Veja como instanciá-lo:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

O parágrafo a seguir explica como configurar o código do aplicativo para aplicativos Xamarin. iOS, Xamarin. Android e UWP. A primeira etapa é instanciar o aplicativo. Uma etapa opcional é configurar o agente.

#### <a name="instantiating-the-application"></a>Instanciando o aplicativo

No Xamarin, ou UWP, a maneira mais simples de instanciar o aplicativo é a seguinte, em `ClientId` que o é o GUID do seu aplicativo, conforme registrado.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Há outros métodos de*parâmetro* que definem o pai da interface do usuário, substituem a autoridade padrão, especificam um nome de cliente e uma versão (para telemetria), especificam um URI de redirecionamento, especificam o alocador de http a ser usado (por exemplo, para lidar com proxies, especifique telemetria e registro em log). Este é o tópico dos parágrafos a seguir.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Especificando a interface do usuário/janela/atividade pai

No Android, você precisa passar a atividade pai antes de fazer a autenticação interativa. No iOS, ao usar um agente, você precisa passar o ViewController. Da mesma forma no UWP, talvez você queira passar a janela pai. Isso é possível quando você adquire o token, mas também é possível especificar um retorno de chamada no momento da criação do aplicativo um delegado que retorna o UIParent.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

No Android, recomendamos a `CurrentActivityPlugin` você [aqui](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Em seguida `PublicClientApplication` , o código do Construtor ficaria assim:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Mais parâmetros de compilação de aplicativo

- Para obter a lista de todos os modificadores `PublicClientApplicationBuilder`disponíveis em, consulte a documentação de referência [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Para obter a descrição de todas as opções expostas em `PublicClientApplicationOptions` consulte [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), na documentação de referência

#### <a name="xamarin-ios-specific-considerations"></a>Considerações específicas do Xamarin iOS

No Xamarin iOS, há várias considerações que você deve levar em conta ao usar o MSAL.NET:

1. [Substituir e implementar a `OpenUrl` função no`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Habilitar grupos de conjunto de chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Habilitar compartilhamento de cache de token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Habilitar o acesso ao conjunto de chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Os detalhes são fornecidos em [Considerações sobre o Xamarin Ios](msal-net-xamarin-ios-considerations.md)

#### <a name="other-xamarin-android-specific-considerations"></a>Outras considerações específicas do Xamarin Android

Aqui estão as especificações do Xamarin Android:

- [Garantindo que o controle volte para MSAL depois que a parte interativa do fluxo de autenticação for encerrada](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Atualizar o manifesto do Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Usar a exibição da Web inserida (opcional)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Solução de problemas](msal-net-xamarin-android-considerations.md#troubleshooting)

Os detalhes são fornecidos em [Considerações sobre o Xamarin Android](msal-net-xamarin-android-considerations.md)

Por fim, há algumas especificidades a saber sobre os navegadores no Android. Eles são explicados em [Considerações específicas do Xamarin Android com MSAL.net](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Considerações específicas de UWP

No UWP, você pode usar redes corporativas. Para obter informações sobre as especificações de UWP, consulte [Considerações específicas de plataforma universal do Windows com MSAL.net](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Configurando o aplicativo para usar o agente

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>Por que usar agentes em aplicativos Xamarin. iOS e Xamarin. Android?

No Android e no iOS, os agentes habilitam:

- SSO (logon único). Os usuários não precisarão entrar em cada aplicativo.
- Identificação do dispositivo. Habilita as políticas de acesso condicional relacionadas ao dispositivo do Azure AD, acessando o certificado do dispositivo que foi criado no dispositivo quando ele foi ingressado no local de trabalho.
- Verificação da identificação do aplicativo. Quando um aplicativo chama o agente, ele passa sua URL de redirecionamento e o agente o verifica.

### <a name="enable-the-brokers-on-xamarin"></a>Habilitar os agentes no Xamarin

Para habilitar um desses recursos, use o `WithBroker()` parâmetro ao chamar o `PublicClientApplicationBuilder.CreateApplication` método. `.WithBroker()`é definido como true por padrão. Siga as etapas abaixo para o [Ios](#brokered-authentication-for-xamarinios).

### <a name="brokered-authentication-for-xamarinios"></a>Autenticação orientada para Xamarin. iOS

Siga as etapas abaixo para habilitar seu aplicativo Xamarin. iOS para conversar com o aplicativo [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Etapa 1: Habilitar suporte do Broker

O suporte do Broker está habilitado por`PublicClientApplication` base. Isso está desabilitado por padrão. Você deve usar o `WithBroker()` parâmetro (definido como true por padrão) ao criar o `PublicClientApplication` através do `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Etapa 2: Atualizar AppDelegate para manipular o retorno de chamada

Quando o MSAL.NET chama o agente, o agente, por sua vez, retorna a chamada para o seu `AppDelegate.OpenUrl` aplicativo por meio do método. Como o MSAL aguardará a resposta do agente, seu aplicativo precisará cooperar para chamar MSAL.NET de volta. Você faz isso atualizando o `AppDelegate.cs` arquivo para substituir o método abaixo.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Esse método é invocado toda vez que o aplicativo é iniciado e é usado como uma oportunidade para processar a resposta do agente e concluir o processo de autenticação iniciado pelo MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Etapa 3: Definir um UIViewController ()

Com o Xamarin iOS, normalmente você não precisa definir uma janela de objeto, mas, nesse caso, é necessário enviar e receber respostas de um agente. Ainda em `AppDelegate.cs`, defina um ViewController.

Faça o seguinte para definir a janela de objeto:

1) No `AppDelegate.cs`, defina o `App.RootViewController` para um novo `UIViewController()`. Isso garantirá que haja um `UIViewController` com a chamada para o agente. Se não estiver definido corretamente, você poderá receber esse erro:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Na chamada AcquireTokenInteractive, use `.WithParentActivityOrWindow(App.RootViewController)` e passe a referência para a janela de objeto que você usará.

**Por exemplo:**

Em `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
Em `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Na chamada de aquisição de token:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>Etapa 4: Registrar um esquema de URL

O MSAL.NET usa URLs para invocar o agente e, em seguida, retornar a resposta do agente de volta para seu aplicativo. Para concluir a viagem de ida e volta, você precisa registrar um esquema de URL para seu `Info.plist` aplicativo no arquivo.

Prefixe `CFBundleURLSchemes` o `msauth`com. Em seguida `CFBundleURLName` , adicione ao final.

`$"msauth.(BundleId)"`

**Por exemplo:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Esse esquema de URL se tornará parte do RedirectUri usado para identificar exclusivamente seu aplicativo ao receber a resposta do agente.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>Etapa 5: LSApplicationQueriesSchemes

O MSAL `–canOpenURL:` usa para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou o que os esquemas que um aplicativo pode consultar.

**Adicionar** para a `LSApplicationQueriesSchemes` seção do`Info.plist` arquivo. **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticação orientada para Xamarin. Android

MSAL.NET ainda não dá suporte a agentes para Android.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirindo um token](scenario-mobile-acquire-token.md)
