---
title: Como configurar a política de risco de entrada no Azure Active Directory Identity Protection| Microsoft Docs
description: Saiba como configurar a política de risco de entrada no Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0645e01c8ad9c620b77abd9af6cf7fe7c26ab4ea
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335415"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Como: Configurar a política de risco de entrada

O Azure Active Directory detecta [tipos de evento de risco](../reports-monitoring/concept-risk-events.md#risk-event-types) em tempo real e offline. Cada evento de risco que tiver sido detectado para a entrada de um usuário contribui para um conceito lógico chamado entrada de risco. Uma entrada de risco é um indicador de uma tentativa de logon que pode não ter sido realizada pelo proprietário legítimo de uma conta de usuário.

## <a name="what-is-the-sign-in-risk-policy"></a>O que é a política de risco de entrada?

O Microsoft Azure AD analisa cada entrada de um usuário. O objetivo da análise é detectar ações suspeitas que ocorrem junto com a entrada. Por exemplo, a entrada é feita usando um endereço IP anônimo ou é iniciada de um local desconhecido? No Azure AD, as ações suspeitas que o sistema pode detectar também são conhecidas como eventos de risco. Com base nos eventos de risco que foram detectados durante a entrada, o Azure AD calcula um valor. O valor representa a probabilidade (baixa, média, alta) de que a entrada não seja executada pelo usuário legítimo. A probabilidade é chamada de **nível de risco de entrada**.

A política de risco de entrada é uma resposta automatizada que pode ser configurada para um nível de risco de entrada específico. Na resposta, você pode bloquear o acesso aos seus recursos ou exigir a transmissão de um desafio de MFA (autenticação multifator) para obter acesso.
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Como acessar a política de risco de entrada?
   
A política de risco de entrada encontra-se na seção **Configurar** na [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de entrada](./media/howto-sign-in-risk-policy/1014.png "Política de risco de entrada")

## <a name="policy-settings"></a>Configurações de política

Ao configurar a política de risco de entrada, será necessário definir:

- Os usuários e grupos aos quais a política se aplica:

    ![Usuários e grupos](./media/howto-sign-in-risk-policy/11.png)

- O nível de risco de entrada que dispara a política:

    ![Nível de risco da conexão](./media/howto-sign-in-risk-policy/12.png)

- O tipo de acesso que deverá ser imposto quando o nível de risco de entrada for alcançado:  

    ![Acessar](./media/howto-sign-in-risk-policy/13.png)

- O estado da sua política:

    ![Impor a política](./media/howto-sign-in-risk-policy/14.png)

A caixa de diálogo de configuração de política fornece uma opção para estimar o impacto da reconfiguração.

![Impacto estimado](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>O que você deve saber

Você pode configurar uma política de segurança de risco de entrada para exigir a MFA:

![Exigir MFA](./media/howto-sign-in-risk-policy/16.png)

No entanto, por motivos de segurança, essa configuração só funciona para usuários que já foram registrados na MFA. A proteção de identidade bloqueia usuários com requisito de MFA quando eles ainda não estão registrados para MFA.

Se quiser exigir MFA para entradas de risco, você deverá:

1. Habilite a [política de registro de autenticação multifator](howto-mfa-policy.md) para os usuários afetados.
2. Exija que os usuários afetados façam logon em uma sessão sem risco para realizar um registro com MFA.

A conclusão dessas etapas faz com que a autenticação multifator seja exigida em caso de entrada de risco.

A política de risco de entrada:

- é aplicada a todo o tráfego do navegador e entradas que usam autenticação moderna.
- Não é aplicada a aplicativos que usam protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust no IDP federado, como o ADFS.

Para obter uma visão geral da experiência do usuário relacionada, confira:

* [Recuperação de entrada arriscada](flows.md#risky-sign-in-recovery)
* [Entrada arriscada bloqueada](flows.md#risky-sign-in-blocked)  
* [Experiências de entrada com o Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Práticas recomendadas

Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.  

No entanto, isso exclui entradas sinalizadas com **Baixo** e **Médio** risco da política, o que pode não impedir que um invasor explore uma identidade comprometida.

Ao definir a política,

- exclua os usuários que não tem / não podem ter a autenticação multifator
- exclua os usuários em localidades em que não é viável habilitar a política (por exemplo, sem acesso à assistência técnica)
- exclua usuários que tendem a gerar muitos falsos positivos (desenvolvedores e analistas de segurança)
- use um limite **Alto** durante a distribuição inicial de política ou se você precisar minimizar os desafios encontrados pelos usuários finais.
- Use um limite **Baixo** se sua organização exigir uma segurança maior. Selecionar um limite **Baixo** apresenta desafios de entrada do usuário adicionais, porém representa uma segurança maior.

O padrão recomendado na maioria das organizações é configurar uma regra para um limite **Médio** para atingir um equilíbrio entre segurança e usabilidade.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview.md).
