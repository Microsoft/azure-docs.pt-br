---
title: Agendamentos de manutenção do Azure (versão prévia) | Microsoft Docs
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção programada necessários que o serviço do Azure SQL Data Warehouse usa para implantar novos recursos, atualizações e correções.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: f0b9b59ec09445a170d1f93181c2b432eafb60bf
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839977"
---
# <a name="view-a-maintenance-schedule"></a>Exibir um agendamento de manutenção 

## <a name="portal"></a>Portal

Por padrão, todas as instâncias do Azure SQL Data Warehouse criadas recentemente têm uma janela de manutenção de primários e secundários de oito horas aplicada durante a implantação. Você pode alterar o Windows assim que a implantação for concluída. Nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas sem notificação prévia.

Para exibir o agendamento de manutenção que foi aplicado ao seu data warehouse, conclua as seguintes etapas:

1.  Entre no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja exibir. 
3.  O data warehouse selecionado abre a folha de visão geral. O agendamento de manutenção é aplicado para o data warehouse é exibida abaixo **agendamento de manutenção**.

![Folha de visão geral](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta do log.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Criando e Gerenciar Grupo de ações.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure.


