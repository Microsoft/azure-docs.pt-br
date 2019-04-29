---
title: Habilitar a MFA para todos os administradores do Azure
description: Diretrizes para habilitar o administrador global
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: d0479b834f814616e44a1888ab8b958990610820
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611712"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Impor a MFA (autenticação multifator) para administradores de assinaturas

Quando você cria seus administradores, incluindo sua conta de administrador global, é essencial usar métodos de autenticação muito fortes.

Você pode executar a administração cotidiana atribuindo funções específicas de administrador – como administrador do Exchange ou administrador de senhas – para contas de usuário da equipe de TI conforme necessário.
Além disso, habilitar a [Autenticação Multifator (MFA) do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) para seus administradores adiciona uma segunda camada de segurança para logons de usuário e transações. A MFA do Azure também ajuda o departamento de TI a reduzir a probabilidade de que uma credencial comprometida tenha acesso aos dados da organização.

Por exemplo: você impõe a MFA do Azure a seus usuários e a configura para usar uma chamada telefônica ou uma mensagem de texto como verificação. Se as credenciais do usuário estiverem comprometidas, o invasor não poderá acessar recurso algum, pois não terá acesso ao telefone do usuário. As organizações que não adicionam camadas adicionais de proteção de identidade são mais suscetíveis a ataques de roubo de credenciais, que podem levar ao comprometimento dos dados.

Uma alternativa para as organizações que desejam manter todo o controle da autenticação localmente é usar o [Servidor de Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), também chamado de "MFA local". Usando esse método você ainda poderá impor a autenticação multifator, mantendo o servidor MFA local.

Você pode verificar quem em sua organização tem privilégios administrativos usando o seguinte comando de PowerShell V2 do Microsoft Azure AD:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Enabling MFA

Revise como a [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) opera antes de continuar.

Se os usuários tiverem licenças que incluam a Autenticação Multifator do Microsoft Azure, nada precisa ser feito para ativar o Azure MFA. Você pode começar solicitando uma verificação em duas etapas em base de usuário individual. As licenças que habilitam o Azure MFA são:

- Autenticação Multifator do Azure
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação em duas etapas para usuários

Use um dos procedimentos listados em [Como exigir uma verificação em duas etapas](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) para um usuário ou grupo para começar a usar o Azure MFA. Você pode escolher forçar a verificação em duas etapas para todas as entradas ou criar políticas de acesso condicional para exigir uma verificação em duas etapas somente quando for importante.

