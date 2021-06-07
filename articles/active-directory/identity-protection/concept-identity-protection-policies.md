---
title: Políticas do Azure AD Identity Protection
description: Identificação das três políticas que são habilitadas com o Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835961"
---
# <a name="identity-protection-policies"></a>Políticas do Identity Protection

O Azure Active Directory Identity Protection inclui três políticas padrão que os administradores podem habilitar. A personalização dessas políticas é limitada, mas são aplicáveis à maioria das organizações. Todas as políticas permitem a exclusão de usuários, como suas [contas de administrador de interrupção ou de acesso de emergência](../roles/security-emergency-access.md).

![Políticas do Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Política de registro de MFA do Azure AD

A proteção de identidade pode ajudar as organizações a distribuir a MFA (autenticação multifator) do Azure AD usando uma política de acesso condicional que requer registro na entrada. Habilitar essa política é uma ótima maneira de garantir que novos usuários da organização tenham se registrado para MFA no primeiro dia. A autenticação multifator é um dos métodos de autocorreção para eventos de risco no Identity Protection. A autocorreção permite que os usuários tomem providências por conta própria para reduzir o volume de chamadas para assistência técnica.

Mais informações sobre a autenticação multifator do Azure AD podem ser encontradas no artigo [como funciona: autenticação multifator do Azure ad](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Política de risco de entrada

O Identity Protection analisa os sinais de cada entrada, tanto em tempo real quanto offline, e calcula uma pontuação de risco com base na probabilidade de que a entrada não tenha sido realizada pelo usuário. Os administradores podem tomar uma decisão com base nesse sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigir a autenticação multifator.

Se for detectado um risco, os usuários podem executar a autenticação multifator para autocorrigir e fechar a entrada suspeita para evitar passar problemas desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter registrado anteriormente para a autenticação multifator do Azure AD antes de disparar a política de risco de entrada.

### <a name="custom-conditional-access-policy"></a>Política de Acesso Condicional personalizada

Os administradores também podem optar por criar uma política de Acesso Condicional personalizada, incluindo o risco de entrada como uma condição de atribuição. Mais informações sobre o risco como uma condição em uma política de Acesso Condicional podem ser encontradas no artigo [Acesso Condicional: condições](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Política de risco de entrada de Acesso Condicional personalizada](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Política de risco do usuário

O Identity Protection pode calcular o que acredita ser normal para o comportamento de um usuário e usar isso como base para decidir aquilo que representa um risco. O risco do usuário é um cálculo da probabilidade de que uma identidade tenha sido comprometida. Os administradores podem tomar uma decisão com base nesse sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigir uma alteração de senha usando a [redefinição de senha self-service do Azure AD](../authentication/howto-sspr-deployment.md).

Se for detectado um risco, os usuários podem realizar a redefinição de senha self-service e fechar o evento de risco do usuário para evitar passar problemas desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter se registrado previamente para redefinição de senha self-service antes de disparar a política de risco do usuário.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar a redefinição de senha self-service do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a Autenticação Multifator do Azure AD](../authentication/howto-mfa-getstarted.md)

- [Habilitar a política de registro da autenticação multifator do Azure AD](howto-identity-protection-configure-mfa-policy.md)

- [Habilitar as políticas de risco com usuários e entradas](howto-identity-protection-configure-risk-policies.md)
