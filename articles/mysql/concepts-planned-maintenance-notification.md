---
title: Notificação de manutenção planejada-banco de dados do Azure para MySQL-servidor único
description: Este artigo descreve o recurso de notificação de manutenção planejada no banco de dados do Azure para MySQL-servidor único
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: ff197f8add65782a594d64661ffecdaced4598c2
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919617"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Notificação de manutenção planejada no banco de dados do Azure para MySQL-servidor único

Saiba como se preparar para eventos de manutenção planejada no banco de dados do Azure para MySQL.

## <a name="what-is-a-planned-maintenance"></a>O que é uma manutenção planejada?

O serviço banco de dados do Azure para MySQL executa aplicação de patch automatizada do hardware, do sistema operacional e do mecanismo de banco de dados subjacentes. O patch inclui novos recursos de serviço, segurança e atualizações de software. Para o mecanismo MySQL, as atualizações de versão secundária são automáticas e incluídas como parte do ciclo de aplicação de patch. Não há nenhuma ação do usuário nem definições de configuração necessárias para a aplicação de patch. O patch é testado extensivamente e distribuído usando práticas de implantação seguras.

Uma manutenção planejada é uma janela de manutenção quando essas atualizações de serviço são implantadas em servidores em uma determinada região do Azure. Durante a manutenção planejada, um evento de notificação é criado para informar os clientes quando a atualização de serviço é implantada na região do Azure que hospeda os servidores. A duração mínima entre duas manutenções planejadas é de 30 dias. Você receberá uma notificação da próxima janela de manutenção com 72 horas de antecedência.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Manutenção planejada-duração e impacto sobre o cliente

Uma manutenção planejada para uma determinada região do Azure normalmente deve ser executada 15 h. A janela também inclui o tempo de buffer para executar um plano de reversão, se necessário. Durante a manutenção planejada, pode haver reinicializações ou failovers do servidor de banco de dados, o que pode levar a uma breve indisponibilidade dos servidores de banco de dados para os usuários finais. Os servidores do banco de dados do Azure para MySQL estão em execução em contêineres, portanto, as reinicializações do servidor de banco de dados normalmente são rápidas, esperadas normalmente em 60-120 segundos. Todo o evento de manutenção planejada, incluindo cada reinicialização do servidor, é cuidadosamente monitorado pela equipe de engenharia. O tempo de failover do servidor depende do tempo de recuperação do banco de dados, o que pode fazer com que o banco de dados fique online mais tempo se você tiver atividade transacional pesada no servidor no momento do failover. Para evitar tempo de reinício mais longo, é recomendável evitar todas as transações de longa execução (cargas em massa) durante os eventos de manutenção planejada.

Em resumo, enquanto o evento de manutenção planejada é executado por 15 horas, o impacto do servidor individual geralmente dura 60 segundos, dependendo da atividade transacional no servidor. Uma notificação é enviada 72 horas de calendário antes do início da manutenção planejada e outra, enquanto a manutenção está em andamento para uma determinada região.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Como posso ser notificado sobre a manutenção planejada?

Você pode utilizar o recurso de notificações de manutenção planejada para receber alertas para um próximo evento de manutenção planejada. Você receberá a notificação sobre as próximas horas de manutenção 72 do calendário antes do evento e de outra, enquanto a manutenção está em andamento para uma determinada região.

### <a name="planned-maintenance-notification"></a>Notificação de manutenção planejada

> [!IMPORTANT]
> As notificações de manutenção planejada estão disponíveis atualmente na visualização em todas as regiões **, exceto** no Oeste EUA Central

As **notificações de manutenção planejada** permitem que você receba alertas para o próximo evento de manutenção planejada para o banco de dados do Azure para MySQL. Essas notificações são integradas à manutenção planejada [da integridade do serviço](../service-health/overview.md) e permitem que você exiba toda a manutenção agendada para suas assinaturas em um único local. Ele também ajuda a dimensionar a notificação para os públicos certos para grupos de recursos diferentes, pois você pode ter contatos diferentes responsáveis por diferentes recursos. Você receberá a notificação sobre as próximas horas de manutenção 72 do calendário antes do evento.

Faremos todas as tentativas de fornecer **notificação de manutenção planejada** 72 horas de aviso para todos os eventos. No entanto, em casos de patches críticos ou de segurança, as notificações podem ser enviadas próximo ao evento ou ser omitidas.

Você pode verificar a notificação de manutenção planejada em portal do Azure ou configurar alertas para receber notificações. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Verificar a notificação de manutenção planejada de portal do Azure

1. Na [portal do Azure](https://portal.azure.com), selecione **integridade do serviço**.
2. Selecione a guia **manutenção planejada**
3. Selecione a **assinatura**, a **região** e o **serviço** para os quais você deseja verificar a notificação de manutenção planejada. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Para receber a notificação de manutenção planejada

1. No [portal](https://portal.azure.com), selecione **Integridade do Serviço**.
2. Na seção **Alertas**, selecione **Alertas de integridade**.
3. Selecione **+ adicionar alerta de integridade do serviço** e preencha os campos.
4. Preencha os campos obrigatórios. 
5. Escolha o **tipo de evento**, selecione **manutenção planejada** ou **selecionar tudo**
6. Em **grupos de ação** , defina como você deseja receber o alerta (obter um email, disparar um aplicativo lógico etc.)  
7. Verifique se habilitar regra após a criação está definido como Sim.
8. Selecione **criar regra de alerta** para concluir o alerta

Para obter etapas detalhadas sobre como criar **alertas de integridade do serviço**, consulte [criar alertas do log de atividades em notificações de serviço](../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Posso cancelar ou adiar a manutenção planejada?

A manutenção é necessária para manter seu servidor seguro, estável e atualizado. O evento de manutenção planejada não pode ser cancelado ou adiado. Depois que a notificação é enviada para uma determinada região do Azure, as alterações de agendamento de aplicação de patch não podem ser feitas para qualquer servidor individual nessa região. O patch é distribuído para toda a região ao mesmo tempo. Banco de dados do Azure para MySQL-o serviço de servidor único foi projetado para aplicativos nativos de nuvem que não exigem controle granular ou personalização do serviço. Se você estiver procurando ter a capacidade de agendar a manutenção para seus servidores, recomendamos que considere [servidores flexíveis](./flexible-server/overview.md).

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Todas as regiões do Azure são corrigidas ao mesmo tempo?

Não, todas as regiões do Azure são corrigidas durante os intervalos da janela de implantação. A janela de implantação inteligente geralmente se estende de 5 PM-8:00 na hora local no dia seguinte, em uma determinada região do Azure. As regiões do Azure em pares geograficamente são corrigidas em dias diferentes. Para alta disponibilidade e continuidade de negócios de servidores de banco de dados, é recomendável aproveitar [réplicas de leitura entre regiões](./concepts-read-replicas.md#cross-region-replication) .

## <a name="retry-logic"></a>Lógica de repetição

Um erro transitório, também conhecido como uma falha transitória, é um erro que será resolvido por si só. Podem ocorrer [erros transitórios](./concepts-connectivity.md#transient-errors) durante a manutenção. A maioria desses eventos é atenuada automaticamente pelo sistema em menos de 60 segundos. Os erros transitórios devem ser tratados usando a [lógica de repetição](./concepts-connectivity.md#handling-transient-errors).


## <a name="next-steps"></a>Próximas etapas

- Para perguntas ou sugestões que você pode ter sobre como trabalhar com o banco de dados do Azure para MySQL, envie um email para a equipe do banco de dados do Azure para MySQL em AskAzureDBforMySQL@service.microsoft.com
- Consulte [Como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- [Solucionar problemas de conexão com o banco de dados do Azure para MySQL-servidor único](howto-troubleshoot-common-connection-issues.md)
- [Manipular erros transitórios e conectar-se com eficiência ao banco de dados do Azure para MySQL-servidor único](concepts-connectivity.md)
