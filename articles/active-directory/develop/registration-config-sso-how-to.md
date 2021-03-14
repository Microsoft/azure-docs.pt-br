---
title: Configurar o logon único do aplicativo
description: Como configurar o logon único para um aplicativo personalizado que você está desenvolvendo e registrar com o Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 60a07145e38c19f577ad9864efb599e7b244aa83
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653704"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Como configurar o logon único para um aplicativo

Habilitar logon único federado (SSO) em seu aplicativo é habilitado automaticamente por meio do Azure AD para OpenID Connect, SAML 2.0 ou WS-Fed. Se os usuários finais estão precisando entrar, mesmo já tendo uma sessão existente com o Azure AD, é provável que seu aplicativo possa estar configurado incorretamente.

* Se você estiver usando o ADAL/MSAL, verifique se você tem o **PromptBehavior** definido como **Automático** em vez de **Sempre**.

* Se você estiver criando um aplicativo móvel, talvez seja necessário configurações adicionais para habilitar SSO orientado ou não orientado.

Para o Android, consulte [Habilitando SSO entre aplicativos no Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

Para o iOS, consulte [Habilitando SSO entre aplicativos no iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Próximas etapas

[SSO do Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Habilitando SSO entre aplicativos no Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Habilitando SSO entre aplicativos no iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integrando aplicativos ao AzureAD](./quickstart-register-app.md)<br>

[Permissões e consentimento na plataforma de identidade da Microsoft](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)