---
title: Configurar aplicativos móveis que chamam APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o código do aplicativo móvel para chamar uma API da Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6f13d789cd63bb568bb8940ce614ebdb2dbcdb83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199741"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Configurar um aplicativo móvel que chama APIs da Web

Depois de criar seu aplicativo, você aprenderá a configurar o código usando os parâmetros de registro do aplicativo. Os aplicativos móveis apresentam algumas complexidades relacionadas ao ajuste da estrutura de criação.

## <a name="microsoft-libraries-supporting-mobile-apps"></a>Microsoft Libraries com suporte a aplicativos móveis

As seguintes bibliotecas da Microsoft dão suporte a aplicativos móveis:

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="instantiate-the-application"></a>Instanciar o aplicativo

### <a name="android"></a>Android

Os aplicativos móveis usam a `PublicClientApplication` classe. Veja como instanciá-lo:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Os aplicativos móveis no iOS precisam criar uma instância da `MSALPublicClientApplication` classe. Para criar uma instância da classe, use o código a seguir.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[As propriedades MSALPublicClientApplicationConfig adicionais](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) podem substituir a autoridade padrão, especificar um URI de redirecionamento ou alterar o comportamento do cache de token MSAL.

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

Esta seção explica como instanciar o aplicativo para aplicativos Xamarin. iOS, Xamarin. Android e UWP.

#### <a name="instantiate-the-application"></a>Instanciar o aplicativo

No Xamarin ou UWP, a maneira mais simples de instanciar o aplicativo é usando o código a seguir. Nesse código, `ClientId` é o GUID do seu aplicativo registrado.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

`With<Parameter>`Os métodos adicionais definem o pai da interface do usuário, substituem a autoridade padrão, especificam um nome e uma versão do cliente para telemetria, especificam um URI de redirecionamento e especificam a fábrica http a ser usada. A fábrica HTTP pode ser usada, por exemplo, para lidar com proxies e para especificar telemetria e registro em log.

As seções a seguir fornecem mais informações sobre como instanciar o aplicativo.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Especificar a interface do usuário pai, a janela ou a atividade

No Android, passe a atividade pai antes de fazer a autenticação interativa. No iOS, quando você usa um agente, o Pass-in `ViewController` . Da mesma forma no UWP, talvez você queira passar a janela pai. Você o passa quando adquire o token. Mas quando você estiver criando o aplicativo, também poderá especificar um retorno de chamada como um delegado que retorna `UIParent` .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

No Android, recomendamos que você use o [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) . O `PublicClientApplication` código do Construtor resultante é semelhante a este exemplo:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Encontrar mais parâmetros de criação de aplicativos

Para obter uma lista de todos os métodos disponíveis no `PublicClientApplicationBuilder` , consulte a [lista de métodos](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Para obter uma descrição de todas as opções que são expostas no `PublicClientApplicationOptions` , consulte a [documentação de referência](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Tarefas para o Xamarin iOS

Se você usar o MSAL.NET no Xamarin iOS, execute as seguintes tarefas.

* [Substituir e implementar a `OpenUrl` função no `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Habilitar grupos de conjunto de chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Habilitar compartilhamento de cache de token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Habilitar o acesso ao conjunto de chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Para obter mais informações, consulte [Considerações sobre o Xamarin Ios](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Tarefas para MSAL para iOS e macOS

Essas tarefas são necessárias quando você usa o MSAL para iOS e macOS:

* [Implementar o `openURL` retorno de chamada](#brokered-authentication-for-msal-for-ios-and-macos)
* [Habilitar grupos de acesso do conjunto de chaves](howto-v2-keychain-objc.md)
* [Personalizar navegadores e WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Tarefas para Xamarin. Android

Se você usar o Xamarin. Android, execute as seguintes tarefas:

- [Verifique se o controle retorna ao MSAL após a parte interativa do fluxo de autenticação terminar](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Atualizar o manifesto do Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)
- [Usar a exibição da Web inserida (opcional)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Solucionar problemas conforme necessário](msal-net-xamarin-android-considerations.md#troubleshooting)

Para obter mais informações, consulte [Considerações sobre o Xamarin. Android](msal-net-xamarin-android-considerations.md).

Para obter considerações sobre os navegadores no Android, consulte [Considerações específicas do Xamarin. Android com o MSAL.net](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Tarefas para UWP

No UWP, você pode usar redes corporativas. As seções a seguir explicam as tarefas que você deve concluir no cenário corporativo.

Para obter mais informações, consulte [Considerações específicas de UWP com MSAL.net](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Configurar o aplicativo para usar o agente

No Android e no iOS, os agentes habilitam:

- **SSO (logon único)**: você pode usar o SSO para dispositivos que são registrados com o Azure Active Directory (Azure AD). Quando você usa o SSO, os usuários não precisam entrar em cada aplicativo.
- **Identificação do dispositivo**: essa configuração habilita políticas de acesso condicional que estão relacionadas aos dispositivos do Azure AD. O processo de autenticação usa o certificado de dispositivo que foi criado quando o dispositivo foi ingressado no local de trabalho.
- **Verificação da identificação do aplicativo**: quando um aplicativo chama o agente, ele passa sua URL de redirecionamento. Em seguida, o agente o verifica.

### <a name="enable-the-broker-on-xamarin"></a>Habilitar o agente no Xamarin

Para habilitar o agente no Xamarin, use o `WithBroker()` parâmetro ao chamar o `PublicClientApplicationBuilder.CreateApplication` método. Por padrão, `.WithBroker()` é definido como true.

Para habilitar a autenticação orientada para o Xamarin. iOS, siga as etapas na [seção xamarin. Ios](#enable-brokered-authentication-for-xamarin-ios) neste artigo.

### <a name="enable-the-broker-for-msal-for-android"></a>Habilitar o agente para MSAL para Android

Para obter informações sobre como habilitar um agente no Android, consulte [autenticação orientada no Android](msal-android-single-sign-on.md).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Habilitar o agente para MSAL para iOS e macOS

A autenticação orientada é habilitada por padrão para cenários do Azure AD no MSAL para iOS e macOS.

As seções a seguir fornecem instruções para configurar seu aplicativo para o suporte de autenticação orientada para MSAL para Xamarin. iOS ou MSAL para iOS e macOS. Nos dois conjuntos de instruções, algumas das etapas são diferentes.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Habilitar a autenticação orientada para o Xamarin iOS

Siga as etapas nesta seção para habilitar seu aplicativo Xamarin. iOS para se comunicar com o aplicativo [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Etapa 1: habilitar o suporte do Broker

O suporte do Broker está desabilitado por padrão. Você o habilita para uma `PublicClientApplication` classe individual. Use o `WithBroker()` parâmetro ao criar a `PublicClientApplication` classe por meio de `PublicClientApplicationBuilder` . O `WithBroker()` parâmetro é definido como true por padrão.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Etapa 2: atualizar AppDelegate para manipular o retorno de chamada

Quando MSAL.NET chama o agente, o agente então chama de volta para seu aplicativo. Ele chama de volta usando o `AppDelegate.OpenUrl` método. Como MSAL aguarda a resposta do agente, seu aplicativo precisa cooperar para chamar MSAL.NET de volta. Você configura esse comportamento atualizando o `AppDelegate.cs` arquivo para substituir o método, como mostra o código a seguir.

```csharp
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

Esse método é invocado toda vez que o aplicativo é iniciado. É uma oportunidade para processar a resposta do agente e concluir o processo de autenticação que o MSAL.NET iniciou.

#### <a name="step-3-set-a-uiviewcontroller"></a>Etapa 3: definir um UIViewController ()

Para o Xamarin iOS, normalmente você não precisa definir uma janela de objeto. Mas, nesse caso, você deve defini-lo para que possa enviar e receber respostas de um agente. Para definir uma janela de objeto, no `AppDelegate.cs` , você define um `ViewController` .

Para definir a janela de objeto, siga estas etapas:

1. No `AppDelegate.cs` , defina o `App.RootViewController` para um novo `UIViewController()` . Essa configuração garante que a chamada para o agente inclua `UIViewController` . Se não estiver definido corretamente, você poderá receber esse erro:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Na `AcquireTokenInteractive` chamada, use `.WithParentActivityOrWindow(App.RootViewController)` . Passe a referência à janela de objeto que você usará. Veja um exemplo:

    Em `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Em `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    Na `AcquireToken` chamada:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>Etapa 4: registrar um esquema de URL

O MSAL.NET usa URLs para invocar o agente e, em seguida, retornar a resposta do agente de volta para seu aplicativo. Para concluir a viagem de ida e volta, registre o esquema de URL do seu aplicativo no `Info.plist` arquivo.

Para registrar o esquema de URL do seu aplicativo, siga estas etapas:

1. Prefixo `CFBundleURLSchemes` com `msauth` .
1. Adicionar `CFBundleURLName` ao final. Siga este padrão:

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente seu dispositivo. Por exemplo, se `BundleId` for `yourcompany.xforms` , o esquema de URL será `msauth.com.yourcompany.xforms` .

  
      Esse esquema de URL se tornará parte do URI de redirecionamento que identifica exclusivamente seu aplicativo quando receber a resposta do agente.

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

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Etapa 5: adicionar à seção LSApplicationQueriesSchemes

O MSAL usa `–canOpenURL:` para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que um aplicativo pode consultar.

Adicione `msauthv2` à `LSApplicationQueriesSchemes` seção do `Info.plist` arquivo, como no exemplo de código a seguir:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Autenticação orientada para MSAL para iOS e macOS

A autenticação orientada é habilitada por padrão para cenários do Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Etapa 1: atualizar AppDelegate para manipular o retorno de chamada

Quando o MSAL para iOS e macOS chama o agente, o agente retorna ao seu aplicativo usando o `openURL` método. Como MSAL aguarda a resposta do agente, seu aplicativo precisa cooperar para chamar novamente MSAL. Configure esse recurso atualizando o `AppDelegate.m` arquivo para substituir o método, como mostra os exemplos de código a seguir.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Se você adotou `UISceneDelegate` no Ios 13 ou posterior, coloque o retorno de chamada MSAL no `scene:openURLContexts:` de `UISceneDelegate` em vez disso. MSAL `handleMSALResponse:sourceApplication:` deve ser chamado apenas uma vez para cada URL.

Para obter mais informações, consulte a [documentação da Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Etapa 2: registrar um esquema de URL

O MSAL para iOS e macOS usa URLs para invocar o agente e, em seguida, retornar a resposta do agente para seu aplicativo. Para concluir a viagem de ida e volta, registre um esquema de URL para seu aplicativo no `Info.plist` arquivo.

Para registrar um esquema para seu aplicativo:

1. Prefixe seu esquema de URL personalizado com `msauth` .

1. Adicione seu identificador de pacote ao final do seu esquema. Siga este padrão:

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente seu dispositivo. Por exemplo, se `BundleId` for `yourcompany.xforms` , o esquema de URL será `msauth.com.yourcompany.xforms` .

    Esse esquema de URL se tornará parte do URI de redirecionamento que identifica exclusivamente seu aplicativo quando receber a resposta do agente. Verifique se o URI de redirecionamento no formato `msauth.(BundleId)://auth` está registrado para seu aplicativo no [portal do Azure](https://portal.azure.com).

   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Etapa 3: Adicionar LSApplicationQueriesSchemes

Adicione `LSApplicationQueriesSchemes` para permitir chamadas para o aplicativo Microsoft Authenticator, se ele estiver instalado.

> [!NOTE]
> O `msauthv3` esquema é necessário quando seu aplicativo é compilado usando o Xcode 11 e posterior.

Veja um exemplo de como adicionar `LSApplicationQueriesSchemes` :

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticação orientada para Xamarin. Android

Para obter informações sobre como habilitar um agente no Android, consulte [autenticação orientada no Xamarin. Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android).

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [adquirindo um token](scenario-mobile-acquire-token.md).
