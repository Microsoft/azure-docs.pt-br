---
title: Fornecer um proxy de & HttpClient (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como fornecer seu próprio HttpClient e proxy para se conectar ao Azure AD usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6139fd1b081c69f037ec9cd3313e4a6499c39543
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064616"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Como fornecer seu próprio HttpClient e proxy usando MSAL.NET
Ao [inicializar um aplicativo cliente público](msal-net-initializing-client-applications.md), você pode usar o `.WithHttpClientFactory method` para fornecer seu próprio HttpClient.  Fornecer seu próprio HttpClient permite cenários avançados, como o controle refinado de um proxy HTTP, a personalização dos cabeçalhos do agente do usuário ou forçar o MSAL a usar um HttpClient específico (por exemplo, em aplicativos Web/APIs do ASP.NET Core).

## <a name="initialize-with-httpclientfactory"></a>Inicializar com HttpClientFactory
O exemplo a seguir mostra para criar um `HttpClientFactory` e, em seguida, inicializar um aplicativo cliente público com ele:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Ao usar o Xamarin iOS, é recomendável criar uma `HttpClient` que use explicitamente o `NSURLSession`, com base no manipulador para o iOS 7 e mais recente. O MSAL.NET cria automaticamente um `HttpClient` que usa `NSURLSessionHandler` para iOS 7 e mais recente. Para saber mais, leia a [Documentação do Xamarin iOS para HttpClient](/xamarin/cross-platform/macios/http-stack).