---
title: "Como adicionar um aplicativo multilocatário à galeria de aplicativos do Azure AD | Microsoft Docs"
description: "Explica como listar seu aplicativo multilocatário personalizado na Galeria de Aplicativos do Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 208f0d40bd7a8e8f35f16e1fcb09c305d833dbb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Como adicionar um aplicativo multilocatário à Galeria de Aplicativos do Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de Aplicativos do Azure AD?

A Galeria de Aplicativos do Azure AD é uma ótima maneira de colocar seu aplicativo diante de milhões de clientes do Azure Active Directory para ampliar o impacto e o alcance do aplicativo no mercado. As etapas a seguir explicam como listar seu aplicativo na Galeria de Aplicativos do Azure AD.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se seu aplicativo der suporte a SAML ou OpenIDConnect
Se tiver um aplicativo multilocatário que queira listar na Galeria de Aplicativos do Azure AD, você precisa primeiro assegurar que seu aplicativo dê suporte a uma das seguintes tecnologias de logon único:

1. **OpenID Connect** : integração direta com o Azure AD usando o OpenID Connect para autenticação e a API de consentimento do AD do Azure para configuração. Se você estiver apenas começando uma integração e seu aplicativo não der suporte a SAML, esse é o modo recomendado.
2. **SAML** : seu aplicativo já tem a capacidade de configurar provedores de identidade de terceiros usando o protocolo SAML.

Se seu aplicativo der suporte a um desses modos de logon único e você quiser listar seu aplicativo multilocatário na Galeria de Aplicativos do Azure AD, siga as etapas no documento abaixo. Para iniciar rapidamente, envie um email para **waadpartners@microsoft.com**.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se seu aplicativo não der suporte a SAML ou OpenIDConnect
Mesmo que seu aplicativo não dê suporte a um desses modos, ainda assim podemos integrá-lo a nossa galeria usando nossa tecnologia de Logon único com senha. Para explorar essa opção, envie um email para **waadpartners@microsoft.com**.

## <a name="next-steps"></a>Próximas etapas
[Como listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
