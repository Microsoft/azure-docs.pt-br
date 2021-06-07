---
title: Manutenção agendada-banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Este artigo descreve o recurso de manutenção agendada no banco de dados do Azure para PostgreSQL-Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027534"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Manutenção agendada no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

Banco de dados do Azure para PostgreSQL-hiperescala (Citus) faz manutenção periódica para manter seu banco de dados gerenciado seguro, estável e atualizado.  Durante a manutenção, todos os nós no grupo de servidores obtêm novos recursos, atualizações e patches.

Os principais recursos da manutenção agendada para hiperescala (Citus) são:

* As atualizações são aplicadas ao mesmo tempo em todos os nós no grupo de servidores
* As notificações sobre a manutenção futura são lançadas na integridade do serviço do Azure cinco dias de antecedência
* Normalmente, há pelo menos 30 dias entre eventos de manutenção bem-sucedidos para um grupo de servidores

## <a name="notification-about-upcoming-maintenance"></a>Notificação sobre a manutenção futura

As notificações sobre a manutenção agendada futura são postadas na integridade do serviço do Azure e podem ser:

* Enviado por email para um endereço específico
* Enviado por email para uma função de Azure Resource Manager
* Enviado em uma mensagem de texto (SMS) para dispositivos móveis
* Envio por push como notificação para um aplicativo do Azure
* Entregue como mensagem de voz

> [!IMPORTANT]
> Normalmente, há pelo menos 30 dias entre os eventos de manutenção agendados bem-sucedidos para um grupo de servidores.
>
> No entanto, no caso de uma atualização de emergência crítica, como uma vulnerabilidade grave, a janela de notificação pode ter menos de cinco dias. A atualização crítica pode ser aplicada ao seu servidor, mesmo se uma manutenção agendada bem-sucedida tiver sido realizada nos últimos 30 dias.

Se a manutenção falhar ou for cancelada, o sistema criará uma notificação.
Ele tentará realizar a manutenção novamente de acordo com as configurações de agendamento atuais e notificará cinco dias antes do próximo evento de manutenção.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [obter notificações sobre a manutenção futura](../service-health/service-notifications.md) usando a integridade do serviço do Azure
* Saiba como [configurar alertas sobre eventos de manutenção agendados futuros](../service-health/resource-health-alert-monitor-guide.md)
