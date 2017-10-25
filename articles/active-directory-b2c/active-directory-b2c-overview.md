---
title: "Visão geral - Azure AD B2C | Microsoft Docs"
description: Desenvolvendo aplicativos voltados para o consumidor com o Active Directory B2C do Azure
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeedakhter-msft
ms.openlocfilehash: 44d5d31d49c375c802a67511d1f962df20656559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C: concentre-se em seu aplicativo, deixe que nos preocupemos com a inscrição e a conexão

O Azure AD B2C é uma solução de gerenciamento de identidade na nuvem para os seus aplicativos móveis e Web. É um serviço global altamente disponível que se adapta a centenas de milhões de identidades. Compilado em uma plataforma segura de grau empresarial, o Azure AD B2C mantém seus aplicativos, seu negócio e seus consumidores protegidos.

Com a configuração mínima, Azure AD B2C permite que seu aplicativo autentique:

* **Contas Sociais** (como Google, LinkedIn, Facebook e muito mais)
* **Contas Corporativas** (usando protocolos de padrão aberto, SAML ou OpenID Connect)
* **Contas Locais** (endereço de email e senha ou nome de usuário e senha)

## <a name="get-started"></a>Introdução

Primeiro, obtenha seu próprio locatário usando as etapas descritas em [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md).

Em seguida, escolha seu cenário de desenvolvimento de aplicativo:

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplicativos móveis e de desktop](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />Aplicativos móveis e de desktop</center> | [Visão geral](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Aplicativos Web](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Aplicativos Web</center> | [Visão geral](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Aplicativos de Página Única](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Aplicativos de Página Única</center> | [Visão geral](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![APIs da Web](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />APIs da Web</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [Chamar uma API Web do .NET](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>Novidades

Volte aqui frequentemente para saber mais sobre as alterações que virão no Azure Active Directory B2C. Também tweetamos sobre todas as atualizações usando @AzureAD.

* Além de "Políticas internas" (disponibilidade geral), o recurso ["Políticas personalizadas"](active-directory-b2c-overview-custom.md) agora está disponível em visualização pública.  Políticas personalizadas são para os profissionais de identidade que precisam de controle sobre a composição das respectivas experiências de identidade.
* O recurso [Token de Acesso](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview) agora está disponível na visualização pública.
* A [Disponibilidade Geral de diretórios do Azure AD B2C com base na Europa](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/) foi anunciada.
* Confira nossa biblioteca crescente de [exemplos de código no GitHub](https://github.com/Azure-Samples?q=b2c)!

## <a name="how-to-articles"></a>Artigos de instruções

Aprenda a usar os recursos específicos do Azure Active Directory B2C:

* Configure contas do [Facebook](active-directory-b2c-setup-fb-app.md), do [Google+](active-directory-b2c-setup-goog-app.md), da [Microsoft](active-directory-b2c-setup-msa-app.md), da [Amazon](active-directory-b2c-setup-amzn-app.md) e do [LinkedIn](active-directory-b2c-setup-li-app.md) para usá-las em seus aplicativos voltados para o consumidor.
* [Usar atributos personalizados para coletar informações sobre seus consumidores](active-directory-b2c-reference-custom-attr.md).
* [Habilite o Azure Multi-Factor Authentication em seus aplicativos voltados para o consumidor](active-directory-b2c-reference-mfa.md).
* [Configure a redefinição de senha de autoatendimento para seus consumidores](active-directory-b2c-reference-sspr.md).
* [Personalize a aparência das páginas de inscrição, de entrada e outras páginas voltadas ao consumidor](active-directory-b2c-reference-ui-customization.md) fornecidas pelo Azure Active Directory B2C.
* [Use a API do Graph do Azure Active Directory para criar, ler, atualizar e excluir programaticamente os consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) em seu locatário do Azure Active Directory B2C.

## <a name="next-steps"></a>Próximas etapas

Estes links são úteis na exploração aprofundada do serviço:

* Veja as [informações sobre preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Examinar nosso [amostras de código](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) para o Azure Active Directory B2C. 
* Obtenha ajuda no Stack Overflow usando a marca [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Envie-nos seus comentários usando [Voz do Usuário](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)– queremos recebê-los!
* Examine a [Referência de protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Examine a [Referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Leia as [Perguntas frequentes do Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Solicitações de suporte a arquivos para o Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha notificações sobre a ocorrência de incidentes de segurança visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.

