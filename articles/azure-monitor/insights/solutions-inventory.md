---
title: Detalhes da coleta de dados para soluções de gerenciamento no Azure | Microsoft Docs
description: As soluções de gerenciamento do Azure são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem as métricas relacionadas a uma área de problema específica.  Este artigo fornece uma lista de soluções de gerenciamento disponíveis na Microsoft e detalhes sobre o método e a frequência de coleta de dados.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 6da2b63b7a9c8dc490bd30bb02ae6e7d932b2f7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595986"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Detalhes da coleta de dados para soluções de gerenciamento no Azure
Este artigo inclui uma lista de [soluções de gerenciamento](solutions.md) disponíveis na Microsoft com links para a documentação detalhada.  Ele também fornece informações sobre o método e frequência de coleta de dados no Azure Monitor.  É possível usar as informações deste artigo para identificar as diferentes soluções disponíveis e compreender o fluxo de dados e os requisitos de conexão para diferentes soluções de gerenciamento. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-management-solutions"></a>Lista de soluções de gerenciamento

A tabela a seguir lista as [soluções de gerenciamento](solutions.md) no Azure fornecidas pela Microsoft. Uma entrada na coluna significa que a solução coleta dados no Azure Monitor usando esse método.  Se uma solução não tiver colunas selecionadas, ela gravará diretamente no Azure Monitor de outro serviço do Azure. Siga cada link para obter a documentação detalhada e mais informações.

As explicações das colunas são as seguintes:

- **Microsoft Monitoring Agent** - Agente usado no Windows e Linux para executar o pacote de gerenciamento do SCOM e as soluções de gerenciamento do Azure. Nessa configuração, o agente é conectado diretamente ao Azure Monitor sem estar conectado a um grupo de gerenciamento do Operations Manager. 
- **Operations Manager** - Agente igual ao Microsoft Monitoring Agent. Nessa configuração, ele é [conectado a um grupo de gerenciamento do Operations Manager](../../azure-monitor/platform/om-agents.md) que está conectado ao Azure Monitor. 
-  **Armazenamento do Microsoft Azure** - A solução coleta dados de uma conta de armazenamento do Azure. 
- **O Operations Manager é necessário?** - Um grupo de gerenciamento do Operations Manager conectado é necessário para a coleta de dados pela solução de gerenciamento. 
- **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** – se o agente estiver [conectado a um grupo de gerenciamento do SCOM](../../azure-monitor/platform/om-agents.md), os dados serão enviados ao Azure Monitor do servidor de gerenciamento. Nesse caso, o agente não precisa se conectar diretamente ao Azure Monitor. Se essa caixa não estiver selecionada, os dados serão enviados diretamente do agente para o Azure Monitor, mesmo se o agente estiver conectado a um grupo de gerenciamento do SCOM. Ele precisará ser capaz de se comunicar com o Azure Monitor por meio do [gateway do Log Analytics](../../azure-monitor/platform/gateway.md).
- **Frequência de coleta** - Especifica a frequência em que os dados são coletados pela solução de gerenciamento. 



| **Solução de gerenciamento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Análise do Log de Atividades](../../azure-monitor/platform/collect-activity-logs.md) | Azure | | | | | | após a notificação |
| [Avaliação do AD](../../azure-monitor/insights/ad-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [Status de replicação do AD](../../azure-monitor/insights/ad-replication-status.md) | Windows |&#8226; |&#8226; | | |&#8226; |5 dias |
| [Integridade do Agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gerenciamento de Alertas](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |na chegada |
| [Gerenciamento de Alertas](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gerenciamento de Alertas](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) | Windows | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Conector do Application Insights (Preterido)](../../azure-monitor/platform/app-insights-connector.md) | Azure | | | |  |  | após a notificação |
| [Hybrid Worker de Automação](../../automation/automation-hybrid-runbook-worker.md) |  Windows | &#8226; | &#8226; |  |  |  | n/d |
| [Análise de Gateway de Aplicativo do Azure](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| **Solução de gerenciamento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Grupo de Segurança de Rede do Azure (preterido)](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| [Azure SQL Analytics (Visualização)](../../azure-monitor/insights/azure-sql.md) |  Windows | | | | | | 1 minuto |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | após a notificação |
| [Capacidade e Desempenho (versão prévia)](../../azure-monitor/insights/capacity-performance.md) | Windows |&#8226; |&#8226; | | |&#8226; |na chegada |
| [Controle de alterações](../../automation/automation-change-tracking.md) | Windows |&#8226; |&#8226; | | |&#8226; |[varia de acordo](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Controle de alterações](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |[varia de acordo](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Contêineres](../../azure-monitor/insights/containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análise do Cofre de Chaves](../../azure-monitor/insights/azure-key-vault.md) | Windows | | | | | |após a notificação |
| [Avaliação de malware](../../security-center/security-center-install-endpoint-protection.md) | Windows |&#8226; |&#8226; | | |&#8226; |por hora |
| [Monitor de Desempenho de Rede](../../azure-monitor/insights/network-performance-monitor.md) |  Windows | &#8226; | &#8226; |  |  |  | Handshakes TCP a cada cinco segundos; dados enviados a cada três minutos |
| [Análise do Office 365 (versão prévia)](solution-office-365.md) | Windows | | | | | |após a notificação |
| **Solução de gerenciamento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) | Windows | | |&#8226; | | |5 minutos |
| [Mapa do Serviço](../../azure-monitor/insights/service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 15 s |
| [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) | Windows |&#8226; | | | | |na chegada |
| [Avaliação do System Center Operations Manager (versão prévia)](../../azure-monitor/insights/scom-assessment.md) |  Windows | &#8226; | &#8226; |  |  | &#8226; | sete dias |
| [Gerenciamento de atualizações](../../automation/automation-update-management.md) |  Windows |&#8226; |&#8226; | | |&#8226; |pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização |
| [Preparação para atualização](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) |  Windows | &#8226; |  |  |  |  | 2 dias |
| [Monitoramento do VMware (preterido)](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Wire Data 2.0 (versão prévia)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2/8.1 ou posterior) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar dados coletados pelas soluções de gerenciamento.
