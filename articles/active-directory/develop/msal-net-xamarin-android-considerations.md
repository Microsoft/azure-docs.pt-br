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
ms.openlocfilehash: 34f2b146dda6e739f977c4894b5ec333c79d74d4
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063426"
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

## <a name="update-the-android-manifest"></a>Atualizar o manifesto do Android

O arquivo de *AndroidManifest.xml* deve conter os seguintes valores:

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
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

### <a name="xamarinforms-43x-manifest"></a>Manifesto do Xamarin. Forms 4.3. x

O Xamarin. Forms 4.3. x gera código que define o `package` atributo como `com.companyname.{appName}` em *AndroidManifest.xml*. Se você usar `DataScheme` as `msal{client_id}` , talvez queira alterar o valor para corresponder ao valor do `MainActivity.cs` namespace.

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
