---
title: Autenticação e autorização no Serviço de Aplicativo do Azure para aplicativos móveis| Microsoft Docs
description: Referência conceitual e visão geral do recurso Autenticação/Autorização do Serviço de Aplicativo do Azure, especificamente para aplicativos móveis
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859117"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autenticação e autorização no Serviço de Aplicativo do Azure para aplicativos móveis

Este artigo descreve como a autenticação e a autorização funcionam ao desenvolver aplicativos móveis nativos com um back-end do Serviço de Aplicativo. O Serviço de Aplicativo fornece autenticação e autorização integradas para que os aplicativos móveis possam conectar usuários sem alterar nenhum código no Serviço de Aplicativo. Ele fornece uma maneira fácil de proteger o aplicativo e trabalhar com dados por usuário. 

Este artigo concentra-se no desenvolvimento de aplicativos para dispositivos móveis. Para início rápido com autenticação e autorização do Serviço de Aplicativo para o aplicativo móvel, consulte um dos seguintes tutoriais [Adicionar autenticação ao aplicativo iOS][iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] ou [Cordova]). 

Para obter informações sobre como a autenticação e a autorização funcionam no Serviço de Aplicativo, consulte [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autenticação com SDK de provedor

Depois que a configuração for totalmente concluída no Serviço de Aplicativo, será possível modificar os clientes móveis para entrar com Serviço de Aplicativo. Há duas abordagens aqui:

* Utilize um SDK que um provedor de identidade específico publica para estabelecer a identidade e obtenha acesso ao Serviço de Aplicativo.
* Use uma única linha de código para permitir que o SDK do cliente dos Aplicativos Móveis possa fazer os usuários entrar.

> [!TIP]
> A maioria dos aplicativos deve usar um SDK de provedor para obter uma experiência mais consistente quando os usuários conectam, para usar o suporte à atualização de token e para obter outros benefícios especificados pelo provedor.
> 
> 

Quando você usar um provedor de SDK, os usuários poderão entrar e obter uma experiência mais rigidamente integrada ao sistema operacional em que o aplicativo está sendo executado. Esse método também fornece um token de provedor e algumas informações sobre o usuário no cliente, o que facilita muito o uso de APIs gráficas e a personalização da experiência do usuário. Ocasionalmente, em blogs e fóruns, ele é chamado de "fluxo do cliente" ou "fluxo direcionado ao cliente", porque o código no cliente assina usuários e o código do cliente tem acesso a um token do provedor.

Depois que um token do provedor é obtido, ele precisa ser enviado ao Serviço de Aplicativo para validação. Assim que o Serviço de Aplicativo valida o token, cria um novo token do Serviço de Aplicativo que é retornado ao cliente. O SDK do cliente dos Aplicativos Móveis tem métodos auxiliares para gerenciar essa troca e anexar automaticamente o token a todas as solicitações para o back-end do aplicativo. Os desenvolvedores também podem manter uma referência ao token do provedor.

Para obter mais informações sobre o fluxo de autenticação, consulte [Fluxo de autenticação do Serviço de Aplicativo](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autenticação sem SDK de provedor

Se você não quiser configurar um provedor de SDK, poderá permitir que o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure entre para você. O SDK do cliente dos Aplicativos Móveis abrirá uma exibição da Web para o provedor de sua escolha e fará o usuário entrar. Ocasionalmente, em blogs e fóruns, isso é chamado de “fluxo do servidor” ou “fluxo direcionado ao servidor”, já que o servidor gerencia o processo de entrada dos usuários e o SDK do cliente nunca recebe o token do provedor.

O código necessário para iniciar esse fluxo foi incluído no tutorial de autenticação de cada plataforma. Ao final do fluxo, o SDK do cliente tem um token do Serviço de Aplicativo, e o token é anexado automaticamente a todas as solicitações para o back-end do aplicativo.

Para obter mais informações sobre o fluxo de autenticação, consulte [Fluxo de autenticação do Serviço de Aplicativo](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Mais recursos

Os tutoriais a seguir mostram como adicionar autenticação aos clientes móveis usando o [fluxo direcionado ao servidor](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Adicionar autenticação ao seu aplicativo iOS][iOS]
* [Adicionar autenticação ao aplicativo do Android][Android]
* [Adicionar autenticação ao seu aplicativo do Windows][Windows]
* [Adicionar autenticação ao aplicativo Xamarin.iOS][Xamarin.iOS]
* [Adicionar autenticação ao aplicativo Xamarin.Android][Xamarin.Android]
* [Adicionar autenticação ao aplicativo Xamarin.Forms][Xamarin.Forms]
* [Adicionar a Autenticação ao aplicativo Cordova][Cordova]

Use os recursos a seguir se quiser usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para o Azure Active Directory:

* [Use a Biblioteca de Autenticação do Active Directory para iOS][ADAL-iOS]
* [Use a Biblioteca de Autenticação do Active Directory para Android][ADAL-Android]
* [Use a Biblioteca de Autenticação do Active Directory para Windows e Xamarin][ADAL-dotnet]

Use os recursos a seguir se quiser usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para o Facebook:

* [Usar o SDK do Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Use os recursos a seguir se quiser usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para o Twitter:

* [Usar o Twitter Fabric para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Use os seguintes recursos se quiser usar o [fluxo direcionado ao cliente](../app-service/overview-authentication-authorization.md#authentication-flow) para o Google:

* [Usar o SDK de logon do Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
