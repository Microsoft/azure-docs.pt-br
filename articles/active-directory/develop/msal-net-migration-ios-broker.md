---
title: Migrar aplicativos Xamarin usando agentes para o MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicativos do Xamarin iOS que usam Microsoft Authenticator de ADAL.NET para MSAL.NET.
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
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89177424"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrar aplicativos iOS que usam Microsoft Authenticator de ADAL.NET para MSAL.NET

Você esteve usando o Azure Active Directory ADAL.NET (biblioteca de autenticação para .NET) e o agente do iOS. Agora é hora de migrar para a MSAL.NET ( [biblioteca de autenticação da Microsoft](msal-overview.md) para .net), que dá suporte ao agente no Ios da versão 4,3 em diante.

Por onde você deve começar? Este artigo ajuda você a migrar seu aplicativo Xamarin iOS da ADAL para MSAL.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já tenha um aplicativo Xamarin iOS integrado ao agente do iOS. Se você não fizer isso, mova diretamente para MSAL.NET e comece a implementação do agente. Para obter informações sobre como invocar o agente do iOS no MSAL.NET com um novo aplicativo, consulte [esta documentação](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Tela de fundo

### <a name="what-are-brokers"></a>O que são agentes?

Os agentes são aplicativos fornecidos pela Microsoft no Android e no iOS. (Consulte o aplicativo [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) no Ios e Android e o aplicativo portal da empresa do Intune no Android.)

Eles permitem:

- Logon único.
- Identificação do dispositivo, que é exigido por algumas [políticas de acesso condicional](../conditional-access/overview.md). Para obter mais informações, consulte [Gerenciamento de dispositivos](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Verificação de identificação do aplicativo, que também é necessária em alguns cenários empresariais. Para obter mais informações, consulte [Gerenciamento de aplicativo móvel (MAM) do Intune](/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrar do ADAL para o MSAL

### <a name="step-1-enable-the-broker"></a>Etapa 1: habilitar o agente

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
No ADAL.NET, o suporte do Broker foi habilitado em uma base de contexto por autenticação. Isso está desabilitado por padrão. Você precisava definir um

`useBroker` Sinalizar como verdadeiro no `PlatformParameters` construtor para chamar o agente:

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
Além disso, no código específico da plataforma, neste exemplo, no renderizador de página para iOS, defina o `useBroker`
sinalizar para verdadeiro:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

Em seguida, inclua os parâmetros na chamada de token de aquisição:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource,
                              ClientId,
                              new Uri(RedirectURI),
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
No MSAL.NET, o suporte do Broker é habilitado em uma base por PublicClientApplication. Isso está desabilitado por padrão. Para habilitá-lo, use o

`WithBroker()` (definido como true por padrão) para chamar o agente:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Na chamada de aquisição de token:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Etapa 2: definir um UIViewController ()
No ADAL.NET, você passou em um UIViewController como parte do `PlatformParameters` . (Consulte o exemplo na etapa 1.) No MSAL.NET, para dar aos desenvolvedores mais flexibilidade, uma janela de objeto é usada, mas não é necessária no uso normal do iOS. Para usar o agente, defina a janela de objeto para enviar e receber respostas do agente.
<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
Um UIViewController é passado para

`PlatformParameters` na plataforma específica do iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
No MSAL.NET, você faz duas coisas para definir a janela de objeto para iOS:

1. Em `AppDelegate.cs` , defina `App.RootViewController` para um novo `UIViewController()` .
Essa atribuição garante que há um UIViewController com a chamada para o agente. Se não estiver definido corretamente, você poderá receber esse erro: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na chamada AcquireTokenInteractive, use `.WithParentActivityOrWindow(App.RootViewController)` e passe a referência à janela de objeto que você usará.

**Por exemplo:**

Em `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
Em `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Na chamada de aquisição de token:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Etapa 3: atualizar AppDelegate para manipular o retorno de chamada
A ADAL e a MSAL chamam o agente, e o agente, por sua vez, chama de volta para seu aplicativo por meio do `OpenUrl` método da `AppDelegate` classe. Para obter mais informações, consulte [esta documentação](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Não há nenhuma alteração aqui entre ADAL.NET e MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Etapa 4: registrar um esquema de URL
ADAL.NET e MSAL.NET usam URLs para invocar o agente e retornar a resposta do agente de volta para o aplicativo. Registre o esquema de URL no `Info.plist` arquivo para seu aplicativo da seguinte maneira:

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
O esquema de URL é exclusivo para seu aplicativo.
</td><td>
O

`CFBundleURLSchemes` o nome deve incluir

`msauth.`

como um prefixo, seguido pelo seu `CFBundleURLName`

Por exemplo: `$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> Esse esquema de URL se torna parte do URI de redirecionamento que é usado para identificar exclusivamente o aplicativo quando ele recebe a resposta do agente.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Etapa 5: Adicionar o identificador do agente à seção LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET usam `-canOpenURL:` para verificar se o agente está instalado no dispositivo. Adicione o identificador correto para o agente do iOS à seção LSApplicationQueriesSchemes do arquivo info. plist da seguinte maneira:

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
Usos

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Usos

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>Etapa 6: registrar seu URI de redirecionamento no portal do Azure

ADAL.NET e MSAL.NET adicionam um requisito extra no URI de redirecionamento quando ele se destina ao agente. Registre o URI de redirecionamento com seu aplicativo no portal do Azure.
<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Exemplo:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Exemplo:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Para obter mais informações sobre como registrar o URI de redirecionamento no portal do Azure, consulte [etapa 7: adicionar um URI de redirecionamento ao registro do aplicativo](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration).

### <a name="step-7-set-the-entitlementsplist"></a>**Etapa 7: definir os direitos. plist**

Habilite o acesso ao conjunto de chaves no arquivo de *direitos. plist* :

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

Para obter mais informações sobre como habilitar o acesso ao conjunto de chaves, consulte [habilitar o acesso](msal-net-xamarin-ios-considerations.md#enable-keychain-access)ao conjunto de chaves.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Considerações específicas do Xamarin Ios com o MSAL.net](msal-net-xamarin-ios-considerations.md).