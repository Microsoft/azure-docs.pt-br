---
title: Políticas de acesso condicional comum-Azure Active Directory
description: Políticas de acesso condicional comumente usadas para organizações
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdaaed72b85bc3c0196a8897be311efa5fb714e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366336"
---
# <a name="common-conditional-access-policies"></a>Políticas de Acesso Condicional Comum

[Os padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) são ótimos para algumas, mas muitas organizações precisam de mais flexibilidade do que oferecem. Por exemplo, muitas organizações precisam da capacidade de excluir contas específicas como suas contas de acesso de emergência ou de administração de interrupções de políticas de acesso condicional que exigem autenticação multifator. Para essas organizações, as políticas comuns referenciadas neste artigo podem ser usadas.

![Políticas de acesso condicional no portal do Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Contas de acesso de emergência

Mais informações sobre contas de acesso de emergência e por que elas são importantes podem ser encontradas nos seguintes artigos: 

* [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../roles/security-emergency-access.md)
* [Criar uma estratégia de gerenciamento de controle de acesso resiliente com o Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Políticas típicas implantadas por organizações

* [Bloquear autenticação herdada](howto-conditional-access-policy-block-legacy.md)\*
* [Exigir MFA para administradores](howto-conditional-access-policy-admin-mfa.md)\*
* [Exigir MFA para gerenciamento do Azure](howto-conditional-access-policy-azure-management.md)\*
* [Exigir MFA para todos os usuários](howto-conditional-access-policy-all-users-mfa.md)\*

\* Essas quatro políticas quando configuradas juntas, imitam a funcionalidade habilitada por [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Políticas adicionais

* [Acesso condicional com base em risco de entrada (requer Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Acesso condicional baseado em risco do usuário (requer Azure AD Premium P2)](howto-conditional-access-policy-risk-user.md)
* [Exigir local confiável para registro de MFA](howto-conditional-access-policy-registration.md)
* [Bloquear o acesso por local](howto-conditional-access-policy-location.md)
* [Exigir dispositivo em conformidade](howto-conditional-access-policy-compliant-device.md)
* [Bloquear o acesso, exceto aplicativos específicos](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Próximas etapas

- [Simule o comportamento de entrada usando a ferramenta de What If de acesso condicional.](troubleshoot-conditional-access-what-if.md)

- [Use o modo somente de relatório para acesso condicional para determinar o impacto das novas decisões de política.](concept-conditional-access-report-only.md)
