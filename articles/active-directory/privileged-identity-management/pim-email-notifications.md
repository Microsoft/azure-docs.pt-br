---
title: Notificações por email no PIM-Azure Active Directory | Microsoft Docs
description: Descreve as notificações por email no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57a44a5a6fa9a631604d92419fd8f5ebcce50a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394723"
---
# <a name="email-notifications-in-pim"></a>Notificações por email no PIM

Privileged Identity Management (PIM) permite que você saiba quando eventos importantes ocorrem em sua organização do Azure Active Directory (Azure AD), como quando uma função é atribuída ou ativada. Privileged Identity Management mantém você informado enviando-lhe e outros participantes notificações por email. Adicionalmente, esses emails podem incluir links para tarefas relevantes como ativar ou renovar uma função. Este artigo descreve a aparência desses emails, quando são enviados e quem recebe os emails.

## <a name="sender-email-address-and-subject-line"></a>Endereço de email do remetente e linha do assunto

Os emails enviados de Privileged Identity Management para as funções de recurso do Azure AD e do Azure têm o seguinte endereço de email do remetente:

- Endereço de email:  **Azure-noresponder \@ Microsoft.com**
- Nome de exibição: Microsoft Azure

Esses emails incluem um prefixo **PIM** na linha de assunto. Aqui está um exemplo:

- PIM: Alain Charon atribuiu permanentemente a função de leitor de backup

## <a name="notifications-for-azure-ad-roles"></a>Notificações para funções do Azure AD

Privileged Identity Management envia emails quando os seguintes eventos ocorrem para funções do Azure AD:

- Quando uma ativação de função com privilégios está com aprovação pendente
- Quando uma solicitação de ativação de função com privilégios é concluída
- Quando Azure AD Privileged Identity Management está habilitado

Quem recebe esses emails para as funções do Azure AD depende da função, do evento e da configuração de notificações:

| Usuário | Ativação de função está pendente de aprovação | A solicitação de ativação de função está concluída | O PIM está habilitado |
| --- | --- | --- | --- |
| Administrador de função com privilégios</br>(Ativado/Qualificado) | Sim</br>(somente se nenhum aprovador explícito for especificado) | Sim* | Sim |
| Administrador de Segurança</br>(Ativado/Qualificado) | Não | Sim* | Sim |
| Administrador global</br>(Ativado/Qualificado) | Não | Sim* | Sim |

\*Se as [**configurações de** Notificações](pim-how-to-change-default-settings.md#notifications) estiver definida como **Habilitar**.

A seguir, é mostrado um email de exemplo enviado quando um usuário ativa uma função do Azure AD para a organização fictícia Contoso.

![Novo Privileged Identity Management email para funções do Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Email de resumo semanal Privileged Identity Management para funções do Azure AD

Um email de resumo Privileged Identity Management semanal para funções do Azure AD é enviado para administradores de função com privilégios, administradores de segurança e administradores globais que habilitaram o Privileged Identity Management. Este email semanal fornece um instantâneo de Privileged Identity Management atividades para a semana, bem como atribuições de função com privilégios. Ele só está disponível para organizações do Azure AD na nuvem pública. Aqui está um exemplo de e-mail:

![Email de resumo semanal Privileged Identity Management para funções do Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

O email inclui quatro blocos:

| Tile | Descrição |
| --- | --- |
| **Usuários ativados** | Número de vezes que os usuários ativaram sua função qualificada dentro da organização. |
| **Usuários tornados permanentes** | Número de vezes que usuários com uma atribuição qualificada tornam-se permanentes. |
| **Atribuições de função no Privileged Identity Management** | Número de vezes que os usuários recebem uma função qualificada dentro Privileged Identity Management. |
| **Atribuições de função fora do PIM** | Número de vezes que os usuários recebem uma função permanente fora do Privileged Identity Management (dentro do Azure AD). |

A **visão geral da seção de funções principais** lista as cinco principais funções em sua organização com base no número total de administradores permanentes e qualificados para cada função. O link **Executar ação** abre o [assistente do PIM](pim-security-wizard.md), onde é possível converter administradores permanentes em administradores qualificados em lotes.

## <a name="email-timing-for-activation-approvals"></a>Tempo de email para aprovações de ativação

Quando os usuários ativam sua função e a configuração de função requer aprovação, os aprovadores receberão dois emails para cada aprovação:

- Solicitação para aprovar ou negar a solicitação de ativação do usuário (enviada pelo mecanismo de aprovação de solicitação)
- A solicitação do usuário é aprovada (enviada pelo mecanismo de aprovação de solicitação)

Além disso, os administradores globais e os administradores de função com privilégios recebem um email para cada aprovação:

- A função do usuário é ativada (enviada por Privileged Identity Management)

Os dois primeiros emails enviados pelo mecanismo de aprovação de solicitação podem ser atrasados. Atualmente, 90% dos emails levam de três a dez minutos, mas para clientes de 1%, pode ser muito mais demorado, até quinze minutos.

Se uma solicitação de aprovação for aprovada no portal do Azure antes de o primeiro email ser enviado, o primeiro email não será disparado e outros Aprovadores não serão notificados por email da solicitação de aprovação. Pode parecer que ele não recebe um email, mas é o comportamento esperado.

## <a name="pim-emails-for-azure-resource-roles"></a>Emails do PIM para funções de recurso do Azure

Privileged Identity Management envia emails aos proprietários e aos administradores de acesso do usuário quando os seguintes eventos ocorrem para as funções de recurso do Azure:

- Quando uma atribuição de função estiver com aprovação pendente
- Quando uma função for atribuída
- Quando uma função estiver prestes a expirar
- Quando uma função for qualificada para estender
- Quando uma função estiver sendo renovada por um usuário final
- Quando uma solicitação de ativação de função for concluída

Privileged Identity Management envia emails aos usuários finais quando os seguintes eventos ocorrem para as funções de recurso do Azure:

- Quando uma função for atribuída ao usuário
- Quando a função de um usuário expirar
- Quando a função do usuário for estendida
- Quando a solicitação de ativação de função de um usuário for concluída

A seguir, é mostrado um email de exemplo enviado quando um usuário recebe uma função de recurso do Azure para a organização fictícia Contoso.

![Novo Privileged Identity Management email para funções de recurso do Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Próximas etapas

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Aprovar ou negar solicitações para funções do Azure AD no Privileged Identity Management](azure-ad-pim-approval-workflow.md)
