---
title: Adicionar um provedor de identidade-Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar um provedor de identidade ao seu locatário Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 01/14/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 1665f8f595e2bb9ba2a5f2c8528f85854630ab4f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216573"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um provedor de identidade ao seu locatário Azure Active Directory B2C

Você pode configurar o Azure AD B2C para permitir que os usuários entrem em seu aplicativo com credenciais de IdP (provedores de identidade) social ou empresarial externos. O Azure AD B2C dá suporte a provedores de identidade externos, como Facebook, conta Microsoft, Google, Twitter e qualquer provedor de identidade que dê suporte aos protocolos OAuth 1.0, OAuth 2.0, OpenID Connect e SAML.

Com a federação do provedor de identidade externa, você pode oferecer aos consumidores a capacidade de entrar com suas contas sociais ou empresariais existentes, sem precisar criar uma conta apenas para seu aplicativo.

Na página de inscrição ou de entrada, o Azure AD B2C apresenta uma lista de provedores de identidade externos que o usuário pode escolher para entrar. Depois de selecionar um dos provedores de identidade externos, o usuário é redirecionado para o site do provedor selecionado para concluir o processo de entrada. Depois de entrar com êxito, o usuário será levado de volta para o Azure AD B2C para autenticação da conta em seu aplicativo.

![Exemplo de entrada móvel com uma conta social (Facebook)](media/add-identity-provider/external-idp.png)

Você pode adicionar provedores de identidade compatíveis com o Azure AD B2C (Azure Active Directory B2C) a seus [fluxos dos usuários](user-flow-overview.md) usando o portal do Azure. Você também pode adicionar provedores de identidade às suas [políticas personalizadas](custom-policy-get-started.md).

## <a name="select-an-identity-provider"></a>Selecionar um provedor de identidade

Você normalmente usa apena um provedor de identidade em seus aplicativos, mas tem a opção de adicionar mais. Os artigos de instruções abaixo mostram como criar o aplicativo de provedor de identidade, adicionar o provedor de identidade ao seu locatário e adicionar o provedor de identidade ao seu fluxo de usuário ou a uma política personalizada.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Azure AD (único locatário)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (multilocatário)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [Facebook](identity-provider-facebook.md)
* [Provedor de identidade genérico](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Conta da Microsoft](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (protocolo SAML)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
