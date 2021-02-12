---
title: Processo de solicitação & notificações-gerenciamento de direitos do Azure AD
description: Saiba mais sobre o processo de solicitação de um pacote do Access e quando as notificações por email são enviadas no gerenciamento de direitos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746228"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Solicitar notificações de processo e email no gerenciamento de direitos do Azure AD

Quando um usuário envia uma solicitação para um pacote do Access, um processo começa a entregar essa solicitação de acesso. O gerenciamento de direitos do Azure AD envia notificações por email a Aprovadores e solicitantes quando ocorrem eventos de chave durante o processo. Este artigo descreve o processo de solicitação e as notificações de email que são enviadas.

## <a name="request-process"></a>Processo de solicitação

Um usuário que precisa acessar um pacote do Access pode enviar uma solicitação de acesso. Dependendo da configuração da política, a solicitação pode exigir uma aprovação. Quando uma solicitação é aprovada, um processo começa a atribuir o acesso do usuário a cada recurso no pacote do Access. O diagrama a seguir mostra uma visão geral do processo e os diferentes Estados:

![Diagrama de processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | Descrição |
| --- | --- |
| Enviado | O usuário envia uma solicitação. |
| Aprovação pendente | Se a política para um pacote de acesso exigir aprovação, uma solicitação será movida para aprovação pendente. |
| Expirado | Se nenhum Aprovador aprovar uma solicitação dentro do tempo limite da solicitação de aprovação, a solicitação expirará. Para tentar novamente, o usuário precisará reenviar a solicitação. |
| Negadas | O aprovador nega uma solicitação. |
| Aprovado | O aprovador aprova uma solicitação. |
| Fornecimento | O usuário **não** recebeu acesso a todos os recursos no pacote de acesso. Se esse for um usuário externo, talvez o usuário ainda não tenha acessado o diretório de recursos. Eles também podem não ter aceitado o prompt de consentimento. |
| Entregue | O usuário recebeu acesso a todos os recursos no pacote de acesso. |
| Acesso estendido | Se as extensões forem permitidas na política, o usuário estendeu a atribuição. |
| Acesso expirado | O acesso do usuário ao pacote de acesso expirou. Para obter acesso novamente, o usuário precisará enviar uma solicitação. |

## <a name="email-notifications"></a>Notificações por email

Se você for um aprovador, receberá notificações por email quando precisar aprovar uma solicitação de acesso. Você também receberá notificações quando uma solicitação de acesso tiver sido concluída. Você também receberá notificações por email que indicam o status da sua solicitação se você for um solicitante.

Os diagramas a seguir mostram quando essas notificações de email são enviadas para os aprovadores ou para o solicitante. Faça referência à [tabela de notificações de email](entitlement-management-process.md#email-notifications-table) para localizar o número correspondente às notificações de email exibidas nos diagramas.

### <a name="first-approvers-and-alternate-approvers"></a>Primeiros Aprovadores e aprovadores alternativos
O diagrama a seguir mostra a experiência dos primeiros Aprovadores e aprovadores alternativos e as notificações por email que eles recebem durante o processo de solicitação:

![Fluxo de processo de aprovadores do primeiro e alternativo](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Solicitantes
O diagrama a seguir mostra a experiência de solicitantes e as notificações por email recebidas durante o processo de solicitação:

![Fluxo do processo do solicitante](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>aprovação de dois estágios
O diagrama a seguir mostra a experiência dos aprovadores estágio-1 e estágio 2 e as notificações de email que eles recebem durante o processo de solicitação:

![fluxo do processo de aprovação de 2 estágios](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabela de notificações de email
A tabela a seguir fornece mais detalhes sobre cada uma dessas notificações por email. Para gerenciar esses emails, você pode usar regras. Por exemplo, no Outlook, você pode criar regras para mover os emails para uma pasta se o assunto contiver palavras desta tabela:

| # | Assunto do email | Quando enviado | Enviado a |
| --- | --- | --- | --- |
| 1 | Ação necessária: aprovar ou negar a solicitação encaminhada por *[data]* | Este email será enviado aos aprovadores alternativos do estágio 1 (depois que a solicitação for escalonada) para executar uma ação. | Etapas de aprovadores alternativos do estágio 1 |
| 2 | Ação necessária: aprovar ou negar solicitação por *[data]* | Este email será enviado para o primeiro aprovador, se o escalonamento estiver desabilitado, para executar uma ação. | Primeiro aprovador |
| 3 | Lembrete: aprovar ou negar a solicitação em *[data]* para *[solicitante]* | Este email de lembrete será enviado para o primeiro aprovador, se o escalonamento estiver desabilitado. O email solicita que eles executem uma ação se não tiverem. | Primeiro aprovador |
| 4 | Aprovar ou negar a solicitação em *[time]* em *[data]* | Este email será enviado para o primeiro aprovador (se o escalonamento estiver habilitado) para executar uma ação. | Primeiro aprovador |
| 5 | Lembrete necessário da ação: aprovar ou negar a solicitação por *[data]* para *[solicitante]* | Este email de lembrete será enviado para o primeiro aprovador, se o escalonamento estiver habilitado. O email solicita que eles executem uma ação se não tiverem. | Primeiro aprovador |
| 6 | A solicitação expirou para *[access_package]* | Este email será enviado para os aprovadores alternativos do primeiro aprovador e do estágio 1 depois que a solicitação tiver expirado. | Primeiro aprovador, Aprovadores alternativos do estágio 1 |
| 7 | Solicitação aprovada para *[solicitante]* para *[access_package]* | Este email será enviado para o primeiro aprovador e o estágio 1 aprovadores alternativos mediante a conclusão da solicitação. | Primeiro aprovador, Aprovadores alternativos do estágio 1 |
| 8 | Solicitação aprovada para *[solicitante]* para *[access_package]* | Este email será enviado aos aprovadores alternativos do primeiro aprovador e do estágio 1 de uma solicitação de dois estágios quando a solicitação Stage-1 for aprovada. | Primeiro aprovador, Aprovadores alternativos do estágio 1 |
| 9 | Solicitação negada para *[access_package]* | Este email será enviado para o solicitante quando a solicitação for negada | Solicitante |
| 10 | Sua solicitação expirou para *[access_package]* | Este email será enviado ao solicitante no final de uma solicitação de único ou de dois estágios. O email notifica o solicitante de que a solicitação expirou. | Solicitante |
| 11 | Ação necessária: aprovar ou negar solicitação por *[data]* | Este email será enviado para o segundo aprovador, se o escalonamento estiver desabilitado, para executar uma ação. | Segundo aprovador |
| 12 | Lembrete necessário da ação: aprovar ou negar a solicitação por *[data]* | Este email de lembrete será enviado para o segundo aprovador, se o escalonamento estiver desabilitado. A notificação solicita que eles executem uma ação caso ainda não tenham feito isso. | Segundo aprovador |
| 13 | Ação necessária: aprovar ou negar a solicitação em *[data]* para *[solicitante]* | Este email será enviado para o segundo aprovador, se o escalonamento estiver habilitado, para executar uma ação. | Segundo aprovador |
| 14 | Lembrete necessário da ação: aprovar ou negar a solicitação por *[data]* para *[solicitante]* | Este email de lembrete será enviado para o segundo aprovador, se o escalonamento estiver habilitado. A notificação solicita que eles executem uma ação caso ainda não tenham feito isso. | Segundo aprovador |
| 15 | Ação necessária: aprovar ou negar a solicitação encaminhada por *[data]* | Este email será enviado aos aprovadores alternativos do estágio 2, se o escalonamento estiver habilitado, para executar uma ação. | Aprovadores alternativos de estágio 2 |
| 16 | Solicitação aprovada para *[solicitante]* para *[access_package]* | Este email será enviado para o segundo aprovador e para o estágio 2 aprovadores alternativos após a aprovação da solicitação. | Segundo aprovador, Aprovadores alternativos de estágio 2 |
| 17 | Uma solicitação expirou para *[access_package]* | Este email será enviado ao segundo Aprovador ou a aprovadores alternativos, depois que a solicitação expirar. | Segundo aprovador, Aprovadores alternativos de estágio 2 |
| 18 | Agora você tem acesso ao *[access_package]* | Este email será enviado aos usuários finais para começar a usar seu acesso. | Solicitante |
| 19 | Estender o acesso para *[access_package]* por *[data]* | Este email será enviado aos usuários finais antes de seu acesso expirar. | Solicitante |
| 20 | O acesso foi encerrado para *[access_package]* | Este email será enviado aos usuários finais após o acesso expirar. | Solicitante |

### <a name="access-request-emails"></a>Emails de solicitação de acesso

Quando um solicitante envia uma solicitação de acesso para um pacote de acesso configurado para exigir aprovação, todos os aprovadores adicionados à política receberão uma notificação por email com detalhes da solicitação. Os detalhes no email incluem: nome da organização do solicitante e justificativa de negócios; e a data de início e de término do acesso solicitado (se fornecido). Os detalhes também incluirão quando a solicitação foi enviada e quando a solicitação expirará.

O email inclui um link que os aprovadores podem clicar para acessar o meu acesso para aprovar ou negar a solicitação de acesso. Aqui está um exemplo de notificação por email que é enviada para o primeiro Aprovador ou segundo aprovador (se a aprovação de 2 estágios estiver habilitada) para concluir uma solicitação de acesso:

![Aprovar solicitação para acessar email de pacote](./media/entitlement-management-shared/approver-request-email.png)

Os aprovadores também podem receber um email de lembrete. O email solicita que o aprovador tome uma decisão sobre a solicitação. Aqui está um exemplo de notificação por email que o aprovador recebe para lembrá-lo de agir:

![Email de solicitação de acesso ao lembrete](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Aprovadores alternativos solicitam emails

Se a configuração de aprovadores alternativos estiver habilitada e a solicitação ainda estiver pendente, ela será encaminhada. Os aprovadores alternativos receberão um email para aprovar ou negar a solicitação. Você pode habilitar os aprovadores alternativos no estágio-1 e no estágio-2. Aqui está um email de exemplo da notificação que os aprovadores alternativos recebem:

![Emails de solicitação de aprovadores alternativos](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

O aprovador e os aprovadores alternativos podem aprovar ou negar a solicitação.

### <a name="approved-or-denied-emails"></a>Emails aprovados ou negados

 Quando um Aprovador recebe uma solicitação de acesso enviada por um solicitante, ele pode aprovar ou negar a solicitação de acesso. O aprovador precisa adicionar uma justificativa de negócios para sua decisão. Aqui está um email de exemplo enviado aos Aprovadores e aprovadores alternativos depois que uma solicitação é aprovada:

![Solicitação aprovada para acessar o email do pacote](./media/entitlement-management-process/approver-request-email-approved.png)

Quando uma solicitação de acesso é aprovada e seu acesso é provisionado, uma notificação por email é enviada ao solicitante que agora tem acesso ao pacote de acesso. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando recebe acesso a um pacote de acesso:

![Email de solicitação de acesso do solicitante aprovado](./media/entitlement-management-process/requestor-email-approved.png)

Quando uma solicitação de acesso é negada, uma notificação por email é enviada ao solicitante. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando sua solicitação de acesso é negada:

![Email de solicitação negada do solicitante](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>emails de solicitação de acesso de aprovação de dois estágios

Se a aprovação de 2 estágios estiver habilitada, pelo menos dois aprovadores deverão aprovar a solicitação, uma de cada estágio, antes que o solicitante possa receber acesso.

Durante o estágio-1, o primeiro aprovador receberá o email de solicitação de acesso e tomará uma decisão. Se eles aprovarem a solicitação, todos os primeiros Aprovadores e aprovadores alternativos no estágio-1 (se o escalonamento estiver habilitado) receberão a notificação informando que o estágio-1 está concluído. Aqui está um email de exemplo da notificação que é enviada quando o estágio-1 é concluído:

![2-preparar email de solicitação de acesso](./media/entitlement-management-process/approver-request-email-2stage.png)

Depois que o primeiro ou os aprovadores alternativos aprovarem a solicitação no estágio-1, o estágio-2 começará. Durante o estágio-2, o segundo aprovador receberá o email de notificação de solicitação de acesso. Após o segundo Aprovador ou aprovadores alternativos no estágio-2 (se o escalonamento estiver habilitado) decidir aprovar ou negar a solicitação, os emails de notificação serão enviados para o primeiro e o segundo Aprovadores e todos os aprovadores alternativos no estágio-1 e no estágio 2, bem como o solicitante.

### <a name="expired-access-request-emails"></a>Emails de solicitação de acesso expirados

As solicitações de acesso poderão expirar se nenhum aprovador tiver aprovado ou negado a solicitação. 

Quando a solicitação atingir sua data de expiração configurada e expirar, ela não poderá mais ser aprovada ou negada pelos aprovadores. Aqui está um email de exemplo da notificação enviada para todas as primeiras, segunda (se a aprovação de 2 estágios estiver habilitada) e aprovadores alternativos:

![Email de solicitação de acesso de aprovadores expirados](./media/entitlement-management-process/approver-request-email-expired.png)

Uma notificação por email também é enviada ao solicitante, notificando que a solicitação de acesso expirou e que eles precisam reenviar a solicitação de acesso. O diagrama a seguir mostra a experiência do solicitante e as notificações por email que eles recebem quando solicitam estender o acesso:

![Solicitando fluxo de processo de acesso de extensão](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Aqui está um exemplo de notificação por email que é enviada para um solicitante quando sua solicitação de acesso expirou:

![Email de solicitação de acesso expirado do solicitante](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)
