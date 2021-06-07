---
title: Políticas de risco-Azure Active Directory Identity Protection
description: Habilitar e configurar políticas de risco no Azure Active Directory Identity Protection
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
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997623"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Como configurar e habilitar as políticas de risco

Como aprendemos no artigo anterior, [as políticas de proteção de identidade](concept-identity-protection-policies.md) temos duas políticas de risco que podemos habilitar em nosso diretório. 

- Política de risco de entrada
- Política de risco do usuário

![Página Visão geral de segurança para habilitar as políticas de risco de usuário e de entrada](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Ambas as políticas funcionam para automatizar a resposta a detecções de risco em seu ambiente e permitir que os usuários se corrijam quando o risco é detectado. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Pré-requisitos 

Se sua organização quiser permitir que os usuários se corrijam quando forem detectados riscos, os usuários deverão ser registrados para a redefinição de senha de autoatendimento e a autenticação multifator do Azure AD. É recomendável [habilitar a experiência combinada de registro de informações de segurança](../authentication/howto-registration-mfa-sspr-combined.md) para obter a melhor experiência. Permitir que os usuários se corrijam os coloca novamente em um estado produtivo mais rapidamente, sem a necessidade de intervenção do administrador. Os administradores ainda podem ver esses eventos e investigá-los após o fato. 

## <a name="choosing-acceptable-risk-levels"></a>Escolhendo níveis de risco aceitáveis

As organizações devem decidir o nível de risco que estão dispostos a aceitar o balanceamento da experiência do usuário e da postura de segurança. 

A recomendação da Microsoft é definir o limite de política de risco do usuário como **alto** e a política de risco de entrada como **média e posterior**.

Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários. No entanto, isso exclui detecções de risco sinalizadas com **Baixo** e **Médio** da política, o que pode não impedir que um invasor explore uma identidade comprometida. A seleção de um limite **baixo** introduz interrupções de usuário adicionais, mas uma postura de segurança aumenta.

## <a name="exclusions"></a>Exclusões

Todas as políticas permitem a exclusão de usuários, como suas [contas de administrador de interrupção ou de acesso de emergência](../roles/security-emergency-access.md). As organizações podem determinar que precisam excluir outras contas de políticas específicas com base na maneira como as contas são usadas. Todas as exclusões devem ser revisadas regularmente para ver se elas ainda são aplicáveis.

Os [locais de rede](../conditional-access/location-condition.md) confiáveis configurados são usados pela proteção de identidade em algumas detecções de risco para reduzir os falsos positivos.

## <a name="enable-policies"></a>Habilitar políticas

Para habilitar o risco do usuário e as políticas de risco de entrada, conclua as etapas a seguir.

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >    >    >  **visão geral** da proteção de identidade de segurança.
1. Selecione a **política de risco do usuário**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições**  -  do **Risco do usuário** A recomendação da Microsoft é definir essa opção como **alta**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir alteração de senha**.
   1. **Impor política**  -  **Em**
   1. **Salvar** – esta ação retornará à página **visão geral** .
1. Selecione **a política de risco de entrada**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições**  -  do **Risco de entrada** A recomendação da Microsoft é definir essa opção como **média e posterior**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir a autenticação multifator**.
   1. **Impor política**  -  **Em**
   1. **Salvar**

## <a name="next-steps"></a>Próximas etapas

- [Habilitar a política de registro da autenticação multifator do Azure AD](howto-identity-protection-configure-mfa-policy.md)

- [O que é risco](concept-identity-protection-risks.md)

- [Investigar detecções de risco](howto-identity-protection-investigate-risk.md)

- [Simular detecções de riscos](howto-identity-protection-simulate-risk.md)
