---
title: Política de linha de base a proteção de usuário final (visualização) – Azure Active Directory
description: Política de acesso condicional para exigir a autenticação multifator para usuários
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
ms.openlocfilehash: f2644e0e35139ac470b89f6af1b95cf510f60a0a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561012"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Política de linha de base: Proteção do usuário final (visualização)

Podemos tendem a pensar que as contas de administrador são as únicas contas que precisam de proteção com a autenticação multifator (MFA). Os administradores têm amplo acesso a informações confidenciais e podem fazer alterações nas configurações de toda a assinatura. No entanto, os atores ruins tendem a usuários finais de destino. Após obter acesso, esses atores ruins podem solicitar acesso a informações privilegiadas em nome do titular da conta original ou baixar todo o diretório para realizar um ataque de phishing em toda a organização. Um método comum para melhorar a proteção para todos os usuários é exigir uma forma mais forte de verificação de conta, como multi-factor authentication (MFA).

Para obter um equilíbrio razoável entre segurança e usabilidade, os usuários não devem ser solicitados toda vez que entrar. Solicitações de autenticação que refletem o comportamento do usuário normal, como entrar no mesmo dispositivo do mesmo local, terão a chance de baixa de comprometimento. Somente entradas que são consideradas arriscadas e mostram as características de um ator mal-intencionado devem ser solicitadas com desafios de MFA.

A proteção do usuário final é um MFA baseado em risco [política de linha de base](concept-baseline-protection.md) que protege todos os usuários em um diretório, incluindo todas as funções de administrador. A habilitação dessa política requer que todos os usuários se registrar para MFA usando o aplicativo autenticador. Os usuários podem ignorar o prompt de registro MFA por 14 dias, após o qual eles serão impedidos de entrar até que eles se registrar para MFA. Depois de registrado para MFA, os usuários serão solicitados para o MFA somente durante as tentativas de entrada arriscadas. Contas de usuário comprometidas são bloqueadas até que a senha é redefinida e eventos de risco foram ignorados.

> [!NOTE]
> Essa política se aplica a todos os usuários, incluindo contas de convidado e será avaliada quando o registro em log em todos os aplicativos.

## <a name="recovering-compromised-accounts"></a>Recuperando contas comprometidas

Para ajudar a proteger nossos clientes, o serviço de credenciais vazadas da Microsoft localiza pares de nome de usuário/senha publicamente disponíveis. Se elas corresponderem a um dos nossos usuários, ajudamos a proteger essa conta imediatamente. Usuários identificados como tendo uma credencial vazada forem confirmados comprometida. Esses usuários serão impedidos de entrar até que a senha é redefinida.

Os usuários atribuídos a uma licença do Azure AD Premium podem restaurar o acesso por meio de redefinição de senha de autoatendimento (SSPR) se o recurso estiver habilitado em seu diretório. Os usuários sem uma licença premium que tornam-se bloqueado devem contatar um administrador para executar uma redefinição de senha manual e ignorar o evento de risco do usuário sinalizado.

### <a name="steps-to-unblock-a-user"></a>Etapas para desbloquear um usuário

Confirme que o usuário foi bloqueado pela política, examinando os logs de entrada do usuário.

1. Um administrador precisa entrar para o **portal do Azure** e navegue até **Azure Active Directory** > **usuários** > clique no nome do usuário e navegar para entradas.
1. Para iniciar a redefinição de senha em um usuário bloqueado, um administrador precisa navegar até **Azure Active Directory** > **usuários sinalizados para risco**
1. Clique no usuário cuja conta está bloqueada para exibir informações sobre a atividade de entrada recentes do usuário.
1. Clique em Redefinir a senha para atribuir uma senha temporária que deve ser alterada após o próximo logon.
1. Clique em Descartar todos os eventos para redefinir a pontuação de risco do usuário.

O usuário agora pode entrar, redefina a senha e acessar o aplicativo.

## <a name="deployment-considerations"></a>Considerações de implantação

Porque o **proteção do usuário final** política se aplica a todos os usuários em seu diretório, várias considerações precisam ser feitas para assegurar uma implantação tranquila. Essas considerações incluem a identificação de usuários e entidades de serviço no Azure AD que não é possível ou não deve realizar a MFA, bem como aplicativos e clientes usados pela sua organização que não dão suporte a autenticação moderna.

### <a name="legacy-protocols"></a>Protocolos herdados

Protocolos de autenticação herdados (IMAP, SMTP, POP3, etc.) são usados por clientes de email para fazer solicitações de autenticação. Esses protocolos não oferecem suporte a MFA.  Com muita frequência os comprometimentos de conta vistos pela Microsoft é causadas por atores ruins executar ataques contra protocolos herdados, a tentativa de ignorar o MFA. Para garantir que o MFA é exigido ao fazer logon em uma conta e atores ruins não é possível ignorar o MFA, esta política bloqueia todas as solicitações de autenticação feitas em contas de administrador de protocolos herdados.

> [!WARNING]
> Antes de habilitar essa política, certifique-se de que os usuários não estiverem usando protocolos de autenticação herdados. Consulte o artigo [como: Autenticação herdados do bloco para o Azure AD com acesso condicional](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) para obter mais informações.

## <a name="enable-the-baseline-policy"></a>Habilitar a política de linha de base

A política **política de linha de base: Proteção do usuário final (versão prévia)** vem pré-configurada e aparecerá na parte superior quando você navega até a folha de acesso condicional no portal do Azure.

Para habilitar essa política e proteger os usuários:

1. Entrar para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Proteção do usuário final (versão prévia)** .
1. Definir **habilitar política** à **usar a política imediatamente**.
1. Clique em **salvar**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é acesso condicional no Azure Active Directory?](overview.md)
