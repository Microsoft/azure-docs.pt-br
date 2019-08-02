---
title: Adicionar um aplicativo multilocatário à galeria de aplicativos do Azure Active Directory | Microsoft Docs
description: Explica como é possível listar o aplicativo multilocatário personalizado na galeria de aplicativos do Azure Active Directory.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8024130b986d111abe3b470d6b06e86ed6419dc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320928"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Adicionar um aplicativo multilocatário à galeria de aplicativos do Azure Active Directory

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicativos do Azure Active Directory?

O Azure Active Directory é um serviço de identidade baseado em nuvem. A [galeria de aplicativos do Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) está na loja de aplicativos do Azure Marketplace, onde todos os conectores de aplicativos são publicados para logon único e provisionamento de usuário. Os clientes que usam o Azure Active Directory como provedor de identidade localizam diferentes conectores de aplicativos SaaS publicados aqui. Os administradores de TI adicionam conectores da galeria de aplicativos e, em seguida, configuram e usam os conectores para logon único e provisionamento. O Azure Active Directory fornece suporte a todos os principais protocolos de federação, incluindo SAML 2.0, OpenID Connect, OAuth e WS-Fed para logon único. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se seu aplicativo der suporte a SAML ou OpenIDConnect
Se você tiver um aplicativo multilocatário que deseja listar na galeria de aplicativos do Azure Active Directory, será necessário primeiro certificar-se de que o aplicativo fornece suporte a uma das tecnologias de logon único seguir:

- **OpenID Connect**: Para que o aplicativo seja listado, crie o aplicativo multilocatário no Azure AD e implemente a [estrutura de consentimento do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) para o aplicativo. Envie a solicitação de logon para um ponto de extremidade comum para que qualquer cliente possa dar consentimento ao aplicativo. É possível pode controlar o acesso de um usuário com base na ID do locatário e o UPN do usuário recebido no token. Envie o aplicativo usando o processo descrito em [Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Se o aplicativo der suporte ao SAML 2.0, ele poderá ser listado na galeria. Siga as instruções em [Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se seu aplicativo não der suporte a SAML ou OpenIDConnect
Aplicativos que não têm suporte para SAML ou OpenIDConnect poderão ainda ser integrados na galeria de aplicativos por meio de tecnologia de logon único de senha.

O logon único de senha, também chamado de cofre de senha, permite que você gerencie o acesso e senhas de usuários a aplicativos Web que não têm suporte para federação de identidade. Também é útil para cenários, nos quais vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização. 

Se você quiser listar o aplicativo com essa tecnologia:
1. Crie um aplicativo Web que tenha uma página de logon HTML para configurar o [logon único de senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Envie a solicitação, conforme descrito em [Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Escalonamentos

Para qualquer escalonamentos, envie um email para a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e retornaremos o mais rápido possível.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
