---
title: Adicionar aplicativo multilocatário à galeria de aplicativos do Azure AD
description: Explica como é possível listar o aplicativo multilocatário personalizado na galeria de aplicativos do Azure Active Directory.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ROBOTS: NOINDEX
ms.openlocfilehash: de0231a49c4f806660ea0cb305fdc1a70e2b36d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063120"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Adicionar um aplicativo multilocatário à galeria de aplicativos do Azure Active Directory

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicativos do Azure Active Directory?

O Azure Active Directory é um serviço de identidade baseado em nuvem. A [galeria de aplicativos do Azure Active Directory](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) está na loja de aplicativos do Azure Marketplace, onde todos os conectores de aplicativos são publicados para logon único e provisionamento de usuário. Os clientes que usam o Azure Active Directory como provedor de identidade localizam diferentes conectores de aplicativos SaaS publicados aqui. Os administradores de TI adicionam conectores da galeria de aplicativos e, em seguida, configuram e usam os conectores para logon único e provisionamento. O Azure Active Directory fornece suporte a todos os principais protocolos de federação, incluindo SAML 2.0, OpenID Connect, OAuth e WS-Fed para logon único. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se seu aplicativo der suporte a SAML ou OpenIDConnect
Se você tiver um aplicativo multilocatário que deseja listar na galeria de aplicativos do Azure Active Directory, será necessário primeiro certificar-se de que o aplicativo fornece suporte a uma das tecnologias de logon único seguir:

- **OpenID Connect**: Para que o aplicativo seja listado, crie o aplicativo multilocatário no Azure AD e implemente a [estrutura de consentimento do Azure AD](./consent-framework.md) para o aplicativo. Envie a solicitação de logon para um ponto de extremidade comum para que qualquer cliente possa dar consentimento ao aplicativo. É possível pode controlar o acesso de um usuário com base na ID do locatário e o UPN do usuário recebido no token. Envie o aplicativo usando o processo descrito em [Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](./v2-howto-app-gallery-listing.md).

- **SAML**: Se o aplicativo der suporte ao SAML 2.0, ele poderá ser listado na galeria. Siga as instruções em [Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](./v2-howto-app-gallery-listing.md).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se seu aplicativo não der suporte a SAML ou OpenIDConnect
Aplicativos que não têm suporte para SAML ou OpenIDConnect poderão ainda ser integrados na galeria de aplicativos por meio de tecnologia de logon único de senha.

O logon único de senha, também chamado de cofre de senha, permite que você gerencie o acesso e senhas de usuários a aplicativos Web que não têm suporte para federação de identidade. Também é útil para cenários, nos quais vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização. 

Se você quiser listar o aplicativo com essa tecnologia:
1. Crie um aplicativo Web que tenha uma página de logon HTML para configurar o [logon único de senha](../manage-apps/what-is-single-sign-on.md). 
2. Envie a solicitação, conforme descrito em [Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](./v2-howto-app-gallery-listing.md).

## <a name="escalations"></a>Escalonamentos

Para qualquer escalonamentos, envie um email para a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e retornaremos o mais rápido possível.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [listar seu aplicativo na galeria de aplicativos do Azure Active Directory](./v2-howto-app-gallery-listing.md).