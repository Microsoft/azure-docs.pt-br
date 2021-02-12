---
title: Usando o Azure AD para aplicativos não listados na Galeria de aplicativos
description: Entenda como integrar aplicativos não listados na galeria do Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 9721679938517e38f669f78ee0f5f9f3a80e05a7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258262"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Usando o Azure AD para aplicativos não listados na Galeria de aplicativos

No início rápido [Adicionar um aplicativo](add-application-portal.md) , você aprende a adicionar um aplicativo ao seu locatário do Azure AD.

Além das opções da [Galeria de aplicativos do Azure AD](../saas-apps/tutorial-list.md), você pode adicionar um **aplicativo inexistente na galeria**. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Recursos para aplicativos não listados na galeria do Azure AD

É possível adicionar qualquer aplicativo que já exista em sua organização ou qualquer aplicativo de terceiros, de um fornecedor que ainda não faça parte da galeria do Azure AD. Dependendo do seu [contrato de licença](https://azure.microsoft.com/pricing/details/active-directory/), os seguintes recursos estão disponíveis:

- Integração de autoatendimento de qualquer aplicativo compatível com provedores de identidade [SAML (Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) (iniciado por SP ou IdP)
- Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](sso-options.md#password-based-sso)
- Conexão de autoatendimento para aplicativos que usam o [protocolo SCIM (Sistema de Gerenciamento de Usuários entre Domínios) para provisionamento de usuário](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links a qualquer aplicativo no [iniciador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou [meus aplicativos](sso-options.md#linked-sign-on)

Se você estiver procurando orientação para desenvolvedores sobre como integrar aplicativos personalizados ao Azure AD, consulte [cenários de autenticação do Azure ad](../develop/authentication-vs-authorization.md). Ao desenvolver um aplicativo que usa um protocolo moderno como [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar usuários, você pode registrá-lo na plataforma de identidade da Microsoft usando a experiência de [Registros de aplicativo](../develop/quickstart-register-app.md) no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Série de início rápido no gerenciamento de aplicativos](view-applications-portal.md)