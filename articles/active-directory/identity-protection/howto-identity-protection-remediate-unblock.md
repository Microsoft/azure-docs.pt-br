---
title: Corrigir riscos e desbloquear usuários no Azure AD Identity Protection
description: Saiba mais sobre as opções que você tem de fechar as detecções de risco ativas.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e322f78468c89a549955a01f73952e8cde7a13c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872817"
---
# <a name="remediate-risks-and-unblock-users"></a>Corrigir riscos e desbloquear usuários

Depois de concluir a [investigação](howto-identity-protection-investigate-risk.md), você desejará tomar medidas para corrigir o risco ou desbloquear os usuários. As organizações também têm a opção de habilitar a correção automatizada usando suas [políticas de risco](howto-identity-protection-configure-risk-policies.md). As organizações devem tentar fechar todas as detecções de riscos com as quais elas são apresentadas em um período com que sua organização se sinta confortável. A Microsoft recomenda o fechamento dos eventos rapidamente, pois o tempo é essencial quando se lida com riscos.

## <a name="remediation"></a>Remediação

Todos os eventos de risco ativos contribuem para o cálculo de um nível de risco do usuário chamado valor. O nível de risco do usuário é um indicador (baixo, médio, alto) para a probabilidade de que uma conta tenha sido comprometida. Como administrador, você deseja fechar todos os eventos de risco de modo que os usuários afetados não estejam mais em risco.

Algumas detecções de riscos podem ser marcadas pela proteção de identidade como "fechadas (sistema)", pois os eventos não foram mais determinados como arriscados.

Os administradores têm as seguintes opções para corrigir:

- Correção automática com a política de risco
- Redefinição de senha manual
- Ignorar o risco de usuário
- Fechar detecções de risco individuais manualmente

### <a name="self-remediation-with-risk-policy"></a>Correção automática com a política de risco

Se você permitir que os usuários sejam remediados automaticamente, com a Autenticação multifator do Azure AD (MFA) e a SSPR (redefinição de senha por autoatendimento) em suas políticas de risco, eles poderão ser desbloqueados quando forem detectados riscos. Essas detecções são consideradas fechadas. Os usuários devem ter se registrado anteriormente para Azure AD MFA e SSPR para usar quando o risco for detectado.

Algumas detecções podem não aumentar o risco para o nível em que uma AutoCorreção do usuário seria necessária, mas os administradores ainda devem avaliar essas detecções. Os administradores podem determinar que medidas adicionais são necessárias, como [bloquear o acesso de locais](../conditional-access/howto-conditional-access-policy-location.md) ou reduzir o risco aceitável em suas políticas.

### <a name="manual-password-reset"></a>Redefinição de senha manual

Se exigir uma redefinição de senha usando uma política de risco do usuário não for uma opção, os administradores poderão fechar todas as detecções de risco de um usuário com uma redefinição de senha manual.

Os administradores recebem duas opções ao redefinir uma senha para seus usuários:

- **Gerar uma senha temporária** – ao gerar uma senha temporária, você pode imediatamente deixar uma identidade em um estado seguro novamente. Esse método requer contato com os usuários afetados porque eles precisam saber qual é a senha temporária. Como a senha é temporária, o usuário é solicitado a alterar a senha para algo novo durante a próxima entrada.

- **Exigir que o usuário redefina a senha** – exigir que os usuários redefinam senhas permite a autorrecuperação sem entrar em contato com o suporte técnico nem com o administrador. Esse método só se aplica a usuários registrados para o Azure AD MFA e SSPR. Para usuários que não foram registrados, essa opção não está disponível.

### <a name="dismiss-user-risk"></a>Ignorar o risco de usuário

Se uma redefinição de senha não for uma opção para você, porque, por exemplo, o usuário foi excluído, você poderá optar por ignorar as detecções de risco do usuário.

Quando você clica em **Descartar riscos ao usuário**, todos os eventos são fechados e o usuário afetado não está mais em risco. No entanto, como esse método não tem um impacto sobre a senha existente, ele não deixa a identidade relacionada em um estado seguro novamente. 

### <a name="close-individual-risk-detections-manually"></a>Fechar detecções de risco individuais manualmente

Você pode fechar manualmente as detecções de risco individuais. Fechando as detecções de risco manualmente, você pode diminuir o nível de risco do usuário. Normalmente, as detecções de risco são fechadas manualmente em resposta a uma investigação relacionada. Por exemplo, quando conversar com um usuário revela que uma detecção de risco ativa não é mais necessária. 
 
Ao fechar as detecções de risco manualmente, você pode optar por executar qualquer uma das seguintes ações para alterar o status de uma detecção de risco:

- Confirmar o usuário comprometido
- Ignorar o risco de usuário
- Confirmar entrada segura
- Confirmar entrada comprometida

## <a name="unblocking-users"></a>Desbloqueando usuários

Um administrador pode optar por bloquear uma entrada com base em suas políticas de risco ou investigações. Um bloqueio pode ocorrer com base na entrada ou no risco do usuário.

### <a name="unblocking-based-on-user-risk"></a>Desbloqueio com base no risco do usuário

Para desbloquear uma conta bloqueada devido ao risco do usuário, os administradores têm as seguintes opções:

1. **Redefinir senha** - você pode redefinir a senha do usuário.
1. **Ignorar risco do usuário** – a política de risco do usuário bloqueia um usuário se o nível de risco do usuário configurado para bloquear o acesso tiver sido atingido. Você pode reduzir o nível de risco de um usuário ignorando o risco do usuário ou fechando manualmente as detecções de risco relatadas.
1. **Excluir o usuário da política** – se você acredita que a configuração atual da sua política de entrada está causando problemas para usuários específicos, você pode excluir os usuários dele. Para obter mais informações, consulte a seção exclusões no artigo [como configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Desabilitar política** - se você achar que sua configuração de política está causando problemas para todos os usuários, poderá desabilitar a política. Para obter mais informações, consulte o artigo [como configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Desbloqueio com base no risco de entrada

Para desbloquear uma conta baseada em risco de entrada, os administradores têm as seguintes opções:

1. **Entrada de um local ou dispositivo familiar** – uma razão comum para entradas suspeitas bloqueadas são as tentativas de entrada de locais ou de dispositivos desconhecidos. Os usuários podem determinar rapidamente se esse motivo é o motivo do bloqueio, tentando entrar de um local ou dispositivo familiar.
1. **Excluir o usuário da política** – se você acredita que a configuração atual da sua política de entrada está causando problemas para usuários específicos, você pode excluir os usuários dele. Para obter mais informações, consulte a seção exclusões no artigo [como configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Desabilitar política** - se você achar que sua configuração de política está causando problemas para todos os usuários, poderá desabilitar a política. Para obter mais informações, consulte o artigo [como configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md).

## <a name="powershell-preview"></a>PowerShell (versão prévia)

Ao usar o módulo de versão prévia do SDK do Microsoft Graph PowerShell, as organizações podem gerenciar riscos usando o PowerShell. Os módulos de visualização e o código de exemplo podem ser encontrados no [repositório GitHub do Azure ad](https://github.com/AzureAD/IdentityProtectionTools). 

O `Invoke-AzureADIPDismissRiskyUser.ps1` script incluído no repositório permite que as organizações descartam todos os usuários arriscados em seu diretório.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview-identity-protection.md).
