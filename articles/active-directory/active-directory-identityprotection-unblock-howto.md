---
title: "Azure Active Directory Identity Protection - como desbloquear usuários | Microsoft Docs"
description: "Saiba como desbloquear usuários que foram bloqueados por uma política do Azure Active Directory Identity Protection."
services: active-directory
keywords: "azure active directory identity protection, desbloquear usuário"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: ce6b2805e7281dff7752a73ada86be11d7e01fc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory Identity Protection - como desbloquear usuários
Com o Azure Active Directory Identity Protection, você pode configurar políticas para bloquear os usuários se as condições configuradas forem satisfeitas. Normalmente, um usuário bloqueado entra em contato com o suporte técnico para ser desbloqueado. Este tópico explica as etapas que você pode executar para desbloquear um usuário bloqueado.

## <a name="determine-the-reason-for-blocking"></a>Determinar o motivo do bloqueio
Como uma primeira etapa para desbloquear um usuário, você precisa determinar o tipo de política que bloqueou o usuário porque as próximas etapas dependerão dela.
Com o Azure Active Directory Identity Protection, um usuário pode ser bloqueado ou por uma política de risco de entrada ou por uma política de risco de usuário.

Você pode obter o tipo de política que bloqueou um usuário do título na caixa de diálogo apresentado ao usuário durante uma tentativa de entrada:

| Política | Diálogo do usuário |
| --- | --- |
| Risco de entrada |![Entrada bloqueada](./media/active-directory-identityprotection-unblock-howto/02.png) |
| Risco do usuário |![Conta bloqueada](./media/active-directory-identityprotection-unblock-howto/104.png) |

Um usuário bloqueado por:

* Uma política de risco de entrada também é conhecida como entrada suspeita
* Uma política de risco de usuário também é conhecida como uma conta em risco

## <a name="unblocking-suspicious-sign-ins"></a>Desbloqueio de entradas suspeitas
Para desbloquear uma entrada suspeita, você tem as seguintes opções:

1. **Entrada de um local ou dispositivo familiar** - uma razão comum para entradas suspeitas bloqueadas são as tentativas de entrada de locais ou de dispositivos desconhecidos. Os usuários podem determinar rapidamente se esse é o motivo do bloqueio, tentando entrar de um dispositivo ou local familiar.
2. **Exclusão da política** - se você acha que a configuração atual de sua política de entrada está causando problemas para usuários específicos, pode excluir os usuários dela. Consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.
3. **Desabilitar política** - se você achar que sua configuração de política está causando problemas para todos os usuários, poderá desabilitar a política. Consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.

## <a name="unblocking-accounts-at-risk"></a>Desbloqueio de contas em risco
Para desbloquear uma conta em risco, você tem as seguintes opções:

1. **Redefinir senha** - você pode redefinir a senha do usuário. Veja [redefinição manual de senha segura](active-directory-identityprotection.md#manual-secure-password-reset) para obter mais detalhes.
2. **Descartar todos os eventos de risco** - a política de risco de usuário bloqueará um usuário se o nível de risco de usuário configurado para o bloqueio de acesso tiver sido atingido. Você pode reduzir o nível de risco de um usuário ao fechar manualmente os eventos de risco relatados. Para obter mais detalhes, veja [fechando eventos de risco manualmente](active-directory-identityprotection.md#closing-risk-events-manually).
3. **Exclusão da política** - se você acha que a configuração atual de sua política de entrada está causando problemas para usuários específicos, pode excluir os usuários dela. Consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.
4. **Desabilitar política** - se você achar que sua configuração de política está causando problemas para todos os usuários, poderá desabilitar a política. Consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
 Você deseja saber mais sobre o Azure AD Identity Protection? Confira [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
