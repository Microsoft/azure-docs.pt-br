---
title: Agendamentos de manutenção do Azure (versão prévia) | Microsoft Docs
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção programada necessários que o serviço do Azure SQL Data Warehouse usa para implantar novos recursos, atualizações e correções.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 03/13/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b97e27b86ecad1f7f87a6de4d43b09d69c167c6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075307"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Use agendas de manutenção para gerenciar atualizações e manutenção de serviços

Agendas de manutenção estão disponíveis em todas as regiões do Azure SQL Data Warehouse. Esse recurso integra as Notificações de Manutenção Planejada de Integridade do Serviço, o Monitor de Verificação de Funcionamento do Recurso e o serviço de agendamento de manutenção do Azure SQL Data Warehouse.

Você usa o agendamento de manutenção para escolher uma janela de horário quando for conveniente receber novos recursos, atualizações e patches. Você escolhe uma janela de manutenção primária e secundária dentro de um período de sete dias. Um exemplo é uma janela principal de sábado, das 22:00 h às 01:00 h, e uma segunda janela, das 19:00 h às 22:00 h. Se o SQL Data Warehouse não puder executar manutenção durante a janela de manutenção principal, ele tentará a manutenção novamente durante a janela de manutenção secundária. Manutenção do serviço pode ocorrer durante o primário e secundário windows. Para garantir conclusão rápida de todas as operações de manutenção, DW400(c) e os níveis inferiores de depósito de dados foi possível concluir a manutenção fora da janela de manutenção designada.

Todas as instâncias recém-criados do SQL Data Warehouse do Azure terão um agendamento de manutenção definida pelo sistema aplicada durante a implantação. O agendamento poderá ser editado assim que a implantação for concluída.

Cada janela de manutenção pode ser de três a oito horas. A manutenção pode ocorrer a qualquer momento dentro da janela. Você deve esperar uma breve perda de conectividade à medida que o serviço implanta novo código em seu data warehouse.

Para usar esse recurso, você precisará identificar uma janela primária e secundária dentro de intervalos de dia separado. Todas as operações de manutenção devem terminar dentro das janelas de manutenção programadas. Nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas sem notificação prévia. Se o data warehouse estiver em pausa durante a manutenção agendada, ele será atualizado durante a operação de retomada.  

## <a name="alerts-and-monitoring"></a>Alertas e monitoramento

A integração com notificações do Service Health e o Monitor de verificação de integridade do recurso permite que os clientes se mantenham informados sobre a atividade de manutenção iminente. A nova automação aproveita o Azure Monitor. Você pode decidir como deseja ser notificado sobre eventos de manutenção iminentes. Decida também quais fluxos automatizados podem ajudar a gerenciar o tempo de inatividade e minimizar o impacto em suas operações.

Uma notificação antecipada de 24 horas que precede todos os eventos de manutenção, com a exceção atual de DW400c e os níveis inferiores. Para minimizar o tempo de inatividade da instância, certifique-se de que o data warehouse não tenha transações de longa execução antes do período de manutenção escolhido. Quando a manutenção é iniciada, todas as sessões ativas serão canceladas. Transações confirmadas por não serão revertidas e o data warehouse será experiência curta perda de conectividade. Você será notificado imediatamente após a conclusão da manutenção em seu data warehouse.

Se você recebeu uma notificação prévia de que a manutenção ocorrerá, mas o SQL Data Warehouse não pode executar manutenção durante esse período, você receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção agendada.

Todos os eventos de manutenção ativos são exibidos na seção **Service Health - Planned Maintenance**. O histórico de integridade do serviço inclui um registro completo de eventos passados. Você pode monitorar a manutenção por meio do painel do portal de verificação do Funcionamento do Azure Health durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade do cronograma de manutenção

Mesmo que o agendamento de manutenção não esteja disponível em sua região selecionada, você poderá visualizar e editar sua programação de manutenção a qualquer momento. Quando o agendamento de manutenção estiver disponível em sua região, o agendamento identificado ficará imediatamente ativo em seu data warehouse.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](viewing-maintenance-schedule.md) sobre como exibir um agendamento de manutenção.
- [Saiba mais](changing-maintenance-schedule.md) sobre como alterar um agendamento de manutenção.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta do log.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Criando e Gerenciar Grupo de ações.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure.
