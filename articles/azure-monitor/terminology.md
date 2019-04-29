---
title: Atualizações de terminologia do Azure Monitor | Microsoft Docs
description: Descreve as alterações de terminologia recentes feitas aos serviços de monitoramento do Azure.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 8f645f7d569546a8362d0149806a2b4636567fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086729"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Alterações de nomenclatura e de terminologia do Azure Monitor
Alterações significativas foram feitas ao Azure Monitor recentemente, com diferentes serviços sendo consolidados para simplificar o monitoramento para os clientes do Azure. Este artigo descreve as alterações recentes de nomes e de terminologia na documentação do Azure Monitor.

## <a name="february-2019---log-analytics-terminology"></a>Fevereiro de 2019 – terminologia do Log Analytics
Após a consolidação de serviços diferentes no Azure Monitor, estamos chegando à próxima etapa, modificando a terminologia em nossa documentação para descrever melhor o serviço do Azure Monitor e seus componentes diferentes. 

### <a name="log-analytics"></a>Log Analytics
Os dados de log do Azure Monitor ainda estão armazenados em um espaço de trabalho do Log Analytics e ainda são coletados e analisados pelo mesmo serviço do Log Analytics, mas estamos alterando o termo _Log Analytics_ em muitos locais para _logs do Azure Monitor_. Esse termo melhor reflete sua função no Azure Monitor e fornece [métricas de melhor consistência no Azure Monitor](platform/data-platform-metrics.md).

O termo _Log Analytics_ agora se aplica principalmente para a página no portal do Azure usada para gravar e executar consultas e analisar dados de log. É o equivalente funcional do [Metrics Explorer](platform/metrics-charts.md), que é a página no portal do Azure usada para analisar dados de métrica.

### <a name="log-analytics-workspaces"></a>Workspaces do Log Analytics
[Workspaces](platform/manage-access.md) que armazenam dados de log no Azure Monitor ainda são chamados de workspaces do Log Analytics. O menu **Log Analytics** no portal do Azure foi renomeado para **Workspaces do Log Analytics** e é nele que você [cria novos workspaces](learn/quick-create-workspace.md) e configura fontes de dados. Analise seus logs e outros dados de monitoramento no **Azure Monitor** e configure seu workspace nos **workspaces do Log Analytics**.

### <a name="management-solutions"></a>Soluções de gerenciamento
As [soluções de gerenciamento](insights/solutions.md) foram renomeadas para _soluções de monitoramento_, o que descreve melhor sua funcionalidade.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Agosto de 2018 – consolidação de serviços de monitoramento no Azure Monitor
O Log Analytics e o Application Insights foram consolidados no Azure Monitor para fornecer uma única experiência integrada para monitoramento de recursos do Azure e ambientes híbridos. Nenhuma funcionalidade foi removida desses serviços e os usuários podem fazer as atividades de sempre sem perda nem comprometimento de nenhum recurso.

A documentação para cada um desses serviços foi consolidada em um único conjunto de conteúdo para o Azure Monitor. Isso ajudará o leitor a encontrar todo o conteúdo para um cenário de monitoramento específico em um único local em vez de fazer referência a vários conjuntos de conteúdo. À medida que o serviço consolidado evolui, o conteúdo se tornará mais integrado.

Outros recursos que foram considerados parte do Log Analytics, tais como agentes e exibições, também foram reposicionados como recursos do Azure Monitor. Sua funcionalidade não mudou para além de possíveis aprimoramentos em sua experiência no portal do Azure.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Abril de 2018 – desativação da marca do Operations Management Suite
O OMS (Operations Management Suite) era um agrupamento dos seguintes serviços de gerenciamento do Azure para fins de licenciamento:

- Application Insights
- Automação do Azure
- Serviço de Backup do Azure
- Log Analytics
- Site Recovery

[O novo preço foi introduzido para esses serviços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), e o agrupamento do OMS não está mais disponível para novos clientes. Nenhum dos serviços que faziam parte do OMS foram alterados, exceto para a consolidação no Azure Monitor descrita acima. 




## <a name="next-steps"></a>Próximas etapas

- Leia uma [visão geral do Azure Monitor](overview.md) que descreve as funcionalidades e componentes diferentes.
- Saiba mais sobre a [transição do portal do OMS](../log-analytics/log-analytics-oms-portal-transition.md).