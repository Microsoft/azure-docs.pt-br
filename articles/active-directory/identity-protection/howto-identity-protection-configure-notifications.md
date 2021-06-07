---
title: Notificações do Azure Active Directory Identity Protection
description: Saiba como as notificações dão suporte às suas atividades de investigação.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9536cf41add73f494bfff451c201d36e951864e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997657"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações do Azure Active Directory Identity Protection

Azure AD Identity Protection envia dois tipos de emails de notificação automatizados para ajudá-lo a gerenciar as detecções de risco e risco do usuário:

- Email de usuários em risco detectado
- Email de resumo semanal

Este artigo fornece uma visão geral de ambos os emails de notificação.

## <a name="users-at-risk-detected-email"></a>Email de usuários em risco detectado

Em resposta a uma conta detectada em risco, o Azure AD Identity Protection gera um alerta de email com **Usuários em risco detectado** como assunto. O email inclui um link para o relatório **[Usuários sinalizados por risco](./overview-identity-protection.md)**. Como prática recomendada, você deve investigar imediatamente os usuários em risco.

A configuração para esse alerta permite especificar em qual nível de risco de usuário você deseja que o alerta seja gerado. O email será gerado quando o nível de risco do usuário atingir o que você especificou. Por exemplo, se você definir a política para alertar sobre o risco de usuário médio e a pontuação de risco de usuário de João do usuário passar para o risco médio devido a um risco de entrada em tempo real, você receberá os usuários em risco de email detectado. Se o usuário tiver as detecções de risco subsequentes que fazem com que o cálculo do nível de risco do usuário seja o nível de risco especificado (ou superior), você receberá emails de risco detectados pelo usuário quando a pontuação de risco do usuário for recalculada. Por exemplo, se um usuário passar para o risco médio em 1º de Janeiro, você receberá uma notificação por email se suas configurações estiverem definidas para alertar sobre o risco médio. Se esse mesmo usuário tiver outra detecção de risco em 5 de janeiro que também seja de risco médio e a pontuação de risco do usuário for recalculada e ainda for média, você receberá outra notificação por email. 

No entanto, uma notificação de email adicional só será enviada se a hora em que a detecção de risco ocorreu (que causou a alteração no nível de risco do usuário) for mais recente do que quando o último email foi enviado. Por exemplo, um usuário entra em 1º de janeiro às 17h e não há risco em tempo real (o que significa que nenhum email seria gerado devido a essa entrada). Dez minutos depois, às 5:10 A.M., o mesmo usuário entra novamente e tem alto risco em tempo real, fazendo com que o nível de risco do usuário seja movido para alto e um email a ser enviado. Em seguida, às 5:15 A.M., a pontuação de risco offline para a entrada original às 17:00 muda para alto risco devido ao processamento de risco offline. Um usuário adicional sinalizado para o email de risco não seria enviado, pois a hora da primeira entrada era anterior à segunda entrada que já disparou uma notificação por email.

Para evitar uma sobrecarga de emails, você receberá apenas um email em um período de 5 segundos. Esse atraso significa que, se vários usuários mudarem para o nível de risco especificado durante o mesmo período de 5 segundos, agregaremos e enviaremos um email para representar a alteração no nível de risco para todos eles.

Se sua organização tiver habilitado a AutoCorreção, conforme descrito no artigo, as [experiências do usuário com Azure ad Identity Protection](concept-identity-protection-user-experience.md) há uma chance de que o usuário possa corrigir seu risco antes que você tenha a oportunidade de investigar. Você pode ver usuários arriscados e entradas arriscadas que foram corrigidos adicionando "remediado" ao filtro de estado de **risco** nos relatórios de **usuários arriscados** ou de **entradas arriscadas** .

![Email de usuários em risco detectado](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configurar usuários em risco os alertas detectados

Como administrador, você pode definir:

- **O nível de risco do usuário que dispara a geração desse email** -por padrão, o nível de risco é definido como risco "alto".
- **Os destinatários deste email** -os usuários nas funções administrador global, administrador de segurança ou leitor de segurança são adicionados automaticamente a essa lista. Tentamos enviar emails para os primeiros 20 membros de cada função. Se um usuário estiver registrado no PIM para elevar a uma dessas funções sob demanda, **ele só receberá emails se eles forem elevados no momento em que o email for enviado**.
   - Opcionalmente, você pode **Adicionar um email personalizado aqui** os usuários definidos devem ter as permissões apropriadas para exibir os relatórios vinculados no portal do Azure.

Configure os usuários em risco email no **portal do Azure** em **Azure Active Directory**  >  **segurança**  >  **identidade** do Security  >  **usuários em risco detectado alertas**.

## <a name="weekly-digest-email"></a>Email de resumo semanal

O email de resumo semanal contém um resumo das novas detecções de risco.  
Ele inclui:

- Novos usuários arriscados detectados
- Novas entradas arriscadas detectadas (em tempo real)
- Links para os relatórios relacionados no Identity Protection

![Email de resumo semanal](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Os usuários nas funções administrador global, administrador de segurança ou leitor de segurança são adicionados automaticamente a essa lista. Tentamos enviar emails para os primeiros 20 membros de cada função. Se um usuário estiver registrado no PIM para elevar a uma dessas funções sob demanda, **ele só receberá emails se eles forem elevados no momento em que o email for enviado**

### <a name="configure-weekly-digest-email"></a>Configurar email de resumo semanal

Como administrador, você pode alternar o envio de um email de resumo semanal ou desativá-lo e escolher os usuários atribuídos para receber o email.

Configure o email de resumo semanal no **portal do Azure** em **Azure Active Directory**  >  **segurança identidade do Security**  >  **Protection**  >  **semanalmente Resumo**.

## <a name="see-also"></a>Veja também

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
