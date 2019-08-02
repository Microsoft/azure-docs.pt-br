---
title: Notificações do Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como as notificações dão suporte às suas atividades de investigação.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dcc7d2d03b3c4a3f4c8c772f38aa3785080d986
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335356"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações do Azure Active Directory Identity Protection

O Azure AD Identity Protection envia dois tipos de emails de notificação automatizados para ajudar você a gerenciar o risco do usuário e eventos de risco:

- Email de usuários em risco detectado
- Email de resumo semanal

Este artigo fornece uma visão geral de ambos os emails de notificação.

## <a name="users-at-risk-detected-email"></a>Email de usuários em risco detectado

Em resposta a uma conta detectada em risco, o Azure AD Identity Protection gera um alerta de email com **Usuários em risco detectado** como assunto. O email inclui um link para o relatório **[Usuários sinalizados por risco](../reports-monitoring/concept-user-at-risk.md)** . Como prática recomendada, você deve investigar imediatamente os usuários em risco.

A configuração para esse alerta permite especificar em qual nível de risco de usuário você deseja que o alerta seja gerado. O email será gerado quando o nível de risco do usuário atingir o que você especificou; no entanto, você não receberá novos usuários em risco os alertas de email detectados para esse usuário depois que eles forem movidos para esse nível de risco do usuário. Por exemplo, se você definir a política para alertar sobre o risco de usuário médio e seu usuário João passar para risco médio, você receberá os usuários em risco o email detectado para João. No entanto, você não receberá um segundo usuário em risco detectado alerta se João passar para alto risco ou tiver eventos de risco adicionais.

![Email de usuários em risco detectado](./media/notifications/01.png)

### <a name="configuration"></a>Configuração

Como administrador, você pode definir:

- **O nível de risco do usuário que dispara a geração desse email** -por padrão, o nível de risco é definido como risco "alto".
- **Os destinatários deste email** -Por padrão, os destinatários incluem todos os administradores globais. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.  

Para abrir a caixa de diálogo correspondente, clique em **Alertas** na seção **Configurações** da página **Proteção da Identidade**.

![Email de usuários em risco detectado](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>Email de resumo semanal

O email de resumo semanal contém um resumo dos novos eventos de risco.  
Ele inclui:

- Usuários em risco
- Atividades suspeitas
- Vulnerabilidades detectadas
- Links para os relatórios relacionados no Identity Protection

    ![Correção](./media/notifications/400.png "Correção")

### <a name="configuration"></a>Configuração

Como administrador, é possível desativar o envio de um email de resumo semanal.

![Riscos de usuário](./media/notifications/62.png "Riscos de usuário")

Para abrir a caixa de diálogo correspondente, clique em **Resumo Semanal** na seção **Configurações** da página **Proteção da Identidade**.

![Email de usuários em risco detectado](./media/notifications/04.png)

## <a name="see-also"></a>Consulte também

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
