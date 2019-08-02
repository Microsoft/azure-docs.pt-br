---
title: Considerações sobre o Xamarin Android (biblioteca de autenticação da Microsoft para .NET) | Azure
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff55853c859008690548b161451a24941a597d3a
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277892"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considerações específicas do Xamarin Android com MSAL.NET
Este artigo discute considerações específicas ao usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).

## <a name="set-the-parent-activity"></a>Definir a atividade pai

No Xamarin. Android, você precisa definir a atividade pai para que o token seja retornado depois que a interação acontecer.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Você também pode definir isso no nível de PublicClientApplication (no MSAL 4.2 +) por meio de um retorno de chamada.

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Uma recomendação é usar o CurrentActivityPlugin [aqui](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Em seguida, o código do PublicClientApplication Builder ficaria assim:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Garantindo que o controle volte para MSAL depois que a parte interativa do fluxo de autenticação for encerrada
No Android, você precisa substituir o `OnActivityResult` método `Activity` do e chamar o método SetAuthenticationContinuationEventArgs da classe AuthenticationContinuationHelper MSAL.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Essa linha garante que o controle volte para MSAL depois que a parte interativa do fluxo de autenticação for encerrada.

## <a name="update-the-android-manifest"></a>Atualizar o manifesto do Android
O `AndroidManifest.xml` deve conter os seguintes valores:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Usar a exibição da Web inserida (opcional)

Por padrão, o MSAL.NET usa o navegador da Web do sistema, que permite que você obtenha o SSO com aplicativos Web e outros aplicativos. Em alguns casos raros, talvez você queira especificar que deseja usar a exibição da Web inserida. Para obter mais informações, consulte [MSAL.NET usa um navegador da Web e um](msal-net-web-browsers.md) [navegador do sistema Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Solução de problemas
Se você criar um novo aplicativo Xamarin. Forms e adicionar uma referência ao pacote NuGet do MSAL.Net, isso só funcionará.
No entanto, se você quiser atualizar um aplicativo Xamarin. Forms existente para o MSAL.NET Preview 1.1.2 ou posterior, você poderá enfrentar problemas de compilação.

Para solucionar esses problemas, você deve:
- Atualizar o pacote NuGet do MSAL.NET existente para o MSAL.NET Preview 1.1.2 ou posterior
- Verifique se o Xamarin. Forms foi atualizado automaticamente para a versão 2.5.0.122203 (caso contrário, atualize para esta versão)
- Verifique se o Xamarin. Android. support. v4 foi atualizado automaticamente para a versão 25.4.0.2 (caso contrário, atualize para esta versão)
- Todos os pacotes Xamarin. Android. support devem ter como destino a versão 25.4.0.2
- Limpar/recompilar
- Tente definir as compilações de projetos paralelos máximos como 1 no Visual Studio (opções-> projetos e soluções-> Build e Run-> número máximo de compilações de projetos paralelos)
- Como alternativa, se você estiver criando a partir da linha de comando, tente remover/m do comando se estiver usando-a.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Erro: O nome ' AuthenticationContinuationHelper ' não existe no contexto atual

Isso é provável porque o Visual Studio não atualizou corretamente o arquivo Android. csproj *. Às vezes, o  **\<HintPath >** FilePath contém incorretamente netstandard13 em vez de **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Próximas etapas

Mais detalhes e exemplos são fornecidos no parágrafo de [Considerações específicas do Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) do seguinte arquivo readme.MD do exemplo:

| Amostra | Plataforma | DESCRIÇÃO |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um aplicativo Xamarin Forms simples mostrando como usar o MSAL para autenticar o MSA e o Azure AD por meio do ponto de extremidade do adicione v 2.0 e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |