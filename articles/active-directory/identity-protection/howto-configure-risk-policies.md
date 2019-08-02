---
title: Como configurar políticas de risco no Azure Active Directory Identity Protection (atualizado) | Microsoft Docs
description: Como configurar políticas de risco no Azure Active Directory Identity Protection (atualizado).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce4e2958978de9339f4340755e3740730025a5f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334035"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Como: Configurar políticas de risco no Azure Active Directory Identity Protection (atualizado)

O Azure AD detecta eventos de risco que são indicadores para identidades potencialmente comprometidas. Configurando políticas de risco, você pode definir as respostas automatizadas para os resultados da detecção:

- Com a política de risco de entrada, você pode configurar uma resposta para eventos de risco em tempo real que foram detectados durante a entrada do usuário. 
- Com a política de risco do usuário, você pode configurar uma resposta para todos os riscos de usuário ativos que foram detectados para um usuário ao longo do tempo.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>O que é a política de risco de entrada?

O Microsoft Azure AD analisa cada entrada de um usuário. O objetivo da análise é detectar ações suspeitas que ocorrem junto com a entrada. Por exemplo, a entrada é feita usando um endereço IP anônimo ou é iniciada de um local desconhecido? No Azure AD, as ações suspeitas que o sistema pode detectar também são conhecidas como eventos de risco. Com base nos eventos de risco que foram detectados durante a entrada, o Azure AD calcula um valor. O valor representa a probabilidade (baixa, média, alta) de que a entrada não seja executada pelo usuário legítimo. A probabilidade é chamada de **nível de risco de entrada**.

A política de risco de entrada é uma resposta automatizada que pode ser configurada para um nível de risco de entrada específico. Na resposta, você pode bloquear o acesso aos seus recursos ou exigir a transmissão de um desafio de MFA (autenticação multifator) para obter acesso.

Quando um usuário conclui com êxito um prompt do MFA disparado pela política de risco de entrada, ele fornece comentários para a proteção de identidade que a entrada originou do usuário legítimo. Assim, o evento de risco de entrada que disparou o prompt do MFA será fechado automaticamente e a proteção de identidade impedirá que esse evento contribua para a elevação do risco do usuário. A habilitação da política de risco de entrada pode reduzir o noisiness na exibição de entradas arriscadas, permitindo que os usuários sejam corrigidos automaticamente quando solicitados a MFA e, subsequentemente, fechando a entrada arriscada associada.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Como acessar a política de risco de entrada?
   
A política de risco de entrada encontra-se na seção **Configurar** na [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de entrada](./media/howto-configure-risk-policies/1014.png "Política de risco de entrada")

## <a name="sign-in-risk-policy-settings"></a>Configurações de política de risco de entrada

Ao configurar a política de risco de entrada, será necessário definir:

- Os usuários e grupos aos quais a política se aplica:

   ![Usuários e grupos](./media/howto-configure-risk-policies/11.png)

- O nível de risco de entrada que dispara a política:

   ![Nível de risco da conexão](./media/howto-configure-risk-policies/12.png)

- O tipo de acesso que deverá ser imposto quando o nível de risco de entrada for alcançado:  

   ![Acessar](./media/howto-configure-risk-policies/13.png)

- O estado da sua política:

   ![Impor a política](./media/howto-configure-risk-policies/14.png)

A caixa de diálogo de configuração de política fornece uma opção para estimar o impacto da reconfiguração.

![Impacto estimado](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>O que você deve saber sobre as políticas de risco de entrada

Você pode configurar uma política de segurança de risco de entrada para exigir a MFA:

![Exigir MFA](./media/howto-configure-risk-policies/16.png)

No entanto, por motivos de segurança, essa configuração só funciona para usuários que já foram registrados na MFA. A proteção de identidade bloqueia usuários com requisito de MFA quando eles ainda não estão registrados para MFA.

Se quiser exigir MFA para entradas de risco, você deverá:

1. Habilite a política de registro de autenticação multifator para os usuários afetados.
2. Exija que os usuários afetados façam logon em uma sessão sem risco para realizar um registro de MFA.

A conclusão dessas etapas faz com que a autenticação multifator seja exigida em caso de entrada de risco.

A política de risco de entrada:

- é aplicada a todo o tráfego do navegador e entradas que usam autenticação moderna.
- Não é aplicada a aplicativos que usam protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust no IDP federado, como o ADFS.

Para obter uma visão geral da experiência do usuário relacionada, confira:

* [Recuperação de entrada arriscada](flows.md#risky-sign-in-recovery)
* [Entrada arriscada bloqueada](flows.md#risky-sign-in-blocked)  
* [Experiências de entrada com o Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>O que é uma política de risco do usuário?

O Microsoft Azure AD analisa cada entrada de um usuário. O objetivo da análise é detectar ações suspeitas que ocorrem junto com a entrada. No Microsoft Azure AD, as ações suspeitas que o sistema pode detectar também são conhecidas como eventos de risco. Embora alguns eventos de risco possam podem ser detectados em tempo real, há também aqueles que exigem mais tempo. Por exemplo, para detectar uma viagem impossível a locais atípicos, o sistema requer um período inicial de aprendizado de 14 dias para conhecer melhor o comportamento normal de um usuário. Há várias opções para resolver os eventos de risco detectados. Por exemplo, você pode resolver manualmente os eventos de risco individuais ou pode obtê-los resolvidos usando um risco de entrada ou uma política de acesso condicional de risco do usuário.

Todos os eventos de risco que foram detectados para um usuário e não são resolvidos são conhecidos como eventos de risco ativos. Os eventos de risco ativos que estão associados a um usuário são conhecidos como risco do usuário. Com base no risco do usuário, o Microsoft Azure AD calcula uma probabilidade (baixa, média, alta) de um usuário ter sido comprometido. A probabilidade é chamada de nível de risco do usuário.

![Riscos do usuário](./media/howto-configure-risk-policies/11031.png)

A política de risco do usuário é uma resposta automatizada que pode ser configurada para um nível de risco do usuário específico. Com uma política de risco do usuário, é possível bloquear o acesso aos recursos ou exigir uma alteração de senha para retornar uma conta de usuário para um estado inicial.

## <a name="how-do-i-access-the-user-risk-policy"></a>Como fazer para acessar a política de risco do usuário?
   
A política de risco do usuário encontra-se na seção **Configurar** na [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco do usuário](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Configurações de política de risco de usuário

Ao configurar a política de risco de usuário, será necessário definir:

- Os usuários e grupos aos quais a política se aplica:

   ![Usuários e grupos](./media/howto-configure-risk-policies/111.png)

- O nível de risco de entrada que dispara a política:

   ![Nível de risco do usuário](./media/howto-configure-risk-policies/112.png)

- O tipo de acesso que deverá ser imposto quando o nível de risco de entrada for alcançado:  

   ![Acessar](./media/howto-configure-risk-policies/113.png)

- O estado da sua política:

   ![Impor a política](./media/howto-configure-risk-policies/114.png)

A caixa de diálogo de configuração de política fornece uma opção para estimar o impacto da sua configuração.

![Impacto estimado](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>O que você deve saber sobre as políticas de risco de usuário

É possível definir uma política de segurança de risco do usuário para bloquear os usuários ao entrar dependendo do nível do risco.

![Bloqueando](./media/howto-configure-risk-policies/116.png)

Bloquear a entrada:

* impede a geração de novos eventos de risco do usuário para o usuário afetado
* Permite aos administradores corrigir os eventos de risco que afetam a identidade do usuário manualmente, bem como restaurá-la para um estado seguro

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

 [Canal 9: Azure AD e Identity Show: Versão Prévia do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
