---
title: Solicitar notificações de processo e email no gerenciamento de direitos do Azure AD (versão prévia) – Azure Active Directory
description: Saiba mais sobre o processo de solicitação de um pacote do Access e quando as notificações por email são enviadas no gerenciamento de direitos de Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ad17f4c4d338e1d0b8118c1a172e0aaad7118e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489095"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Solicitar notificações de processo e email no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando um usuário envia uma solicitação para um pacote do Access, um processo é iniciado para entregar essa solicitação. O gerenciamento de direitos do Azure AD também envia notificações por email a Aprovadores e solicitantes quando ocorrem eventos de chave durante o processo.

Este artigo descreve o processo de solicitação e as notificações de email que são enviadas.

## <a name="request-process"></a>Processo de solicitação

Um usuário que precisa acessar um pacote do Access pode enviar uma solicitação de acesso. Dependendo da configuração da política, a solicitação pode exigir uma aprovação. Quando uma solicitação é aprovada, um processo começa a atribuir o acesso do usuário a cada recurso no pacote do Access. O diagrama a seguir mostra uma visão geral do processo e os diferentes Estados.

![Diagrama de processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | Descrição |
| --- | --- |
| Enviado | O usuário envia uma solicitação. |
| Aprovação pendente | Se a política para um pacote de acesso exigir aprovação, uma solicitação será movida para aprovação pendente. |
| Expirado | Se nenhum Aprovador aprovar uma solicitação dentro do tempo limite da solicitação de aprovação, a solicitação expirará. Para tentar novamente, o usuário precisará reenviar a solicitação. |
| Negado | O aprovador nega uma solicitação. |
| Aprovado | O aprovador aprova uma solicitação. |
| Entregando | O usuário **não** recebeu acesso a todos os recursos no pacote de acesso. Se esse for um usuário externo, o usuário ainda não acessou o diretório de recursos e aceitou o prompt de permissões. |
| Entregue | O usuário recebeu acesso a todos os recursos no pacote de acesso. |
| Acesso estendido | Se as extensões forem permitidas na política, o usuário estendeu a atribuição. |
| Acesso expirado | O acesso do usuário ao pacote de acesso expirou. Para obter acesso novamente, o usuário precisará enviar uma solicitação. |

## <a name="email-notifications"></a>Notificações por email

Se você for um aprovador, receberá notificações por email quando precisar aprovar uma solicitação de acesso e quando uma solicitação de acesso tiver sido concluída. Se você for um solicitante, receberá notificações por email que indicam o status de sua solicitação. O diagrama a seguir mostra quando essas notificações de email são enviadas.

![Processo de email de gerenciamento de direitos](./media/entitlement-management-process/email-notifications.png)

A tabela a seguir fornece mais detalhes sobre cada uma dessas notificações por email.

| # | Assunto do email | Quando enviado | Enviado para |
| --- | --- | --- | --- |
| 1 | Ação necessária: Examinar a solicitação de acesso de *[solicitante]* para *[pacote do Access]* por *[data]* | Quando um solicitante envia uma solicitação para um pacote de acesso | Todos os aprovadores |
| 2 | Ação necessária: Examinar a solicitação de acesso de *[solicitante]* para *[pacote do Access]* por *[data]* | X dias antes do tempo limite da solicitação de aprovação | Todos os aprovadores |
| 3 | Notificação de status: a solicitação de acesso *[solicitante]* do *[pacote de acesso]* expirou | Quando os aprovadores não aprovam ou negam uma solicitação de acesso dentro da duração da solicitação | Solicitante |
| 4 | Notificação de status: *[solicitante]* a solicitação de acesso ao *[pacote do Access]* foi concluída | Quando o primeiro aprovador aprova ou nega uma solicitação de acesso | Todos os aprovadores |
| 5 | Você teve o acesso negado ao *[pacote do Access]* | Quando um solicitante teve o acesso negado ao pacote de acesso | Solicitante |
| 6 | Agora você tem acesso ao *[pacote do Access]*  | Quando um solicitante tiver recebido acesso a todos os recursos no pacote de acesso | Solicitante |
| 7 | Seu acesso ao *[pacote do Access]* expira em X dia (s) | X dias antes que o acesso do solicitante ao pacote de acesso expire | Solicitante |
| 8 | Seu acesso ao *[pacote do Access]* expirou | Quando o acesso do solicitante a um pacote de acesso expira | Solicitante |

### <a name="access-request-emails"></a>Emails de solicitação de acesso

Quando um solicitante envia uma solicitação de acesso para um pacote de acesso configurado para exigir aprovação, todos os aprovadores configurados na política recebem uma notificação por email com detalhes da solicitação. Os detalhes incluem o nome do solicitante, organização, data de início e de término do acesso, se fornecido, justificativa de negócios, quando a solicitação foi enviada e quando a solicitação expirará. O email inclui um link em que os aprovadores podem aprovar ou negar a solicitação de acesso. Aqui está um exemplo de notificação por email que é enviada para um Aprovador quando um solicitante envia uma solicitação de acesso.

![Examinar o email de solicitação de acesso](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Emails aprovados ou negados

Os solicitantes são notificados quando sua solicitação de acesso é aprovada e está disponível para acesso ou quando sua solicitação de acesso é negada. Quando um Aprovador recebe uma solicitação de acesso enviada por um solicitante, ele pode aprovar ou negar a solicitação de acesso. O aprovador precisa adicionar uma justificativa de negócios para sua decisão.

Quando uma solicitação de acesso é aprovada, o gerenciamento de direitos inicia o processo de concessão do acesso do solicitante a cada um dos recursos no pacote de acesso. Depois que o solicitante tiver recebido acesso a todos os recursos no pacote de acesso, uma notificação por email será enviada ao solicitante de que sua solicitação de acesso foi aprovada e que agora terá acesso ao pacote de acesso. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando recebe acesso a um pacote de acesso.

Quando uma solicitação de acesso é negada, uma notificação por email é enviada ao solicitante. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando sua solicitação de acesso é negada.

### <a name="expired-access-request-emails"></a>Emails de solicitação de acesso expirados

Os solicitantes são notificados quando sua solicitação de acesso expirou. Quando um solicitante envia uma solicitação de acesso, a solicitação tem uma duração de solicitação após a qual ela expira. Se não houver Aprovadores que enviem uma decisão aprovar/negar, a solicitação continuará a permanecer em um estado de aprovação pendente. Quando a solicitação atinge sua duração de expiração configurada, a solicitação expira e não pode mais ser aprovada ou negada pelos aprovadores. Nesse caso, a solicitação entra em um estado expirado. Uma solicitação expirada não pode mais ser aprovada ou negada. Uma notificação por email é enviada ao solicitante que sua solicitação de acesso expirou e que precisa reenviar a solicitação de acesso. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando sua solicitação de acesso expirou.

![Email de solicitação de acesso expirado](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)
