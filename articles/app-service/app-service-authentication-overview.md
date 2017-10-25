---
title: "Autenticação e autorização no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Referência conceitual e visão geral do recurso Autenticação/Autorização para o Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: 4ba4155515e587038ffe2dbca064ad27aca97445
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicativo do Azure
## <a name="what-is-app-service-authentication--authorization"></a>O que é a Autenticação/Autorização do Serviço de Aplicativo?
A Autenticação/Autorização do Serviço de Aplicativo é um recurso que oferece uma maneira para seu aplicativo conectar usuários de forma que você não precise alterar o código no back-end do aplicativo. Ele fornece uma maneira fácil de proteger o aplicativo e trabalhar com dados por usuário.

O Serviço de Aplicativo usa uma identidade federada, na qual um provedor de identidade de terceiros armazena contas e autentica usuários. O aplicativo se baseia nas informações de identidade do provedor para que o aplicativo não tenha de armazenar essas informações por conta própria. O Serviço de Aplicativo dá suporte a cinco provedores de identidade prontos para uso: Azure Active Directory, Facebook, Google, Conta da Microsoft e Twitter. Seu aplicativo pode utilizar qualquer quantidade desses provedores de identidade para fornecer aos usuários opções de como entrar. Para expandir esse suporte interno, você pode integrar outro provedor de identidade ou [sua própria solução de identidade personalizada][custom-auth].

Se você deseja começar agora mesmo, veja um dos seguintes tutoriais:

* [Adicionar autenticação ao aplicativo iOS][iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] ou [Cordova])
* [Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure][apia-user]

## <a name="how-authentication-works-in-app-service"></a>Como funciona a autenticação no Serviço de Aplicativo
Para autenticar o uso de um dos provedores de identidade, primeiro você precisa configurar o provedor de identidade para conhecer seu aplicativo. O provedor de identidade, em seguida, fornecerá as IDs e os segredos que você fornecer para o Serviço de Aplicativo. Isso conclui a relação de confiança para que o Serviço de Aplicativo possa validar as declarações de usuário, como tokens de autenticação, do provedor de identidade.

Para conectar um usuário usando um desses provedores, o usuário deverá ser redirecionado para um ponto de extremidade que permite a entrada de usuários nesse provedor. Se os clientes estiverem usando um navegador da Web, será possível fazer com que o Serviço de Aplicativo direcione automaticamente todos os usuários não autenticados para o ponto de extremidade que permite a entrada de usuários. Caso contrário, você precisará direcionar seus clientes para `{your App Service base URL}/.auth/login/<provider>`, em que `<provider>` é um dos seguintes valores: aad, facebook, google, microsoft ou twitter. Os cenários móveis e de API serão explicados em seções posteriores deste artigo.

Os usuários que interagem com seu aplicativo por meio de um navegador da Web terão um cookie definido, para que possam permanecer autenticados conforme navegam no aplicativo. Para outros tipos de cliente, como móvel, um JWT (token Web JSON) que deve ser apresentado no cabeçalho `X-ZUMO-AUTH` , será emitido para o cliente. Os SDKs do cliente dos Aplicativos Móveis cuidarão disso para você. Como alternativa, um token de identidade ou de acesso do Azure Active Directory poderá ser incluído diretamente no cabeçalho `Authorization` como um [token de portador](https://tools.ietf.org/html/rfc6750).

O Serviço de Aplicativo validará qualquer cookie ou token que seu aplicativo emitir para autenticar usuários. Para restringir os usuários que tem permissão de acessar seu aplicativo, veja a seção [Autorização](#authorization) , posteriormente neste artigo.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Autenticação móvel com um SDK do provedor
Depois que tudo estiver configurado no back-end, é possível modificar os clientes móveis para entrar com o Serviço de Aplicativo. Há duas abordagens aqui:

* Utilize um SDK que um provedor de identidade específico publica para estabelecer a identidade e obtenha acesso ao Serviço de Aplicativo.
* Use uma única linha de código para permitir que o SDK do cliente dos Aplicativos Móveis possa fazer os usuários entrar.

> [!TIP]
> A maioria dos aplicativos deve usar um provedor de SDK para obter uma experiência mais consistente quando os usuários entram, para usar o suporte atualizado e para obter outros benefícios especificados pelo provedor.
> 
> 

Quando você usar um provedor de SDK, os usuários poderão entrar e obter uma experiência mais rigidamente integrada ao sistema operacional em que o aplicativo está sendo executado. Isso também lhe fornece um token do provedor e algumas informações de usuário no cliente, o que torna muito mais fácil consumir Graph APIs e personalizar a experiência do usuário. Ocasionalmente, em blogs e fóruns você verá isso designado como “fluxo do cliente” ou “fluxo direcionado pelo cliente”, porque o código no cliente faz os clientes entrarem, e o código do cliente tem acesso a um token do provedor.

Depois que um token do provedor é obtido, ele precisa ser enviado ao Serviço de Aplicativo para validação. Assim que o Serviço de Aplicativo valida o token, cria um novo token do Serviço de Aplicativo que é retornado ao cliente. O SDK do cliente dos Aplicativos Móveis tem métodos auxiliares para gerenciar essa troca e anexa automaticamente o token a todas as solicitações para o back-end do aplicativo. Os desenvolvedores também podem manter uma referência ao token do provedor. se desejarem.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Autenticação móvel sem um SDK do provedor
Se você não quiser configurar um provedor de SDK, poderá permitir que o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure entre para você. O SDK do cliente dos Aplicativos Móveis abrirá uma exibição da Web para o provedor de sua escolha e fará o usuário entrar. Ocasionalmente, em blogs e fóruns, você verá isso sendo chamado de “fluxo do servidor” ou “fluxo direcionado pelo servidor”, já que o servidor gerencia o processo de entrada dos usuários, e o SDK do cliente nunca recebe o token do provedor.

O código necessário para iniciar esse fluxo foi incluído no tutorial de autenticação de cada plataforma. Ao final do fluxo, o SDK do cliente tem um token do Serviço de Aplicativo, e o token é anexado automaticamente a todas as solicitações para o back-end do aplicativo.

### <a name="service-to-service-authentication"></a>Autenticação serviço a serviço
Embora você possa conceder aos usuários acesso ao seu aplicativo, também pode confiar em outro aplicativo para chamar sua própria API. Por exemplo, você poderia ter um aplicativo Web chamando uma API em outro aplicativo Web. Neste cenário, você usa as credenciais para uma conta de serviço em vez de credenciais de usuário para obter um token. Uma conta de serviço também é conhecida como *entidade de serviço* no jargão do Azure Active Directory, e a autenticação que usa essa conta também é chamada de cenário de serviço a serviço.

> [!IMPORTANT]
> Como os aplicativos móveis são executados em dispositivos do cliente, os aplicativos móveis *não* contam como aplicativos confiáveis e não devem usar um fluxo da entidade de serviço. Em vez disso, eles devem usar um fluxo de usuário detalhado anteriormente.
> 
> 

Para cenários de serviço a serviço, o Serviço de Aplicativo pode proteger seu aplicativo usando o Azure Active Directory. O aplicativo de chamada precisa apenas fornecer um token de autorização da entidade de serviço do Azure Active Directory obtido pelo fornecimento da ID do cliente e do segredo do cliente por meio do Azure Active Directory. Um exemplo desse cenário que usa aplicativos de API ASP.NET é explicado pelo tutorial [Autenticação de entidade de serviço para Aplicativos de API][apia-service].

Se quiser usar a autenticação do Serviço de Aplicativo para lidar com um cenário de serviço para serviço, você poderá usar os certificados de cliente ou a autenticação básica. Para saber mais sobre certificados de cliente no Azure, veja [Como configurar a Autenticação Mútua TLS para aplicativos Web](app-service-web-configure-tls-mutual-auth.md). Para saber mais sobre a autenticação básica no ASP.NET, veja [Authentication Filters in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters)(Filtros de autenticação na API Web ASP.NET 2).

A autenticação de conta de serviço de um aplicativo lógico do Serviço de Aplicativo para um aplicativo de API é um caso especial, que é explicado detalhadamente em [Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos](../logic-apps/logic-apps-custom-hosted-api.md).

## <a name="authorization"></a>Como funciona a autorização no Serviço de Aplicativo
Você tem controle total sobre as solicitações que podem acessar o aplicativo. A Autenticação/Autorização do Serviço de Aplicativo pode ser configurada com qualquer um dos seguintes comportamentos:

* Permitir que apenas solicitações autenticadas cheguem ao aplicativo.
  
    Se um navegador receber uma solicitação anônima, o Serviço de Aplicativo será redirecionado para o provedor de identidade escolhido para que os usuários possam entrar. Se a solicitação vier de um dispositivo móvel, a resposta retornada será HTTP *401 Não Autorizado* .
  
    Com essa opção, você não precisa escrever nenhum código de autenticação em seu aplicativo. Se precisar de uma autorização mais refinada, as informações sobre o usuário estarão disponíveis para seu código.
* Permitir que todas as solicitações cheguem ao aplicativo, mas validar as solicitações autenticadas e transmitir informações de autenticação nos cabeçalhos HTTP.
  
    Essa opção adia as decisões de autorização no código do aplicativo. Ela oferece mais flexibilidade no tratamento de solicitações anônimas, mas você precisa escrever um código.
* Permitir que todas as solicitações cheguem ao aplicativo e não executar nenhuma ação em relação às informações de autenticação nas solicitações.
  
    Nesse caso, o recurso Autenticação/Autorização é desativado. As tarefas de autenticação e autorização ficam inteiramente a cargo do código do aplicativo.

Os comportamentos anteriores são controlados pela **Ação a ser tomada quando a solicitação não for autenticada** no portal do Azure. Se você escolher **Fazer logon com o *nome do provedor***, todas as solicitações terão que ser autenticadas. **Permitir solicitação (nenhuma ação)** adia a decisão de autorização no código, mas ainda fornece informações de autenticação. Se deseja que seu código lide com tudo, é possível desabilitar o recurso Autenticação/Autorização.

## <a name="working-with-user-identities-in-your-application"></a>Trabalhando com identidades de usuário em seu aplicativo
O Serviço de Aplicativo transmite algumas informações do usuário para seu aplicativo usando cabeçalhos especiais. As solicitações externas proíbem esses cabeçalhos e só estarão presentes se definidas pela Autenticação/Autorização do Serviço de Aplicativo. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Um código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias desses cabeçalhos. Para aplicativos ASP.NET 4.6, **ClaimsPrincipal** é definido automaticamente com os valores apropriados.

O aplicativo também pode obter detalhes adicionais do usuário por meio de um HTTP GET em seu ponto de extremidade `/.auth/me` . Um token válido incluído na solicitação retornará uma carga JSON com detalhes sobre o provedor que está sendo usado, o token do provedor subjacente e algumas outras informações do usuário. Os SDKs do servidor dos Aplicativos Móveis fornecem métodos auxiliares para trabalhar com esses dados. Para saber mais, confira [Como usar o SDK do Node.js dos Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) e [Trabalhar com o SDK do servidor de back-end .NET para Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentação e recursos adicionais
### <a name="identity-providers"></a>Provedores de identidade
Os seguintes tutoriais mostram como configurar o Serviço de Aplicativo para usar provedores de autenticação diferentes:

* [Como configurar seu aplicativo para usar o logon do Azure Active Directory][AAD]
* [Como configurar seu aplicativo para usar o logon do Facebook][Facebook]
* [Como configurar seu aplicativo para usar o logon do Google][Google]
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft][MSA]
* [Como configurar seu aplicativo para usar o logon do Twitter][Twitter]

Se você deseja usar um sistema de identidade diferente daqueles fornecidos aqui, também é possível utilizar o [suporte de autenticação personalizada da visualização no SDK do servidor do .NET dos Aplicativos Móveis][custom-auth], que pode ser usado em aplicativos Web, em aplicativos móveis ou em aplicativos de API.

### <a name="web-applications"></a>Aplicativos Web
Os tutoriais a seguir mostram como adicionar a autenticação a um aplicativo Web:

* [Introdução ao Serviço de Aplicativo do Azure – Parte 2] [web-getstarted]

### <a name="mobile-applications"></a>Aplicativos móveis
Os seguintes tutoriais mostram como adicionar a autenticação aos clientes móveis usando o fluxo direcionado pelo servidor:

* [Adicionar autenticação ao seu aplicativo iOS][iOS]
* [Adicionar autenticação ao aplicativo do Android][Android]
* [Adicionar autenticação ao seu aplicativo do Windows][Windows]
* [Adicionar autenticação ao aplicativo Xamarin.iOS][Xamarin.iOS]
* [Adicionar autenticação ao aplicativo Xamarin.Android][Xamarin.Android]
* [Adicionar autenticação ao aplicativo Xamarin.Forms][Xamarin.Forms]
* [Adicionar a Autenticação ao aplicativo Cordova][Cordova]

Use os recursos a seguir se quiser usar o fluxo direcionado ao cliente para o Azure Active Directory:

* [Use a Biblioteca de Autenticação do Active Directory para iOS][ADAL-iOS]
* [Use a Biblioteca de Autenticação do Active Directory para Android][ADAL-Android]
* [Use a Biblioteca de Autenticação do Active Directory para Windows e Xamarin][ADAL-dotnet]

Use os recursos a seguir se quiser usar o fluxo direcionado ao cliente para o Facebook:

* [Usar o SDK do Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Use os recursos a seguir se quiser usar o fluxo direcionado ao cliente para o Twitter:

* [Usar o Twitter Fabric para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Use os recursos a seguir se quiser usar o fluxo direcionado ao cliente para o Google:

* [Usar o SDK de logon do Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

<!-- ### API applications
The following tutorials show how to protect your API apps:

* [User authentication for API Apps in Azure App Service][apia-user]
* [Service principal authentication for API Apps in Azure App Service][apia-service] -->

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
