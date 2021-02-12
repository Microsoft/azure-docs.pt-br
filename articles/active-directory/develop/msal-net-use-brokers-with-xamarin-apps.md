---
title: Usar agentes com o Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar aplicativos Xamarin iOS que podem usar o Microsoft Authenticator e a MSAL.NET (biblioteca de autenticação da Microsoft para .NET). Saiba também como migrar da biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) para a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: ee9f374d110115b224c48853f26037dc461e5fa0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583834"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Usar Microsoft Authenticator ou Portal da Empresa do Intune em aplicativos Xamarin

No Android e iOS, os agentes como Microsoft Authenticator e o Microsoft Intune específico do Android Portal da Empresa habilitam:

- **SSO (logon único)**: os usuários não precisam entrar em cada aplicativo.
- **Identificação do dispositivo**: o agente acessa o certificado do dispositivo. Esse certificado é criado no dispositivo quando ele é ingressado no local de trabalho.
- **Verificação da identificação do aplicativo**: quando um aplicativo chama o agente, ele passa sua URL de redirecionamento. O agente verifica a URL.

Para habilitar um desses recursos, use o `WithBroker()` parâmetro ao chamar o `PublicClientApplicationBuilder.CreateApplication` método. O `.WithBroker()` parâmetro é definido como true por padrão.

A configuração da autenticação orientada na biblioteca de autenticação da Microsoft para .NET (MSAL.NET) varia de acordo com a plataforma:

* [Aplicativos iOS](#brokered-authentication-for-ios)
* [Aplicativos Android](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Autenticação orientada para iOS

Use as etapas a seguir para habilitar seu aplicativo Xamarin. iOS para se comunicar com o aplicativo [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) . Se você estiver direcionando para o iOS 13, considere ler sobre a [alteração da API de interrupção da Apple](./msal-net-xamarin-ios-considerations.md).

### <a name="step-1-enable-broker-support"></a>Etapa 1: habilitar o suporte do Broker

Você deve habilitar o suporte do Broker para instâncias individuais do `PublicClientApplication` . O suporte está desabilitado por padrão. Ao criar `PublicClientApplication` por meio `PublicClientApplicationBuilder` do, use o `WithBroker()` parâmetro como mostra o exemplo a seguir. O `WithBroker()` parâmetro é definido como true por padrão.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Etapa 2: habilitar o acesso ao conjunto de chaves

Para habilitar o acesso ao conjunto de chaves, você deve ter um grupo de acesso de cadeia de chaves para seu aplicativo. Você pode usar a `WithIosKeychainSecurityGroup()` API para definir o grupo de acesso do conjunto de chaves ao criar seu aplicativo:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Para obter mais informações, consulte [habilitar o acesso](msal-net-xamarin-ios-considerations.md#enable-keychain-access)ao conjunto de chaves.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Etapa 3: atualizar AppDelegate para manipular o retorno de chamada

Quando MSAL.NET chama o agente, o agente chama de volta para o seu aplicativo por meio do `OpenUrl` método da `AppDelegate` classe. Como MSAL aguarda a resposta do agente, seu aplicativo precisa cooperar para chamar MSAL.NET de volta. Para habilitar essa cooperação, atualize o arquivo *AppDelegate.cs* para substituir o método a seguir.

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

Esse método é invocado toda vez que o aplicativo é iniciado. Ele é usado como uma oportunidade de processar a resposta do agente e concluir o processo de autenticação que o MSAL.NET iniciou.

### <a name="step-4-set-uiviewcontroller"></a>Etapa 4: definir UIViewController ()

Ainda no arquivo *AppDelegate.cs* , defina uma janela de objeto. Normalmente, você não precisa definir a janela de objeto para o Xamarin iOS, mas precisa de uma janela de objeto para enviar e receber respostas do agente.

Para configurar a janela de objeto:

1. No arquivo *AppDelegate.cs* , defina `App.RootViewController` para um novo `UIViewController()` . Essa atribuição garante que a chamada para o agente inclua `UIViewController` . Se essa configuração for atribuída incorretamente, você poderá receber esse erro:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na `AcquireTokenInteractive` chamada, use `.WithParentActivityOrWindow(App.RootViewController)` e, em seguida, passe a referência para a janela de objeto que você usará.

    Em *app.cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    Em *AppDelegate.cs*:

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

### <a name="step-5-register-a-url-scheme"></a>Etapa 5: registrar um esquema de URL

O MSAL.NET usa URLs para invocar o agente e, em seguida, retornar a resposta do agente para seu aplicativo. Para concluir a viagem de ida e volta, registre um esquema de URL para seu aplicativo no arquivo *info. plist* .

O `CFBundleURLSchemes` nome deve incluir `msauth.` como um prefixo. Siga o prefixo com `CFBundleURLName` .

No esquema de URL, `BundleId` identifica exclusivamente o aplicativo: `$"msauth.(BundleId)"` . Portanto `BundleId` , se for `com.yourcompany.xforms` , o esquema de URL será `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Esse esquema de URL se torna parte do URI de redirecionamento que identifica exclusivamente seu aplicativo quando recebe a resposta do agente.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Etapa 6: Adicionar o identificador do agente à seção LSApplicationQueriesSchemes

O MSAL usa `–canOpenURL:` para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que um aplicativo pode consultar.

Adicione `msauthv2` à `LSApplicationQueriesSchemes` seção do arquivo *info. plist* , como no exemplo a seguir:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Etapa 7: adicionar um URI de redirecionamento ao registro do aplicativo

Quando você usa o agente, seu URI de redirecionamento tem um requisito extra. O URI de redirecionamento _deve_ ter o seguinte formato:

```csharp
$"msauth.{BundleId}://auth"
```

Aqui está um exemplo:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Observe que o URI de redirecionamento corresponde ao `CFBundleURLSchemes` nome que você incluiu no arquivo *info. plist* .

Adicione o URI de redirecionamento ao registro do aplicativo no [portal do Azure](https://portal.azure.com). Para gerar um URI de redirecionamento formatado corretamente, use **registros de aplicativo** na portal do Azure para gerar o URI de redirecionamento orientado a partir da ID do pacote.

**Para gerar o URI de redirecionamento:**

1. Entre no <a href="https://portal.azure.com/" target="_blank">Portal do Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Selecione **Azure Active Directory**  >  **registros de aplicativo** > seu aplicativo registrado
1. Selecione **autenticação**  >  **Adicionar uma plataforma**  >  **Ios/MacOS**
1. Insira a ID do pacote e, em seguida, selecione **Configurar**.

    Copie o URI de redirecionamento gerado que aparece na caixa de texto **URI de redirecionamento** para inclusão em seu código:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="configurações da plataforma iOS com URI de redirecionamento gerado no portal do Azure":::
1. Selecione **concluído** para concluir a geração do URI de redirecionamento.

## <a name="brokered-authentication-for-android"></a>Autenticação orientada para Android

### <a name="step-1-enable-broker-support"></a>Etapa 1: habilitar o suporte do Broker

O suporte do Broker está habilitado por `PublicClientApplication` base. Isso está desabilitado por padrão. Use o `WithBroker()` parâmetro (definido como true por padrão) ao criar o `IPublicClientApplication` através do `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Etapa 2: atualizar AppDelegate para manipular o retorno de chamada

Quando o MSAL.NET chama o agente, o agente, por sua vez, retorna a chamada para o seu aplicativo com o `OnActivityResult()` método. Como o MSAL aguardará a resposta do agente, seu aplicativo precisará rotear o resultado para MSAL.NET.

Encaminhe o resultado para o `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` método substituindo o `OnActivityResult()` método, como mostrado aqui:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Esse método é invocado toda vez que o aplicativo agente é iniciado e é usado como uma oportunidade para processar a resposta do agente e concluir o processo de autenticação iniciado pelo MSAL.NET.

### <a name="step-3-set-an-activity"></a>Etapa 3: definir uma atividade

Para habilitar a autenticação orientada, defina uma atividade para que o MSAL possa enviar e receber a resposta de e para o agente. Para fazer isso, forneça a atividade (geralmente, `MainActivity` ) ao `WithParentActivityOrWindow(object parent)` objeto pai.

Por exemplo, na chamada para `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Etapa 4: adicionar um URI de redirecionamento ao registro do aplicativo

O MSAL usa URLs para invocar o agente e, em seguida, retornar ao seu aplicativo. Para concluir essa viagem de ida e volta, registre um **URI de redirecionamento** para seu aplicativo usando o [portal do Azure](https://portal.azure.com).

O formato do URI de redirecionamento para seu aplicativo depende do certificado usado para assinar o APK. Por exemplo:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

A última parte do URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` , é a versão codificada em base64 da assinatura com a qual o apk é assinado. Ao desenvolver seu aplicativo no Visual Studio, se você estiver Depurando seu código sem assinar o APK com um certificado específico, o Visual Studio assinará o APK para você para fins de depuração. Quando o Visual Studio assina o APK para você dessa forma, ele fornece uma assinatura exclusiva para o computador no qual ele foi criado. Assim, sempre que você criar seu aplicativo em um computador diferente, precisará atualizar o URI de redirecionamento no código do aplicativo e o registro do aplicativo no portal do Azure para autenticar com o MSAL.

Durante a depuração, você pode encontrar uma exceção MSAL (ou mensagem de log) informando que o URI de redirecionamento fornecido está incorreto. **A exceção ou a mensagem de log também indica o URI de redirecionamento que você deve usar** com o computador atual no qual você está depurando. Você pode usar o URI de redirecionamento fornecido para continuar desenvolvendo seu aplicativo, desde que atualize o URI de redirecionamento no código e adicione o URI de redirecionamento fornecido ao registro do aplicativo no portal do Azure.

Quando você estiver pronto para finalizar seu código, atualize o URI de redirecionamento no código e o registro do aplicativo no portal do Azure para usar a assinatura do certificado com o qual você assina o APK.

Na prática, isso significa que você deve considerar a adição de um URI de redirecionamento para cada membro da sua equipe de desenvolvimento, *além* de um URI de redirecionamento para a versão de produção assinada do APK.

Você pode computar a assinatura por conta própria, semelhante a como o MSAL faz isso:

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Você também tem a opção de adquirir a assinatura para seu pacote usando o **keytool** com os seguintes comandos:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Etapa 5 (opcional): retorne para o navegador do sistema

Se o MSAL estiver configurado para usar o agente, mas o agente não estiver instalado, o MSAL voltará a usar uma exibição da Web (um navegador). O MSAL tentará autenticar usando o navegador do sistema padrão no dispositivo, que falha porque o URI de redirecionamento está configurado para o agente e o navegador do sistema não sabe como usá-lo para navegar de volta para o MSAL. Para evitar a falha, você pode configurar um *filtro de intenção* com o URI de redirecionamento do agente usado na etapa 4.

Modifique o manifesto do aplicativo para adicionar o filtro de intenção:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Por exemplo, se você tiver um URI de redirecionamento de `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` , seu manifesto deverá ser semelhante ao trecho de código XML a seguir.

É necessária a barra de encaminhamento ( `/` ) na frente da assinatura no `android:path` valor .

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Como alternativa, você pode configurar o MSAL para fazer fallback para o navegador incorporado, que não depende de um URI de redirecionamento:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Dicas de solução de problemas para autenticação orientada do Android

Aqui estão algumas dicas sobre como evitar problemas ao implementar a autenticação orientada no Android:

- **URI de redirecionamento** – adicione um URI de redirecionamento ao registro do aplicativo no [portal do Azure](https://portal.azure.com/). Um URI de redirecionamento ausente ou incorreto é um problema comum encontrado pelos desenvolvedores.
- **Versão do agente** -instale a versão mínima necessária dos aplicativos do agente. Qualquer um desses dois aplicativos pode ser usado para autenticação orientada no Android.
  - [Portal da empresa do Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (versão 5.0.4689.0 ou superior)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (versão 6.2001.0140 ou superior).
- **Precedência do agente** -MSAL se comunica com o *primeiro agente instalado* no dispositivo quando vários agentes são instalados.

    Exemplo: se você instalar primeiro Microsoft Authenticator e, em seguida, instalar o Portal da Empresa do Intune, a autenticação orientada ocorrerá *somente* no Microsoft Authenticator.
- **Logs** – se você encontrar um problema com a autenticação orientada, a exibição dos logs do agente poderá ajudá-lo a diagnosticar a causa.
  - Exibir logs de Microsoft Authenticator:

    1. Selecione o botão de menu no canto superior direito do aplicativo.
    1. Selecione **ajudar**  >  **enviar logs**  >  **Exibir logs**.
    1. Selecione **copiar tudo** para copiar os logs do agente para a área de transferência do dispositivo.

    A melhor maneira de depurar com esses logs é enviá-los por email para você mesmo e exibi-los em seu computador de desenvolvimento. Talvez você ache mais fácil analisar os logs em seu computador em vez de no próprio dispositivo. Você também pode usar um editor de teste no Android para salvar os logs como um arquivo de texto e, em seguida, usar um cabo USB para copiar o arquivo em um computador.

  - Exibir logs de Portal da Empresa do Intune:

    1. Selecione o botão de menu no canto superior esquerdo do aplicativo
    1. Selecione **configurações**  >  **dados de diagnóstico**
    1. Selecione **copiar logs** para copiar os logs do agente para o cartão SD do dispositivo.
    1. Conecte o dispositivo a um computador usando um cabo USB para exibir os logs em seu computador de desenvolvimento.

    Depois de ter os logs, você pode pesquisá-los para suas tentativas de autenticação por meio da ID de correlação. A ID de correlação é anexada a cada solicitação de autenticação. Para localizar erros retornados pelo ponto de extremidade de autenticação da plataforma de identidade da Microsoft, procure `AADSTS` .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [considerações para usar o plataforma universal do Windows com MSAL.net](msal-net-uwp-considerations.md).
