---
title: "Autenticação e Autorização nos Aplicativos Móveis do Azure | Microsoft Docs"
description: "Referência conceitual e visão geral do recurso Autenticação/Autorização para os Aplicativos Móveis do Azure"
services: app-service\mobile
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: a46dbf70-867d-48f6-8885-7f5207ad102e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 4105392f58eaf37e88c1d9ffb74f3f4133fa5482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autenticação e Autorização nos Aplicativos Móveis do Azure
## <a name="what-is-app-service-authentication--authorization"></a>O que é a Autenticação/Autorização do Serviço de Aplicativo?
> [!NOTE]
> Este tópico será migrado para um tópico consolidado de [Autenticação/Autorização de Serviço de Aplicativo](../app-service/app-service-authentication-overview.md) , que abrange a Web, celulares e aplicativos de API.
> 
> 

Autenticação/Autorização do Serviço de Aplicativo é um recurso que permite que o seu aplicativo faça logon dos usuários sem alterações de código necessárias no back-end do aplicativo. Ele fornece uma maneira fácil de proteger o aplicativo e trabalhar com dados por usuário.

O Serviço de Aplicativo usa uma identidade federada, em que um (“IDP”) **provedor de identidade** de terceiros armazena contas e autentica usuários, e o aplicativo usa essa identidade, em vez de sua própria. O Serviço de Aplicativo dá suporte a cinco provedores de identidade prontos para uso: *Azure Active Directory*, *Facebook*, *Google*, *Conta da Microsoft* e *Twitter*. Você também pode expandir esse suporte para seus aplicativos ao integrar outro provedor de identidade ou sua própria solução de identidade personalizada.

Seu aplicativo pode utilizar qualquer quantidade desses provedores de identidade, para que você possa fornecer opções de como fazer logon aos seus usuários finais.

Se você deseja começar agora mesmo, veja um dos seguintes tutoriais:

* [Adicione autenticação ao seu aplicativo do iOS]
* [Adicionar autenticação ao aplicativo Xamarin.iOS]
* [Adicione autenticação ao aplicativo Xamarin.Android]
* [Adicionar autenticação ao seu aplicativo do Windows]

## <a name="how-authentication-works"></a>Como funciona a autenticação
Para autenticar o uso de um dos provedores de identidade, primeiro você precisa configurar o provedor de identidade para conhecer seu aplicativo. O provedor de identidade, em seguida, fornecerá as IDs e os segredos que você fornece de volta para o aplicativo. Isso completa a relação de confiança e permite que o Serviço de Aplicativo valide as identidades fornecidas a ele.

Essas etapas são detalhadas nos seguintes tópicos:

* [Como configurar seu aplicativo para usar o logon do Active Directory do Azure]
* [Como configurar seu aplicativo para usar o logon do Facebook]
* [Como configurar seu aplicativo para usar o logon do Google]
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft]
* [Como configurar seu aplicativo para usar o logon do Twitter]

Depois que tudo estiver configurado no back-end, você pode modificar seu cliente para fazer logon. Há duas abordagens aqui:

* Usando uma única linha de código, permita que o SDK do cliente dos Aplicativos Móveis conectem os usuários.
* Utilize um SDK publicado por um provedor de identidade específico para estabelecer a identidade e, em seguida, obtenha acesso ao Serviço de Aplicativo.

> [!TIP]
> A maior parte dos aplicativos deve usar um SDK do provedor para obter uma experiência de logon que se parece mais com a nativa e para aproveitar o suporte de atualização e outros benefícios específicos do provedor.
> 
> 

### <a name="how-authentication-without-a-provider-sdk-works"></a>Como funciona a autenticação sem um SDK do provedor
Se não desejar configurar um SDK do provedor, você poderá permitir que os Aplicativos Móveis realizem o logon para você. O SDK do cliente dos Aplicativos Móveis abrirá uma exibição da Web para o provedor de sua escolha e concluirá a conexão. Ocasionalmente, em blogs e fóruns, você verá isso sendo chamado de “fluxo do servidor” ou “fluxo direcionado pelo servidor”, já que o servidor gerencia o logon e o SDK do cliente nunca recebe o token do provedor.

O código necessário para iniciar esse fluxo é abordado no tutorial de autenticação de cada plataforma. No final do fluxo, o SDK do cliente tem um token do Serviço de Aplicativo, e o token é anexado automaticamente a todas as solicitações para o back-end.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Como funciona a autenticação com um SDK do provedor
Trabalhar com um SDK do provedor possibilita que a experiência de logon interaja mais intimamente com o SO da plataforma em que o aplicativo está sendo executado. Isso também lhe fornece um token do provedor e algumas informações de usuário no cliente, o que torna muito mais fácil consumir Graph APIs e personalizar a experiência do usuário. Ocasionalmente, em blogs e fóruns você verá isso designado como “fluxo do cliente” ou “fluxo direcionado pelo cliente”, já que o código no cliente está manipulando o logon, e o código do cliente tem acesso a um token do provedor.

Depois que um token do provedor é obtido, ele precisa ser enviado ao Serviço de Aplicativo para validação. No final do fluxo, o SDK do cliente tem um token do Serviço de Aplicativo, e o token é anexado automaticamente a todas as solicitações para o back-end. O desenvolvedor também pode manter uma referência ao token do provedor se desejar.

## <a name="how-authorization-works"></a>Como funciona a autorização
Autenticação/Autorização do Serviço de Aplicativo expõe várias opções para **Ação a ser tomada quando a solicitação não estiver autenticada**. Antes que o código receba uma determinada solicitação, você pode fazer com que o Serviço de Aplicativo verifique se a solicitação é autenticada e, se não, rejeitá-la e tentar fazer com que o usuário faça logon antes de tentar novamente.

Uma opção é fazer com que as solicitações não autenticadas sejam redirecionadas para um dos provedores de identidade. Em um navegador da Web, isso na verdade levaria o usuário a uma nova página. No entanto, seu cliente móvel não poderá ser redirecionado dessa maneira, e as respostas não autenticadas receberão uma resposta HTTP *401 não autorizado* . Por causa disso, a primeira solicitação feita pelo cliente sempre deve ser para o ponto de extremidade de logon e, em seguida, você pode fazer chamadas para quaisquer outras APIs. Se você tentar chamar outra API antes de fazer logon, o cliente receberá um erro.

Se você deseja ter um controle mais granular sobre quais pontos de extremidade exigem a autenticação, você também pode escolher “Nenhuma ação (permitir solicitação)” para as solicitações não autenticadas. Nesse caso, todas as decisões de autenticação são adiadas para o código do aplicativo. Isso também permite que você conceda o acesso a usuários específicos de acordo com regras de autorização personalizadas.

## <a name="documentation"></a>Documentação
Os seguintes tutoriais mostram como adicionar a autenticação aos clientes móveis usando o Serviço de Aplicativo:

* [Adicione autenticação ao seu aplicativo do iOS]
* [Adicionar autenticação ao aplicativo Xamarin.iOS]
* [Adicione autenticação ao aplicativo Xamarin.Android]
* [Adicionar autenticação ao seu aplicativo do Windows]

Os seguintes tutoriais mostram como configurar o Serviço de Aplicativo para aproveitar provedores de autenticação diferentes:

* [Como configurar seu aplicativo para usar o logon do Active Directory do Azure]
* [Como configurar seu aplicativo para usar o logon do Facebook]
* [Como configurar seu aplicativo para usar o logon do Google]
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft]
* [Como configurar seu aplicativo para usar o logon do Twitter]

Se você deseja usar um sistema de identidade diferente daqueles fornecidos aqui, também é possível utilizar o [suporte de autenticação personalizada da visualização no SDK do servidor do .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Adicione autenticação ao seu aplicativo do iOS]: app-service-mobile-ios-get-started-users.md
[Adicionar autenticação ao aplicativo Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Adicione autenticação ao aplicativo Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Adicionar autenticação ao seu aplicativo do Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Como configurar seu aplicativo para usar o logon do Active Directory do Azure]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar seu aplicativo para usar o logon do Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar seu aplicativo para usar o logon do Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Como configurar seu aplicativo para usar o logon da Conta da Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar seu aplicativo para usar o logon do Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
