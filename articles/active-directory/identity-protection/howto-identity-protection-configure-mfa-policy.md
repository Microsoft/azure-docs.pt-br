---
title: Configurar a política de registro de MFA-Azure Active Directory Identity Protection
description: Saiba como configurar a política de registro de autenticação multifator do Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835859"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Como configurar a política de registro da autenticação multifator do Azure AD

Azure AD Identity Protection ajuda a gerenciar a distribuição do registro da autenticação multifator (MFA) do Azure AD Configurando uma política de acesso condicional para exigir o registro de MFA, independentemente do aplicativo de autenticação moderno no qual você está entrando.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>O que é a política de registro da autenticação multifator do Azure AD?

A autenticação multifator do Azure AD fornece um meio para verificar quem você está usando mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para entradas de usuário. Para que os usuários possam responder aos prompts do MFA, eles devem primeiro se registrar para a autenticação multifator do Azure AD.

Recomendamos que você exija a autenticação multifator do Azure AD para entradas de usuário porque:

- Fornece autenticação forte por meio de uma variedade de opções de verificação.
- Desempenha um papel fundamental na preparação de sua organização para a remediação automática de detecções de risco no Identity Protection.

Para obter mais informações sobre a autenticação multifator do Azure AD, consulte [o que é a autenticação multifator do Azure ad?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configuração de política

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >  **Security**  >  política de registro do MFA da **proteção de identidade** de segurança  >  **MFA registration policy**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
   1. Em **controles**
      1. Verifique se a caixa de seleção requer que o **registro do Azure ad MFA** esteja marcado e escolha **selecionar**.
   1. **Impor política**  -  **Em**
   1. **Salvar**

## <a name="user-experience"></a>Experiência do usuário

Azure Active Directory Identity Protection solicitará que os usuários se registrem na próxima vez que entrarem de forma interativa e terão 14 dias para concluir o registro. Durante esse período de 14 dias, eles podem ignorar o registro, mas no final do período eles deverão ser registrados para que possam concluir o processo de entrada.

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Experiências de entrada com o Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Próximas etapas

- [Habilitar as políticas de risco com usuários e entradas](howto-identity-protection-configure-risk-policies.md)

- [Habilitar a redefinição de senha self-service do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a autenticação multifator do Azure AD](../authentication/howto-mfa-getstarted.md)
