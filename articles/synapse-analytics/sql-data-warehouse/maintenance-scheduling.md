---
title: Agendas de manutenção para o pool SQL Synapse
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção agendados necessários que o Azure Synapse Analytics usa para distribuir novos recursos, atualizações e patches.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 886e99d6f56a008c694b3bcb366cfb4049f3528e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590216"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Use agendas de manutenção para gerenciar atualizações e manutenção de serviços

O recurso de agendamento de manutenção integra as notificações de manutenção planejada de integridade do serviço, Resource Health verificação de monitor e serviço de agendamento de manutenção para o Synapse SQL pool (data warehouse) no Azure Synapse Analytics.

Você deve usar o agendamento de manutenção para escolher uma janela de tempo quando for conveniente receber novos recursos, atualizações e patches. Você precisará escolher uma janela de manutenção primária e uma secundária em um período de sete dias, cada janela deve estar dentro de intervalos de dias separados.

Por exemplo, você pode agendar uma janela primária de sábado 22:00 para domingo 01:00 e, em seguida, agendar uma janela secundária da quarta-feira 19:00 a 22:00. Se a manutenção não puder ser executada durante a janela de manutenção primária, ela tentará realizar a manutenção novamente durante a janela de manutenção secundária. A manutenção do serviço pode ocorrer durante as janelas primária e secundária. Para garantir a conclusão rápida de todas as operações de manutenção, DW400c e camadas de data warehouse inferiores podem concluir a manutenção fora de uma janela de manutenção designada.

Todas as instâncias de data warehouse criadas recentemente terão um agendamento de manutenção definido pelo sistema aplicado durante a implantação. O agendamento poderá ser editado assim que a implantação for concluída.

Embora uma janela de manutenção possa estar entre três e oito horas, isso não significa que o data warehouse ficará offline durante a duração. A manutenção pode ocorrer a qualquer momento dentro dessa janela e você deve esperar uma única desconexão durante esse período duradoura ~ 5 -6 minutos, pois o serviço implanta o novo código em seu data warehouse. DW400c e Lower podem apresentar várias perdas curtas na conectividade em vários momentos durante a janela de manutenção. Quando a manutenção é iniciada, todas as sessões ativas serão canceladas e as transações não confirmadas serão revertidas. Para minimizar o tempo de inatividade da instância, certifique-se de que o data warehouse não tenha transações de longa execução antes do período de manutenção escolhido.

Todas as operações de manutenção devem ser concluídas dentro das janelas de manutenção especificadas, a menos que seja necessário implantar uma atualização sensível ao tempo. Se o data warehouse estiver em pausa durante a manutenção agendada, ele será atualizado durante a operação de retomada. Você será notificado imediatamente após a conclusão da manutenção do data warehouse.

## <a name="alerts-and-monitoring"></a>Alertas e monitoramento

A integração com notificações do Service Health e o Monitor de verificação de integridade do recurso permite que os clientes se mantenham informados sobre a atividade de manutenção iminente. Essa automação aproveita o Azure Monitor. Você pode decidir como deseja ser notificado sobre eventos de manutenção iminentes. Além disso, você pode escolher quais fluxos automatizados ajudarão você a gerenciar o tempo de inatividade e minimizar o impacto operacional.

Uma notificação antecipada de 24 horas precede todos os eventos de manutenção que não são para as camadas DWC400c e inferior.

> [!NOTE]
> No caso de ser necessário implantar uma atualização crítica de tempo, os tempos de notificação avançados podem ser reduzidos significativamente. Isso pode ocorrer fora de uma janela de manutenção identificada devido à natureza crítica da atualização.

Se você recebeu a notificação antecipada de que a manutenção ocorrerá, mas a manutenção não pode ser executada durante o período de tempo na notificação, você receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção agendada.

Todos os eventos de manutenção ativos são exibidos na seção **Service Health - Planned Maintenance**. O histórico de integridade do serviço inclui um registro completo de eventos passados. Você pode monitorar a manutenção por meio do painel do portal de verificação do Funcionamento do Azure Health durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade do cronograma de manutenção

Mesmo que o agendamento de manutenção não esteja disponível em sua região selecionada, você poderá visualizar e editar sua programação de manutenção a qualquer momento. Quando o agendamento de manutenção se tornar disponível em sua região, o agendamento identificado se tornará imediatamente ativo em seu pool de SQL do Synapse.

## <a name="view-a-maintenance-schedule"></a>Exibir um agendamento de manutenção

Por padrão, todas as instâncias de data warehouse recém-criadas têm uma janela de manutenção primária e secundária de oito horas aplicada durante a implantação. Conforme indicado acima, você pode alterar o Windows assim que a implantação for concluída. Nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas sem notificação prévia.

Para exibir o agendamento de manutenção que foi aplicado ao pool SQL do Synapse, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione o pool SQL do Synapse que você deseja exibir.
3. O pool SQL do Synapse selecionado é aberto na folha visão geral. O agendamento de manutenção que é aplicado ao data warehouse aparece abaixo do **agendamento de manutenção**.

![Folha de visão geral](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Alterar um agendamento de manutenção

Um agendamento de manutenção pode ser atualizado ou alterado a qualquer momento. Se a instância selecionada estiver passando por um ciclo de manutenção ativa, as configurações serão salvas. Elas estarão ativas durante o próximo período de manutenção identificado. [Saiba mais](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre como monitorar seu data warehouse durante um evento de manutenção ativa.

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificando as janelas principais e secundárias

As janelas principais e secundárias devem ter intervalos de dia separados. Um exemplo é uma janela principal de terça-feira – quinta-feira e um secundário da janela de domingo-sábado.

Para alterar o agendamento de manutenção do pool SQL do Synapse, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione o pool SQL do Synapse que você deseja atualizar. A página será aberta na folha de visão geral.
Abra a página para configurações de agendamento de manutenção selecionando o link de **Resumo do agendamento de manutenção** na folha visão geral. Ou, selecione a opção **Agendamento de Manutenção** no menu de recursos do lado esquerdo.

    ![Opções da folha Visão geral](./media/maintenance-scheduling/maintenance-change-option.png)

3. Identifique o intervalo de dia preferencial para a janela de manutenção primário, usando as opções na parte superior da página. Essa seleção determina se a janela principal ocorrerá em um dia da semana ou no final de semana. Sua seleção irá atualizar os valores de lista suspensa.
Durante a visualização, algumas regiões podem ainda não dar suporte ao conjunto completo de opções disponíveis de **Dia**.

   ![Folha de configurações de manutenção](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Escolha suas janelas de manutenção preferencial de primário e secundário, usando as caixas de lista suspensa:
   - **Day**: dia preferencial para executar a manutenção durante a janela selecionada.
   - **Hora de início**: hora de início preferencial para a janela de manutenção.
   - **Janela de tempo**: duração preferida da janela de tempo.

   A área do **Resumo de agendamento** na parte inferior da folha é atualizada com base nos valores que você selecionou.
  
5. Selecione **Salvar**. Será exibida uma mensagem confirmando que seu novo agendamento agora está ativo.

   Se você estiver salvando um agendamento em uma região que não dá suporte a agendamento de manutenção, a seguinte mensagem será exibida. As configurações são salvas e se tornam ativas quando o recurso estiver disponível em sua região selecionada.

   ![Mensagem sobre a disponibilidade de região](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](../../azure-monitor/alerts/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor.
- [Saiba mais](../..//azure-monitor/alerts/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre ações de webhook para regras de alerta do log.
- [Saiba mais](../..//azure-monitor/alerts/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Criando e Gerenciar Grupo de ações.
- [Saiba mais](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre a Integridade do Serviço do Azure.
