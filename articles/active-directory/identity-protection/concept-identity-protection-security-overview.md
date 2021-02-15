---
title: Visão geral da segurança do Azure Active Directory Identity Protection
description: Saiba como a visão geral de segurança fornece informações sobre a postura de segurança de sua organização.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c589289d77597be2550673944c8fa21902e0fb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098460"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – visão geral de segurança

A [visão geral de segurança](https://aka.ms/IdentityProtectionRefresh) na portal do Azure fornece uma percepção da postura de segurança de sua organização. Ela ajuda a identificar possíveis ataques e compreender a eficácia de suas políticas.

A "Visão geral de segurança" é amplamente dividida em duas seções:

- Tendências, à esquerda, fornecem uma linha do tempo de risco em sua organização.
- Blocos, à direita, realçam os principais problemas atuais em sua organização e sugerem como agir rapidamente.

:::image type="content" source="./media/concept-identity-protection-security-overview/01.png" alt-text="Captura de tela da visão geral de segurança do portal do Azure. Os gráficos de barras mostram a contagem de riscos ao longo do tempo. Os blocos resumem informações sobre usuários e entradas." border="false":::
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Novos usuários arriscados detectados

Este gráfico mostra o número de novos usuários arriscados detectados durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico por nível de risco (baixo, médio, alto). Passe o mouse sobre os incrementos de data UTC para ver o número de usuários arriscados detectados nesse dia. Um clique nesse gráfico apresentará a você o relatório "Usuários arriscados". Para corrigir os usuários que estão em risco, considere alterar as respectivas senhas.

### <a name="new-risky-sign-ins-detected"></a>Novas entradas arriscadas detectadas

Este gráfico mostra o número de novas entradas arriscadas detectadas durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico pelo tipo de risco de entrada (em tempo real ou agregação) e o nível de risco de entrada (baixo, médio, alto). Entradas desprotegidas são entradas bem-sucedidas de risco em tempo real que não passaram por desafio de MFA. (Observação: as entradas que são arriscadas devido a detecções offline não podem ser protegidas em tempo real por políticas de risco de entrada). Passe o mouse sobre os incrementos de data UTC para ver o número de entradas detectadas como em risco nesse dia. Um clique nesse gráfico apresentará a você o relatório 'Entradas arriscadas'.

## <a name="tiles"></a>Tiles
 
### <a name="high-risk-users"></a>Usuários de risco alto

O bloco "Usuários de risco alto" mostra a última contagem de usuários com alta probabilidade de comprometimento de identidade. Eles devem ser uma prioridade principal para investigação. Um clique no bloco "Usuários de risco alto" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco alto. Usando esse relatório, você pode aprender mais e corrigir esses usuários com uma redefinição de senha.

:::image type="content" source="./media/concept-identity-protection-security-overview/02.png" alt-text="Captura de tela da visão geral de segurança do portal do Azure. Os gráficos de barras mostram a contagem de riscos ao longo do tempo. Os blocos resumem informações sobre usuários e entradas." border="false":::

### <a name="medium-risk-users"></a>Usuários de risco médio
O bloco "Usuários de risco médio" mostra a última contagem de usuários com probabilidade média de comprometimento de identidade. Um clique no bloco "Usuários de risco médio" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco médio. Usando esse relatório, você pode investigar e corrigir ainda mais esses usuários.

### <a name="unprotected-risky-sign-ins"></a>Entradas arriscadas desprotegidas

O bloco ' entradas arriscadas não protegidas ' mostra a contagem da semana passada de entradas de risco em tempo real bem-sucedidas, que não foram bloqueadas ou que MFA desafiada por uma política de acesso condicional, uma política de risco de proteção de identidade ou uma MFA por usuário. Esses são logons potencialmente comprometidos que tiveram êxito e não passaram por desafio de MFA. Para proteger essas entradas no futuro, aplique uma política de risco de entrada. Um clique no bloco "Entradas arriscadas desprotegidas" redirecionará para a folha de configuração de política de risco de entrada, na qual você poderá configurar a política de risco de entrada para exigir o MFA em uma entrada com um nível de risco especificado.

### <a name="legacy-authentication"></a>Autenticação herdada

O bloco ' autenticação herdada ' mostra a contagem da última semana de autenticações herdadas com risco presente em sua organização. Protocolos de autenticação herdados não são compatíveis com métodos de segurança modernos, tais como um MFA. Para impedir a autenticação herdada, você pode aplicar uma política de acesso condicional. Um clique no bloco ‘Autenticação herdada’ redirecionará você para a ‘Classificação de Segurança de Identidade’.

### <a name="identity-secure-score"></a>Classificação de segurança de identidade

A Classificação de Segurança de Identidade mede e compara a sua postura de segurança para os padrões de mercado. Se você clicar no bloco ' identidade segura (visualização) ', ele será redirecionado para a folha ' identidade segura ', em que você pode aprender mais sobre como melhorar sua postura de segurança.

## <a name="next-steps"></a>Próximas etapas

- [O que é risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
