---
title: Proteção de identidade e usuários B2B-Azure Active Directory
description: Usando a proteção de identidade com usuários B2B como ele funciona e limitações conhecidas
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9128a747dcd42ada2feefd72ad8bfebdf7bfb25a
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334051"
---
# <a name="identity-protection-and-b2b-users"></a>Usuários do Identity Protection e B2B

Com a colaboração B2B do Azure AD, as organizações podem impor políticas baseadas em risco para usuários B2B usando a proteção de identidade. Essas políticas são configuradas de duas maneiras:

- Os administradores podem configurar as políticas internas baseadas em risco da proteção de identidade, que se aplicam a todos os aplicativos, que incluem usuários convidados.
- Os administradores podem configurar suas políticas de acesso condicional, usando o risco de entrada como uma condição, que inclui usuários convidados.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Como o risco é avaliado para usuários de colaboração B2B

O risco do usuário para usuários de colaboração B2B é avaliado em seu diretório base. O risco de entrada em tempo real para esses usuários é avaliado no diretório de recursos quando eles tentam acessar o recurso.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Limitações da proteção de identidade para usuários de colaboração B2B

Há limitações na implementação da proteção de identidade para usuários de colaboração B2B em um diretório de recursos devido à sua identidade existente em seu diretório base. As principais limitações são as seguintes:

- Se um usuário convidado disparar a política de risco do usuário de proteção de identidade para forçar a redefinição de senha, ele será bloqueado. Esse bloco é devido à incapacidade de redefinir senhas no diretório de recursos.
- Os usuários convidados não aparecem no relatório de usuários arriscados. Essa perda de visibilidade ocorre devido à avaliação de risco que ocorre no diretório base do usuário B2B.
- Os administradores não podem ignorar ou corrigir um usuário de colaboração B2B arriscado em seu diretório de recursos. Essa perda de funcionalidade é devido aos administradores no diretório de recursos não terem acesso ao diretório base do usuário B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Por que não é possível corrigir usuários arriscados de colaboração B2B em meu diretório?

A avaliação de risco e a correção para usuários B2B ocorrem em seu diretório base. Devido a esse fato, os usuários convidados não aparecem no relatório usuários arriscados no diretório de recursos e os administradores no diretório de recursos não podem forçar uma redefinição de senha segura para esses usuários.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>O que devo fazer se um usuário de colaboração B2B fosse bloqueado devido a uma política baseada em risco em minha organização?

Se um usuário B2B arriscado em seu diretório for bloqueado pela sua política baseada em risco, o usuário precisará corrigir esse risco em seu diretório base. Os usuários podem corrigir seus riscos executando uma redefinição de senha segura em seu diretório base. Se eles não tiverem a redefinição de senha de autoatendimento habilitada em seu diretório base, eles precisarão entrar em contato com a equipe de ti da própria organização para que um administrador ignore manualmente seu risco ou Redefina sua senha.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Como fazer impedir que os usuários de colaboração B2B sejam afetados por políticas baseadas em risco?

A exclusão de usuários B2B das políticas de acesso condicional com base em risco da sua organização impedirá que os usuários B2B sejam afetados ou bloqueados pela avaliação de risco. Para excluir esses usuários B2B, crie um grupo no Azure AD que contenha todos os usuários convidados da sua organização. Em seguida, adicione esse grupo como uma exclusão para as políticas internas de risco do usuário e de entrada de proteção de identidade, bem como as políticas de acesso condicional que o usuário entrará em risco como uma condição.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do AD do Azure?](../b2b/what-is-b2b.md)
- [O que é o acesso condicional?](../conditional-access/overview.md)
