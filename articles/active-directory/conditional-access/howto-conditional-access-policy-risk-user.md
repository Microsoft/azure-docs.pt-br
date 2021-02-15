---
title: Acesso condicional baseado em risco do usuário-Azure Active Directory
description: Criar políticas de acesso condicional usando o risco de usuário da proteção de identidade
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5423a61c8febee72f32935f3dee4e9f9e868630
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049070"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Acesso condicional: acesso condicional baseado em risco do usuário

A Microsoft trabalha com pesquisadores, imposição de autoridades, várias equipes de segurança da Microsoft e outras fontes confiáveis para encontrar pares de nome de usuário e senha vazados. Organizações com licenças Azure AD Premium P2 podem criar políticas de acesso condicional que incorporam [Azure ad Identity Protection detecções de riscos do usuário](../identity-protection/concept-identity-protection-risks.md#user-risk). 

Há dois locais em que essa política pode ser atribuída. As organizações devem escolher uma das opções a seguir para habilitar uma política de acesso condicional baseada em risco do usuário que exija uma alteração de senha segura.

## <a name="enable-with-conditional-access-policy"></a>Habilitar com política de acesso condicional

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas. Para obter mais informações, [defina os padrões de nomenclatura para suas políticas](./plan-conditional-access.md#set-naming-standards-for-your-policies).
1. Em **Atribuições**, selecione **Usuários e Grupos**.
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações**  >  **incluem**, selecione **todos os aplicativos de nuvem**.
1. Em **condições**  >  **risco do usuário**, defina **Configurar** como **Sim**. Em **configurar os níveis de risco do usuário necessários para que a política seja imposta** , selecione **alta**e, em seguida, selecione **concluído**.
1. Em concessão de **controles de acesso**  >  **Grant**, selecione **conceder acesso**, **exigir alteração de senha**e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

## <a name="enable-through-identity-protection"></a>Habilitar por meio da proteção de identidade

1. Entre no **portal do Azure**.
1. Selecione **todos os serviços**e, em seguida, navegue até **Azure ad Identity Protection**.
1. Selecione a **política de risco do usuário**.
1. Em **atribuições**, selecione **usuários**.
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**.
   1. Selecione **Concluído**.
1. Em **condições**, selecione **risco do usuário**e, em seguida, escolha **alta**.
   1. Selecione **selecionar**e, em seguida, **concluído**.
1. Em **controles**  >  de**acesso**, escolha **permitir acesso**e, em seguida, selecione **exigir alteração de senha**.
   1. Selecione **Selecionar**.
1. Defina **aplicar política** como **ativado**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Acesso condicional baseado em risco de entrada](howto-conditional-access-policy-risk.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)

[O que é Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)