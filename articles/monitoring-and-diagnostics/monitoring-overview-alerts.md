---
title: "Visão geral dos Alertas no Microsoft Azure e no Azure Monitor | Microsoft Docs"
description: "Alerta para permitir que você monitore as métricas dos recursos do Azure, eventos ou logs, e seja notificado quando uma condição especificada for atendida."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afa863e2a900d4f823b77453d92f034db7d5a93f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>O que são os alertas no Microsoft Azure?
Este artigo descreve as várias fontes de alertas no Microsoft Azure, as finalidades desses alertas, seus benefícios e como começar a usá-los. Ele se aplica especificamente ao Azure Monitor, mas fornece indicações de outros serviços com alertas. Os alertas oferecem um método de monitoramento no Azure que permite a configuração de condições sobre os dados e o recebimento de notificações quando as condições correspondem aos últimos dados de monitoramento.

## <a name="taxonomy-of-azure-alerts"></a>Taxonomia de alertas do Azure
O Azure usa os seguintes termos para descrever os alertas e suas funções:
* **Alerta** – uma definição de critérios (uma ou mais regras ou condições) que são ativados quando atendidos.
* **Ativo** – o estado quando os critérios definidos por um alerta são atendidos.
* **Resolvido** – o estado quando os critérios definidos por um alerta não forem mais atendidos após terem sido atendidos anteriormente.
* **Notificação** – a ação realizada após a ativação de um alerta.
* **Ação** – uma chamada específica enviada a um destinatário de uma notificação (por exemplo, enviando por email um endereço ou publicando em uma URL de webhook). As notificações normalmente podem disparar várias ações.

## <a name="alerts-in-different-azure-services"></a>Alertas nos diferentes serviços do Azure
Os alertas estão disponíveis em vários serviços de monitoramento do Azure. Para saber mais sobre como e quando usar esses serviços, [confira este artigo](./monitoring-overview.md). Confira uma divisão dos tipos de alerta disponíveis no Azure:

| O Barramento de | Tipo de alerta | Serviços com suporte | Descrição |
|---|---|---|---|
| Azure Monitor | [Alertas de métricas](./insights-alerts-portal.md) | [Métricas com suporte do Azure Monitor](./monitoring-supported-metrics.md) | Receba uma notificação quando qualquer métrica no nível da plataforma atender a uma condição específica (por exemplo, a % de CPU em uma VM for maior do que 90 nos últimos cinco minutos). |
|Azure Monitor | [Alertas de Métrica Quase em Tempo Real (versão prévia)](./monitoring-near-real-time-metric-alerts.md)| [Recursos com suporte no Azure Monitor](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | Receba uma notificação mais rapidamente do que os alertas de métrica quando uma ou mais métricas no nível da plataforma atender às condições especificadas (por exemplo, o % de CPU em uma VM é maior que 90 e a Entrada de Rede é maior que 500 MB nos últimos 5 minutos). |
| Azure Monitor | [Alertas do log de atividades](./monitoring-activity-log-alerts.md) | Todos os tipos de recursos disponíveis no Azure Resource Manager | Receba uma notificação quando qualquer evento novo no [Log de Atividades do Azure](./monitoring-overview-activity-logs.md) corresponder a condições específicas (por exemplo, quando uma operação "Excluir VM" ocorrer em myProductionResourceGroup ou quando um novo evento de Integridade do Serviço com status "Ativo" for exibido). |
| Application Insights | [Alertas de métricas](../application-insights/app-insights-alerts.md) | Qualquer aplicativo instrumentado para enviar dados ao Application Insights | Receba uma notificação quando qualquer métrica no nível do aplicativo atender a uma condição específica (por exemplo, tempo de resposta do servidor superior a dois segundos). |
| Application Insights | [Alertas de teste da Web](../application-insights/app-insights-monitor-web-app-availability.md) | Qualquer site instrumentado para enviar dados ao Application Insights | Receba uma notificação quando a disponibilidade ou capacidade de resposta de um site estiver abaixo das expectativas. |
| Log Analytics | [Alertas do Log Analytics](../log-analytics/log-analytics-alerts.md) | Qualquer serviço configurado para enviar dados ao Log Analytics | Receba uma notificação quando uma consulta de pesquisa do Log Analytics sobre métricas e/ou dados de evento atender a certos critérios. |

## <a name="alerts-on-azure-monitor-data"></a>Alertas sobre dados do Azure Monitor
Há três tipos de alertas de dados disponíveis no Azure Monitor – alertas de métrica, alertas de métrica quase em tempo real (versão prévia) e alertas do Log de Atividades.

* **Alertas de métrica**: este alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite que você atribui. O alerta gera uma notificação quando o alerta é "Ativado" (quando o limite for ultrapassado, e a condição do alerta for atendida), e também quando for "Resolvido" (quando o limite for ultrapassado novamente e a condição não for mais atendida). Para obter uma lista crescente de métricas disponíveis com suporte do Azure Monitor, consulte [Lista de métricas com suporte no Azure Monitor](monitoring-supported-metrics.md).
* **Alertas de métrica quase em tempo real (versão prévia)** – esses alertas são semelhantes aos alertas de métrica, mas diferem de algumas maneiras. Em primeiro lugar, como o nome sugere, esses alertas podem ser disparados quase em tempo real (na velocidade de 1 minuto). Eles também dão suporte ao monitoramento de várias métricas (atualmente duas).  O alerta gera uma notificação quando é “Ativado” (quando os limites de cada métrica são ultrapassados ao mesmo tempo e a condição do alerta é atendida) e também quando é “Resolvido” (quando, pelo menos, uma métrica ultrapassa o limite novamente e a condição não é mais atendida).

> [!NOTE]
> Atualmente, os alertas de métrica quase em tempo real estão em visualização pública. A funcionalidade e a experiência do usuário está sujeita a alterações.
>
>

* **Alertas do log de atividade** – um alerta do log de streaming que dispara quando um evento do Log de Atividades for gerado correspondendo aos critérios do filtro que você atribuiu. Esses alertas têm apenas um estado, "Ativado", pois o mecanismo de alerta simplesmente aplica os critérios do filtro a qualquer evento novo. Esses alertas podem ser usados para receber uma notificação quando um novo incidente de Integridade do Serviço ocorrer, ou quando um usuário ou aplicativo executar uma operação em sua assinatura, por exemplo, "Excluir a máquina virtual".

Para dados de Log de Diagnóstico disponíveis por meio do Azure Monitor, sugerimos rotear os dados para o Log Analytics e usando um alerta do Log Analytics. O diagrama a seguir resume as fontes de dados no Azure Monitor e, conceitualmente, como emitir um alerta com base nesses dados.

![Alertas explicados](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Como fazer para receber uma notificação em um alerta do Azure Monitor?
Historicamente, os alertas do Azure de serviços diferentes usavam seus próprios métodos de notificação internos. De agora em diante, o Azure Monitor oferece um agrupamento de notificação reutilizável chamado de Grupos de Ações. Os Grupos de Ação especificam um conjunto de destinatários para uma notificação – qualquer quantidade de endereços de email, números de telefone (para SMS) ou URLs de webhook – e sempre que um alerta for ativado fazendo referência ao Grupo de Ações, todos os destinatários receberão essa notificação. Isso permite que você reutilize um agrupamento de destinatários (por exemplo, sua lista de engenheiros em serviço) em vários objetos de alerta. Atualmente, apenas os alertas do Log de Atividades usam os Grupos de Ação, mas vários outros tipos de alerta do Azure também estão trabalhando para usar os Grupos de Ação.

Grupos de Ação dão suporte à notificação postando uma URL de webhook além dos endereços de email e números SMS. Isso permite a automação e a correção, por exemplo, usando:
    - Runbook de Automação do Azure
    - Azure Function
    - Aplicativo lógico do Azure
    - um serviço de terceiros

Os alertas de métrica quase em tempo real (versão prévia) e os alertas do Log de Atividades usam Grupos de Ação.

Alertas de métricas ainda não usam Grupos de Ação. Em um alerta de métrica individual, você pode configurar notificações para:
* Enviar notificações por email para o administrador do serviço, coadministradores e/ou emails adicionais especificados.
* Chamar um webhook, o que permite inicializar ações de automação adicionais.

## <a name="next-steps"></a>Próximas etapas
Obter informações sobre as regras de alerta e sobre como configurá-las usando:

* Saiba mais sobre [Métricas](monitoring-overview-metrics.md)
* Configurar [alertas de métrica por meio do Portal do Azure](insights-alerts-portal.md)
* Configurar o [PowerShell de alertas de métrica](insights-alerts-powershell.md)
* Configurar a [CLI (interface de linha de comando) de alertas de métrica](insights-alerts-command-line-interface.md)
* Configurar a [API REST do Azure Monitor de alertas de métrica](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre o [Log de Atividades](monitoring-overview-activity-logs.md)
* Configurar [alertas do Log de Atividades por meio do Portal do Azure](monitoring-activity-log-alerts.md)
* Configurar [alertas do Log de Atividades por meio do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Examine o [esquema do webhook de alertas do Log de Atividade](monitoring-activity-log-alerts-webhook.md)
* Saiba mais sobre [Alertas de Métrica Quase em Tempo Real](monitoring-near-real-time-metric-alerts.md)
* Saiba mais sobre as [Notificações de Serviço](monitoring-service-notifications.md)
* Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md)
