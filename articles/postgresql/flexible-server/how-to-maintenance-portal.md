---
title: Banco de dados do Azure para PostgreSQL – servidor flexível (visualização)-manutenção agendada-portal do Azure
description: Saiba como definir as configurações de manutenção agendada para um servidor de banco de dados do Azure para PostgreSQL do portal do Azure.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: be6040b8b84a4b86746d62bd2f1c07f0ffea0a3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336285"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--flexible-server"></a>Gerenciar configurações de manutenção agendadas para o banco de dados do Azure para PostgreSQL – servidor flexível
 
Você pode especificar opções de manutenção para cada servidor flexível em sua assinatura do Azure. As opções incluem o agendamento de manutenção e as configurações de notificação para eventos de manutenção futuros e concluídos.

> [!IMPORTANT]
> Banco de dados do Azure para PostgreSQL-o servidor flexível está em versão prévia.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [banco de dados do Azure para PostgreSQL – servidor flexível](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Especificar opções de agendamento de manutenção
 
1. Na página do servidor PostgreSQL, no título **configurações** , escolha **manutenção** para abrir as opções de manutenção agendada.
2. A agenda padrão (gerenciada pelo sistema) é um dia aleatório da semana e uma janela de 60 minutos para manutenção iniciada entre 23h e 7h hora do servidor local. Se você quiser personalizar essa agenda, escolha **agendamento personalizado**. Em seguida, você pode selecionar um dia da semana preferencial e uma janela de 60 minutos para a hora de início da manutenção.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Notificações sobre eventos de manutenção agendada
 
Você pode usar a integridade do serviço do Azure para [exibir notificações](../../service-health/service-notifications.md) sobre a manutenção agendada futura e realizada em seu servidor flexível. Você também pode [Configurar](../../service-health/resource-health-alert-monitor-guide.md) alertas na integridade do serviço do Azure para obter notificações sobre eventos de manutenção.
 
## <a name="next-steps"></a>Próximas etapas  
 
* Saiba mais sobre [a manutenção agendada no banco de dados do Azure para PostgreSQL – servidor flexível](concepts-maintenance.md)
* Saiba mais sobre a [integridade do serviço do Azure](../../service-health/overview.md)
