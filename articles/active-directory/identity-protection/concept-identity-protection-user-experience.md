---
title: Experiências do usuário com o Azure AD Identity Protection
description: Experiência do usuário de Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835978"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Experiências do usuário com o Azure AD Identity Protection

Com o Azure Active Directory Identity Protection, é possível:

* Exigir que os usuários se registrem na MFA (autenticação multifator) do Azure AD
* Automatizar a correção de entradas arriscadas e usuários comprometidos

Todas as políticas de proteção de identidade têm um impacto na experiência de entrada para os usuários. Permitir que os usuários se registrem e usem ferramentas como a MFA do Azure AD e a redefinição de senha de autoatendimento pode diminuir o impacto. Essas ferramentas, juntamente com as opções de política apropriadas, oferecem aos usuários uma opção de AutoCorreção quando precisam.

## <a name="multi-factor-authentication-registration"></a>Registro de autenticação multifator

Habilitar a política de proteção de identidade que requer o registro de autenticação multifator e direcionar todos os seus usuários garantirá que eles tenham a capacidade de usar o Azure AD MFA para corrigir automaticamente no futuro. Configurar essa política dá aos seus usuários um período de 14 dias em que eles podem optar por se registrar e, no final, são forçados a se registrar. A experiência para os usuários é descrita abaixo. Mais informações podem ser encontradas na documentação do usuário final no artigo [visão geral da verificação de dois fatores e sua conta corporativa ou de estudante](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="registration-interrupt"></a>Interrupção de registro

1. Ao entrar em qualquer aplicativo integrado ao Azure AD, o usuário recebe uma notificação sobre a necessidade de configurar a conta para autenticação multifator. Essa política também é disparada na experiência do Windows 10 pronta para uso para novos usuários com um novo dispositivo.
   
    ![Mais informações necessárias](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Conclua as etapas guiadas para se registrar para a autenticação multifator do Azure AD e conclua sua entrada.

## <a name="risky-sign-in-remediation"></a>Correção de entrada arriscada

Quando um administrador configurou uma política para riscos de entrada, os usuários afetados são notificados quando tentam entrar e disparam o nível de risco das políticas. 

### <a name="risky-sign-in-self-remediation"></a>AutoCorreção de entrada arriscada

1. O usuário é informado de que algo incomum foi detectado em sua entrada, por exemplo, a entrada a partir de um novo local, dispositivo ou aplicativo.
   
    ![Algo de aviso incomum](./media/concept-identity-protection-user-experience/120.png)

1. O usuário precisa provar sua identidade concluindo o Azure AD MFA com um de seus métodos anteriormente registrados. 

### <a name="risky-sign-in-administrator-unblock"></a>Desbloqueio de administrador de entrada arriscada

Os administradores podem optar por bloquear os usuários após a entrada, dependendo do nível de risco. Para ser desbloqueado, os usuários finais devem entrar em contato com sua equipe de ti ou tentar entrar de um dispositivo ou local familiar. A AutoCorreção executando a autenticação multifator não é uma opção nesse caso.

![Bloqueado pela política de risco de entrada](./media/concept-identity-protection-user-experience/200.png)

A equipe de ti pode seguir as instruções na seção [desbloqueando os usuários](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) para permitir que os usuários entrem novamente.

## <a name="risky-user-remediation"></a>Remediação de usuário arriscada

Quando uma política de risco do usuário tiver sido configurada, os usuários que atenderem à probabilidade de comprometimento do nível de risco do usuário deverão passar pelo fluxo de recuperação de comprometimento do usuário antes que possam entrar. 

### <a name="risky-user-self-remediation"></a>AutoCorreção de usuário arriscado

1. O usuário é informado de que a segurança da conta está em risco devido à atividade suspeita ou credenciais vazadas.
   
    ![Remediação](./media/concept-identity-protection-user-experience/101.png)

1. O usuário precisa provar sua identidade concluindo o Azure AD MFA com um de seus métodos anteriormente registrados. 
1. Por fim, o usuário é forçado a alterar sua senha usando a redefinição de senha de autoatendimento, já que outra pessoa pode ter tido acesso à sua conta.

## <a name="risky-sign-in-administrator-unblock"></a>Desbloqueio de administrador de entrada arriscada

Os administradores podem optar por bloquear os usuários após a entrada, dependendo do nível de risco. Para ser desbloqueado, os usuários finais devem entrar em contato com a equipe de ti. A AutoCorreção executando a autenticação multifator e a redefinição de senha de autoatendimento não é uma opção nesse caso.

![Bloqueado pela política de risco do usuário](./media/concept-identity-protection-user-experience/104.png)

A equipe de ti pode seguir as instruções na seção [desbloqueando os usuários](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) para permitir que os usuários entrem novamente.

## <a name="see-also"></a>Confira também

- [Corrigir riscos e desbloquear usuários](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)