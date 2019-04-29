---
title: Agendamentos de manutenção do Azure (versão prévia) | Microsoft Docs
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção programada necessários que o serviço do Azure SQL Data Warehouse usa para implantar novos recursos, atualizações e correções.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680382"
---
# <a name="change-a-maintenance-schedule"></a>Alterar um agendamento de manutenção 

## <a name="portal"></a>Portal
Um agendamento de manutenção pode ser atualizado ou alterado a qualquer momento. Se a instância selecionada estiver passando por um ciclo de manutenção ativa, as configurações serão salvas. Elas estarão ativas durante o próximo período de manutenção identificado. [Saiba mais](https://docs.microsoft.com/azure/service-health/resource-health-overview) sobre como monitorar seu data warehouse durante um evento de manutenção ativa. 

Para usar programações de manutenção, você deve selecionar duas janelas de manutenção durante um período de sete dias. Cada janela de manutenção pode ser de três a oito horas. A manutenção pode ocorrer a qualquer momento dentro de uma janela de manutenção, mas não ocorrerá fora das janelas de tempo sem notificação prévia. Você enfrentará também uma breve perda de conectividade que o serviço implante o novo código para o data warehouse. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificando as janelas principais e secundárias

As janelas principais e secundárias devem ter intervalos de dia separados. Um exemplo é uma janela principal de terça-feira – quinta-feira e um secundário da janela de domingo-sábado.

Para alterar o agendamento de manutenção para seu data warehouse, conclua as seguintes etapas:
1.  Entre no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja atualizar. A página será aberta na folha de visão geral. 
3.  Abra a página de configurações de agendamento de manutenção, selecionando o link do **Resumo de agendamento de manutenção (visualização)** na folha de visão geral. Ou, selecione a opção **Agendamento de Manutenção** no menu de recursos do lado esquerdo.  

    ![Opções da folha Visão geral](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifique o intervalo de dia preferencial para a janela de manutenção primário, usando as opções na parte superior da página. Essa seleção determina se a janela principal ocorrerá em um dia da semana ou no final de semana. Sua seleção irá atualizar os valores de lista suspensa. Durante a visualização, algumas regiões podem ainda não dar suporte ao conjunto completo de opções disponíveis de**Dia**.

   ![Folha de configurações de manutenção](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Escolha suas janelas de manutenção preferencial de primário e secundário, usando as caixas de lista suspensa:
   - **Dia**: Dia preferencial para executar a manutenção durante a janela selecionada.
   - **Hora de início:** Hora de início preferencial para a janela de manutenção.
   - **Janela de tempo**: Duração preferencial da sua janela de tempo.

   A área do **Resumo de agendamento** na parte inferior da folha é atualizada com base nos valores que você selecionou. 
  
6. Clique em **Salvar**. Será exibida uma mensagem confirmando que seu novo agendamento agora está ativo. 

   Se você estiver salvando um agendamento em uma região que não dá suporte a agendamento de manutenção, a seguinte mensagem será exibida. As configurações são salvas e se tornam ativas quando o recurso estiver disponível em sua região selecionada.    

   ![Mensagem sobre a disponibilidade de região](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta do log.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure.


