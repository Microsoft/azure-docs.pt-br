---
title: Configuração e solução de problemas de código do Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as considerações para usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199555"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Requisitos de configuração e dicas de solução de problemas para o Xamarin Android com MSAL.NET

Há várias alterações de configuração que você precisa fazer em seu código ao usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET). As seções a seguir descrevem as modificações necessárias, seguidas por uma seção de [solução de problemas](#troubleshooting) para ajudá-lo a evitar alguns dos problemas mais comuns.

## <a name="set-the-parent-activity"></a>Definir a atividade pai

No Xamarin Android, defina a atividade pai para que o token seja retornado após a interação:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

No MSAL.NET 4,2 e posterior, você também pode definir essa funcionalidade no nível de [PublicClientApplication][PublicClientApplication]. Para fazer isso, use um retorno de chamada:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se você usar [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), o [`PublicClientApplication`][PublicClientApplication] código do Construtor deverá ser semelhante a este trecho de código:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Verifique se o controle retorna ao MSAL

Quando a parte interativa do fluxo de autenticação terminar, retorne o controle para MSAL substituindo o [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] método.

Em sua substituição, chame MSAL. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` método para retornar o controle para MSAL no final da parte interativa do fluxo de autenticação.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>Atualizar o manifesto do Android para o suporte do sistema de WebView 

Para dar suporte ao sistema WebView, o arquivo de *AndroidManifest.xml* deve conter os seguintes valores:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

O `android:scheme` valor é criado a partir do URI de redirecionamento configurado no portal do aplicativo. Por exemplo, se o URI de redirecionamento for `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` , a `android:scheme` entrada no manifesto ficaria parecida com este exemplo:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Como alternativa, [crie a atividade em código](/xamarin/android/platform/android-manifest#the-basics) em vez de editar manualmente *AndroidManifest.xml*. Para criar a atividade no código, primeiro crie uma classe que inclua o `Activity` atributo e o `IntentFilter` atributo.

Aqui está um exemplo de uma classe que representa os valores do arquivo XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Usar o sistema de WebView na autenticação orientada

Para usar o modo de exibição da Web do sistema como um fallback para autenticação interativa quando você configurou seu aplicativo para usar a autenticação orientada e o dispositivo não tem um agente instalado, habilite MSAL para capturar a resposta de autenticação usando o URI de redirecionamento do agente. O MSAL tentará autenticar usando o sistema de WebView padrão no dispositivo quando detectar que o agente está indisponível. Usando esse padrão, ele falhará porque o URI de redirecionamento está configurado para usar um agente e o WebView do sistema não sabe como usá-lo para retornar ao MSAL. Para resolver isso, crie um _filtro de intenção_ usando o URI de redirecionamento do agente que você configurou anteriormente. Adicione o filtro de intenção modificando o manifesto do aplicativo, como neste exemplo:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Substitua o nome do pacote que você registrou no portal do Azure para o `android:host=` valor. Substitua o hash de chave que você registrou no portal do Azure para o `android:path=` valor. O hash de assinatura *não* deve ser codificado em URL. Verifique se uma barra () à esquerda `/` é exibida no início do hash de assinatura.

### <a name="xamarinforms-43x-manifest"></a>Manifesto do Xamarin. Forms 4.3. x

O Xamarin. Forms 4.3. x gera código que define o `package` atributo como `com.companyname.{appName}` em *AndroidManifest.xml*. Se você usar `DataScheme` as `msal{client_id}` , talvez queira alterar o valor para corresponder ao valor do `MainActivity.cs` namespace.

## <a name="android-11-support"></a>Suporte do Android 11

Para usar o navegador do sistema e a autenticação orientada no Android 11, você deve primeiro declarar esses pacotes, para que eles fiquem visíveis para o aplicativo. Os aplicativos destinados ao Android 10 (API 29) e anteriores podem consultar o sistema operacional para obter uma lista de pacotes disponíveis no dispositivo em um determinado momento. Para dar suporte à privacidade e segurança, o Android 11 reduz a visibilidade do pacote para uma lista padrão de pacotes do sistema operacional e os pacotes especificados no arquivo de *AndroidManifest.xml* do aplicativo. 

Para permitir que o aplicativo seja autenticado usando o navegador do sistema e o agente, adicione a seguinte seção a *AndroidManifest.xml*:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Substituir `{Package Name}` pelo nome do pacote de aplicativos. 

Seu manifesto atualizado, que agora inclui suporte para o navegador do sistema e a autenticação orientada, deve ser semelhante a este exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Usar a exibição da Web inserida (opcional)

Por padrão, o MSAL.NET usa o navegador da Web do sistema. Este navegador permite que você obtenha SSO (logon único) usando aplicativos Web e outros aplicativos. Em alguns casos raros, talvez você queira que o seu sistema use uma exibição da Web inserida.

Este exemplo de código mostra como configurar uma exibição da Web inserida:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Para obter mais informações, consulte [usar navegadores da Web para](msal-net-web-browsers.md) [Considerações sobre o navegador do sistema MSAL.net e Xamarin Android](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="general-tips"></a>Dicas gerais

- Atualize o pacote NuGet do MSAL.NET existente para a [versão mais recente do MSAL.net](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Verifique se o Xamarin. Forms está na versão mais recente.
- Verifique se Xamarin. Android. support. v4 está na versão mais recente.
- Verifique se todos os pacotes Xamarin. Android. support têm como destino a versão mais recente.
- Limpar ou recompilar o aplicativo.
- No Visual Studio, tente definir o número máximo de compilações de projetos paralelas como **1**. Para fazer isso, selecione **Opções**  >  **projetos e soluções**  >  **Compilar e executar** o  >  **número máximo de compilações de projetos paralelos**.
- Se você estiver criando a partir da linha de comando e o comando usar `/m` , tente remover esse elemento do comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erro: o nome AuthenticationContinuationHelper não existe no contexto atual

Se um erro indicar que `AuthenticationContinuationHelper` não existe no contexto atual, o Visual Studio poderá ter atualizado incorretamente o arquivo *Android. csproj \** . Às vezes, o caminho do arquivo no `<HintPath>` elemento contém incorretamente `netstandard13` , em vez de `monoandroid90` .

Este exemplo contém um caminho de arquivo correto:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o exemplo de um [aplicativo do Xamarin Mobile que usa a plataforma de identidade da Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). A tabela a seguir resume as informações relevantes no arquivo LEIAme.

| Amostra | Plataforma | Descrição |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Um aplicativo Xamarin. Forms simples que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e o Azure AD por meio do ponto de extremidade 2,0 do Azure AD. O aplicativo também mostra como acessar Microsoft Graph e mostra o token resultante. <br>![Diagrama de fluxo de autenticação](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
