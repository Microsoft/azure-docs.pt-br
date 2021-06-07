---
title: Acesso condicional com base em risco de entrada-Azure Active Directory
description: Criar políticas de acesso condicional usando o risco de entrada da proteção de identidade
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
ms.openlocfilehash: a09c4513206bea3462577ecba49b5d77b655b0e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91628257"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>Acesso condicional: acesso condicional com base em risco de entrada

A maioria dos usuários tem um comportamento normal que pode ser acompanhado; quando eles saem do padrão, pode ser arriscado permitir que eles se conectem diretamente. Talvez você queira bloquear esse usuário ou talvez apenas pedir que eles executem a autenticação multifator para provar que eles são realmente quem dizem que estão. 

Um risco de entrada representa a probabilidade de uma determinada solicitação de autenticação não estar autorizada pelo proprietário da identidade. Organizações com licenças Azure AD Premium P2 podem criar políticas de acesso condicional que incorporam [Azure ad Identity Protection detecções de risco de entrada](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).

Há dois locais em que essa política pode ser atribuída. As organizações devem escolher uma das opções a seguir para habilitar uma política de acesso condicional com base em risco de entrada que exija uma alteração de senha segura.

## <a name="enable-with-conditional-access-policy"></a>Habilitar com política de acesso condicional

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e Grupos**.
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **Aplicativos ou ações de nuvem** > **Incluir**, selecione **Todos os aplicativos de nuvem**.
1. Em   >  **risco de entrada** de condições, defina **Configurar** como **Sim**. Em **selecionar o nível de risco de entrada ao qual esta política será aplicada** 
   1. Selecione **alta** e **média**.
   1. Selecione **Concluído**.
1. Em **Controles de acesso** > **Conceder**, selecione **Conceder acesso**, **Exigir autenticação multifator** e selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

## <a name="enable-through-identity-protection"></a>Habilitar por meio da proteção de identidade

1. Entre no **portal do Azure**.
1. Selecione **todos os serviços** e, em seguida, navegue até **Azure ad Identity Protection**.
1. Selecione **a política de risco de entrada**.
1. Em **atribuições**, selecione **usuários**.
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**.
   1. Selecione **Concluído**.
1. Em **condições**, selecione **risco de entrada** e, em seguida, escolha **médio e superior**.
   1. Selecione **selecionar** e, em seguida, **concluído**.
1. Em **controles**  >  de **acesso**, escolha **permitir acesso** e, em seguida, selecione **exigir autenticação multifator**.
   1. Selecione **Selecionar**.
1. Defina **aplicar política** como **ativado**.
1. Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Acesso condicional baseado em risco de usuário](howto-conditional-access-policy-risk-user.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)

[O que é Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
