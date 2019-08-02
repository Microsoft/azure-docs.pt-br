---
title: Política de linha de base exigir MFA para administradores - Azure Active Directory
description: Política de acesso condicional para exigir a autenticação multifator para administradores
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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560947"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Política de linha de base: Exigir MFA para os administradores (versão prévia)

Usuários com acesso a contas privilegiadas têm acesso irrestrito a seu ambiente. Devido à capacidade que essas contas têm, devem ser tratadas com cuidado especial. Um método comum para melhorar a proteção de contas privilegiadas é exigir uma forma mais forte de verificação de conta quando elas são usadas para entrar. No Azure Active Directory, você pode obter uma verificação de conta mais forte exigindo MFA (autenticação multifator).

**Exigir MFA para os administradores (versão prévia)**  é um [política de linha de base](concept-baseline-protection.md) que requer a MFA sempre que uma das seguintes funções de administrador com privilégios entra em:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança
* Administrador de assistência técnica / administrador de senha
* Administrador de cobrança
* Administrador de usuários

Após a habilitação da exigir MFA para os administradores política, as funções de nove administrador acima precisará se registrar para MFA usando o aplicativo autenticador. Depois que o registro de MFA for concluído, os administradores precisarão executar a MFA toda vez que entrar.

## <a name="deployment-considerations"></a>Considerações de implantação

Porque o **exigir MFA para que os administradores (versão prévia)** política se aplica a todos os administradores críticos, várias considerações precisam ser feitas para assegurar uma implantação tranquila. Essas considerações incluem a identificação de usuários e entidades de serviço no Azure AD que não é possível ou não deve realizar a MFA, bem como aplicativos e clientes usados pela sua organização que não dão suporte a autenticação moderna.

### <a name="legacy-protocols"></a>Protocolos herdados

Protocolos de autenticação herdados (IMAP, SMTP, POP3, etc.) são usados por clientes de email para fazer solicitações de autenticação. Esses protocolos não oferecem suporte a MFA. Com muita frequência os comprometimentos de conta vistos pela Microsoft é causadas por atores ruins executar ataques contra protocolos herdados, a tentativa de ignorar o MFA. Para garantir que o MFA é exigido ao fazer logon em uma conta administrativa e atores ruins não é possível ignorar o MFA, esta política bloqueia todas as solicitações de autenticação feitas em contas de administrador de protocolos herdados.

> [!WARNING]
> Antes de habilitar essa política, verifique se que os administradores não estão usando protocolos de autenticação herdados. Consulte o artigo [como: Autenticação herdados do bloco para o Azure AD com acesso condicional](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) para obter mais informações.

## <a name="enable-the-baseline-policy"></a>Habilitar a política de linha de base

A política **política de linha de base: Exigir MFA para os administradores (versão prévia)** vem pré-configurada e aparecerá na parte superior quando você navega até a folha de acesso condicional no portal do Azure.

Para habilitar essa política e proteger seus administradores:

1. Entrar para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Exigir MFA para os administradores (versão prévia)** .
1. Definir **habilitar política** à **usar a política imediatamente**.
1. Clique em **salvar**.

> [!WARNING]
> Houve uma opção **habilitar a política automaticamente no futuro** quando essa política estava em versão prévia. Removemos esta opção para minimizar o impacto do usuário repentina. Se você selecionou essa opção quando ele estava disponível, **não usar a política** é automaticamente selecionado agora. Se eles desejam usar essa política de linha de base, consulte as etapas acima para habilitá-lo.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é acesso condicional no Azure Active Directory?](overview.md)
