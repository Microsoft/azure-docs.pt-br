---
title: Visão geral de segurança do Azure AD (Azure Active Directory) Identity Protection | Microsoft Docs
description: Saiba como a "Visão geral de segurança" oferece a você um insight da postura de segurança da sua organização.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b894f7020083dd6ca46c394ec2930a3da36bc76d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335212"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – visão geral de segurança

A [Visão geral de segurança](https://aka.ms/IdentityProtectionRefresh) oferece um insight quanto à postura de segurança da sua organização. Ela ajuda a identificar possíveis ataques e compreender a eficácia de suas políticas.

A "Visão geral de segurança" é amplamente dividida em duas seções:

- Tendências, à esquerda, fornecem uma linha do tempo de risco em sua organização.
- Blocos, à direita, realçam os principais problemas atuais em sua organização e sugerem como agir rapidamente.

![Visão geral da segurança](./media/security-overview/01.png)
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Novos usuários arriscados detectados

Este gráfico mostra o número de novos usuários arriscados detectados durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico por nível de risco (baixo, médio, alto). Passe o mouse sobre os incrementos de data UTC para ver o número de usuários arriscados detectados nesse dia. Um clique nesse gráfico apresentará a você o relatório "Usuários arriscados". Para corrigir os usuários que estão em risco, considere alterar as respectivas senhas.

### <a name="new-risky-sign-ins-detected"></a>Novas entradas arriscadas detectadas

Este gráfico mostra o número de novas entradas arriscadas detectadas durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico pelo tipo de risco de entrada (em tempo real ou agregação) e o nível de risco de entrada (baixo, médio, alto). Entradas desprotegidas são entradas bem-sucedidas de risco em tempo real que não passaram por desafio de MFA. (Observação: Entradas são arriscadas porque detecções offline não podem ser protegidas em tempo real por meio de políticas de risco de entrada). Passe o mouse sobre os incrementos de data UTC para ver o número de entradas detectadas como em risco nesse dia. Um clique nesse gráfico apresentará a você o relatório 'Entradas arriscadas'.

## <a name="tiles"></a>Blocos
 
### <a name="high-risk-users"></a>Usuários de risco alto

O bloco "Usuários de risco alto" mostra a última contagem de usuários com alta probabilidade de comprometimento de identidade. Eles devem ser uma prioridade principal para investigação. Um clique no bloco "Usuários de risco alto" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco alto. Usando esse relatório, você pode aprender mais e corrigir esses usuários com uma redefinição de senha.

![Visão geral da segurança](./media/security-overview/02.png)

### <a name="medium-risk-users"></a>Usuários de risco médio
O bloco "Usuários de risco médio" mostra a última contagem de usuários com probabilidade média de comprometimento de identidade. Um clique no bloco "Usuários de risco médio" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco médio. Usando esse relatório, você pode investigar e corrigir ainda mais esses usuários.

### <a name="unprotected-risky-sign-ins"></a>Entradas arriscadas desprotegidas

O bloco "Entradas arriscadas desprotegidas" mostra a contagem da semana passada de entradas arriscadas bem-sucedidas em tempo real que não foram bloqueadas nem passaram por desafio de MFA de uma política de acesso condicional, política de risco do Identity Protection ou MFA por usuário. Esses são logons potencialmente comprometidos que tiveram êxito e não passaram por desafio de MFA. Para proteger essas entradas no futuro, aplique uma política de risco de entrada. Um clique no bloco "Entradas arriscadas desprotegidas" redirecionará para a folha de configuração de política de risco de entrada, na qual você poderá configurar a política de risco de entrada para exigir o MFA em uma entrada com um nível de risco especificado.

### <a name="legacy-authentication"></a>Autenticação herdada

O bloco "Autenticação herdada" mostra a contagem de autenticações herdadas realizadas na semana passada em sua organização. Protocolos de autenticação herdados não são compatíveis com métodos de segurança modernos, tais como um MFA. Para impedir a autenticação herdada, você pode aplicar uma política de acesso condicional. Um clique no bloco ‘Autenticação herdada’ redirecionará você para a ‘Classificação de Segurança de Identidade’.

### <a name="identity-secure-score"></a>Classificação de Segurança de Identidade

A Classificação de Segurança de Identidade mede e compara a sua postura de segurança para os padrões de mercado. Se você clicar no bloco ‘Classificação de Segurança de Identidade (Versão Prévia)’, ele redirecionará para a folha de ‘Classificação de Segurança de Identidade (Versão Prévia)’, na qual você poderá aprender mais sobre como melhorar sua postura de segurança.

## <a name="next-steps"></a>Próximas etapas

- [Canal 9: Azure AD e Identity Show: Versão Prévia do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Habilitando o Azure Active Directory Identity Protection](enable.md)
