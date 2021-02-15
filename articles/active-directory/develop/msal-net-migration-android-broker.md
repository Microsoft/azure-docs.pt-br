---
title: Migrar aplicativos Xamarin Android usando agentes para MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicativos Xamarin Android que usam o Microsoft Authenticator ou Portal da Empresa do Intune de ADAL.NET para MSAL.NET.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 01af0e620ecb100839f7b1101e5ff9fcfc874eea
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206662"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>Migrar aplicativos Android que usam um agente de ADAL.NET para MSAL.NET

Se você tiver um aplicativo Xamarin Android usando atualmente o Azure Active Directory ADAL.NET (biblioteca de autenticação para .NET) e um [agente de autenticação](msal-android-single-sign-on.md), é hora de migrar para o MSAL.net ( [Microsoft Authentication library for .net ](msal-overview.md) ).

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo Xamarin Android já integrado a um agente ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [Portal da Empresa do Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) e Adal.NET que você precisa migrar para o MSAL.net.

## <a name="step-1-enable-the-broker"></a>Etapa 1: habilitar o agente

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
No ADAL.NET, o suporte do Broker é habilitado em uma base de contexto por autenticação.

Para chamar o agente, você precisava definir um `useBroker` como *true* no `PlatformParameters` Construtor:

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

No código do processador de páginas específico da plataforma para Android, defina o `useBroker` sinalizador como true:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Em seguida, inclua os parâmetros na chamada de token de aquisição:

```CSharp
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
No MSAL.NET, o suporte do Broker é habilitado em uma base por PublicClientApplication.

Use o `WithBroker()` parâmetro (que é definido como true por padrão) para chamar o Broker:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

Em seguida, na chamada AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>Etapa 2: definir uma atividade

No ADAL.NET, você passou em uma atividade (geralmente a MainActivity) como parte da Plataformaparameters, conforme mostrado na [etapa 1: habilitar o agente](#step-1-enable-the-broker).

O MSAL.NET também usa uma atividade, mas não é necessário no uso normal do Android sem um agente. Para usar o agente, defina a atividade para enviar e receber respostas do agente.

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
A atividade é passada para o Platformparameters na plataforma específica do Android.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

No MSAL.NET, faça duas coisas para definir a atividade para Android:

1. No `MainActivity.cs` , defina o `App.RootViewController`  para `MainActivity` para garantir que haja uma atividade com a chamada para o agente.

    Se não estiver definido corretamente, você poderá receber esse erro: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. Na chamada AcquireTokenInteractive, use `.WithParentActivityOrWindow(App.RootViewController)` e passe a referência para a atividade que será usada. Este exemplo usará o MainActivity.

**Por exemplo: **

Em *app.cs*:

```CSharp
   public static object RootViewController { get; set; }
```

Em *MainActivity.cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

Na chamada AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre considerações específicas do Android ao usar o MSAL.NET com Xamarin, consulte [requisitos de configuração e dicas de solução de problemas para o Xamarin Android com MSAL.net](msal-net-xamarin-android-considerations.md).