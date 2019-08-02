---
title: Como configurar a política de registro de autenticação multifator no Azure Active Directory Identity Protection| Microsoft Docs
description: Saiba como configurar a política de registro de autenticação multifator do Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ff7512bb3f9422ed4c8edd7ab50fce23f0ed07
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499556"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Como: Configurar a política de registro da autenticação multifator do Azure

Azure AD Identity Protection ajuda a gerenciar a distribuição do registro de autenticação multifator (MFA) configurando uma política de acesso condicional para exigir o registro de MFA, independentemente do aplicativo de autenticação moderno no qual você está entrando. Este artigo explica o que a política pode ser usada e como configurá-la.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>O que é a política de registro da autenticação multifator do Azure?

A autenticação multifator do Azure fornece um meio de verificar quem você está usando mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para entradas de usuário. Para que os usuários possam responder aos prompts do MFA, eles devem primeiro se registrar para a autenticação multifator do Azure.

Recomendamos que você exija a autenticação multifator do Azure para entradas de usuário porque:

- fornece autenticação forte com uma variedade de opções de verificação fácil
- Desempenha um papel fundamental na preparação de sua organização para proteger e recuperar-se de eventos de risco no Identity Protection

Para obter mais detalhes sobre o MFA, consulte [o que é a autenticação multifator do Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Como fazer acessar a política de registro?

A política de risco de MFA encontra-se na seção **Configurar** na página do [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Política do MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Configurações de política

Ao configurar a política de registro de MFA, você precisa fazer as seguintes alterações de configuração:

- Os usuários e grupos aos quais a política se aplica. Lembre-se de excluir as [contas de acesso de emergência](../users-groups-roles/directory-emergency-access.md)da sua organização.

    ![Usuários e grupos](./media/howto-mfa-policy/11.png)

- O controle que você deseja impor – **exigir registro do Azure MFA**

    ![Acessar](./media/howto-mfa-policy/12.png)

- Impor política deve ser definido como **ativado**.

    ![Impor a política](./media/howto-mfa-policy/14.png)

- **Salvar** sua política

## <a name="user-experience"></a>Experiência de usuário

Azure Active Directory Identity Protection solicitará que os usuários se registrem na próxima vez que entrarem de forma interativa.

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Fluxo do registro de autenticação multifator](flows.md#multi-factor-authentication-registration).  
- [Experiências de entrada com o Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview.md).
