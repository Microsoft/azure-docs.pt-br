---
title: Políticas de proteção de linha de base de acesso condicional-Azure Active Directory
description: Políticas de acesso condicional de linha de base para proteger as organizações contra ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2fc552211ed55239259ce84d84584c451733d70
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499872"
---
# <a name="what-are-baseline-policies"></a>O que são políticas de linha de base?

As políticas de linha de base são um conjunto de políticas predefinidas que ajudam a proteger as organizações contra muitos ataques comuns. Esses ataques comuns podem incluir pulverização, reprodução e phishing de senha. As políticas de linha de base estão disponíveis em todas as edições do Azure AD. A Microsoft está tornando essas políticas de proteção de linha de base disponíveis para todos, pois os ataques baseados em identidade estão aumentando os últimos anos. O objetivo dessas quatro políticas é garantir que todas as organizações tenham um nível de linha de base de segurança habilitado sem nenhum custo adicional.  

O gerenciamento de políticas de acesso condicional personalizado requer uma licença de Azure AD Premium.

## <a name="baseline-policies"></a>Políticas de linha de base

![Políticas de linha de base de acesso condicional no portal do Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Há quatro políticas de linha de base que as organizações podem habilitar:

* [Exigir MFA para administradores (versão prévia)](howto-baseline-protect-administrators.md)
* [Proteção do usuário final (versão prévia)](howto-baseline-protect-end-users.md)
* [Bloquear autenticação herdada (versão prévia)](howto-baseline-protect-legacy-auth.md)
* [Exigir MFA para gerenciamento de serviços (versão prévia)](howto-baseline-protect-azure.md)

Todas as quatro políticas afetarão os fluxos de autenticação herdados como POP, IMAP e clientes de área de trabalho do Office mais antigos.

### <a name="require-mfa-for-admins-preview"></a>Exigir MFA para administradores (versão prévia)

Devido à potência e ao acesso que as contas de administrador têm, você deve tratá-las com cuidado especial. Um método comum para melhorar a proteção de contas com privilégios é exigir uma forma mais forte de verificação de conta quando eles são usados para entrar. No Azure Active Directory, você pode obter uma verificação de conta mais forte exigindo que os administradores se registrem e usem a autenticação multifator do Azure.

[Exigir MFA para administradores (visualização)](howto-baseline-protect-administrators.md) é uma política de linha de base que requer MFA (autenticação multifator) para as seguintes funções de diretório, consideradas como as funções mais privilegiadas do Azure AD:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de Acesso Condicional
* Administrador de segurança
* Administrador de assistência técnica/administrador de senha
* Administrador de cobrança
* Administrador de usuários

Se sua organização tiver essas contas em uso em scripts ou código, considere substituí-las [](../managed-identities-azure-resources/overview.md)por identidades gerenciadas.

### <a name="end-user-protection-preview"></a>Proteção do usuário final (versão prévia)

Administradores com alto privilégio não são os únicos destinados a ataques. Atores ruins tendem a ter como alvo usuários normais. Depois de obter acesso, esses atores inválidos podem solicitar acesso a informações privilegiadas em nome do titular da conta original ou baixar o diretório inteiro e executar um ataque de phishing em toda a organização. Um método comum para melhorar a proteção para todos os usuários é exigir uma forma mais forte de verificação de conta quando uma entrada arriscada é detectada.

A **proteção do usuário final (versão prévia)** é uma política de linha de base que protege todos os usuários em um diretório. A habilitação dessa política exige que todos os usuários se registrem para a autenticação multifator do Azure dentro de 14 dias. Depois de registrado, os usuários receberão uma solicitação pela MFA somente durante tentativas de entrada arriscadas. As contas de usuário comprometidas são bloqueadas até que a redefinição de senha e o risco sejam ignorados. 

[!NOTE]
Todos os usuários sinalizados anteriormente para risco são bloqueados até a redefinição de senha e o risco serem ignorados na ativação da política.

### <a name="block-legacy-authentication-preview"></a>Bloquear autenticação herdada (versão prévia)

Protocolos de autenticação herdados (por exemplo: IMAP, SMTP, POP3) são protocolos normalmente usados por clientes de email mais antigos para autenticar. Os protocolos herdados não dão suporte à autenticação multifator. Mesmo que você tenha uma política que exija autenticação multifator para seu diretório, um ator inadequado pode autenticar usando um desses protocolos herdados e ignorar a autenticação multifator.

A melhor maneira de proteger sua conta contra solicitações de autenticação mal-intencionadas feitas por protocolos herdados é bloqueá-las.

A política de linha de base de **autenticação herdada de bloqueio (versão prévia)** bloqueia as solicitações de autenticação feitas usando protocolos herdados. A autenticação moderna deve ser usada para entrar com êxito para todos os usuários. Usado em conjunto com as outras políticas de linha de base, as solicitações provenientes de protocolos herdados serão bloqueadas. Além disso, todos os usuários serão solicitados a MFA sempre que necessário. Esta política não bloqueia o Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Exigir MFA para gerenciamento de serviços (versão prévia)

As organizações usam uma variedade de serviços do Azure e os gerenciam de ferramentas baseadas em Azure Resource Manager como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Usar qualquer uma dessas ferramentas para executar o gerenciamento de recursos é uma ação altamente privilegiada. Essas ferramentas podem alterar as configurações de toda a assinatura, como configurações de serviço e cobrança de assinatura.

Para proteger ações privilegiadas, isso **requer MFA para a política de gerenciamento de serviços (versão prévia)** exigirá autenticação multifator para qualquer usuário que acesse portal do Azure, Azure PowerShell ou CLI do Azure.

## <a name="enable-a-baseline-policy"></a>Habilitar uma política de linha de base

Para habilitar uma política de linha de base:

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione uma política de linha de base que você gostaria de habilitar.
1. Defina **habilitar política** como **ativado**.
1. Clique em salvar.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

* [Cinco etapas para proteger a infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)
* [Exigir MFA para administradores (versão prévia)](howto-baseline-protect-administrators.md)
* [Proteção do usuário final (versão prévia)](howto-baseline-protect-end-users.md)
* [Bloquear autenticação herdada (versão prévia)](howto-baseline-protect-legacy-auth.md)
* [Exigir MFA para gerenciamento de serviços (versão prévia)](howto-baseline-protect-azure.md)
