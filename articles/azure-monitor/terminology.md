---
title: Atualizações de terminologia do Azure Monitor | Microsoft Docs
description: Descreve as alterações de terminologia recentes feitas aos serviços de monitoramento do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 819ef7e61045c0ed4205eeaf0094fec706b0b9da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498603"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Alterações de nomenclatura e de terminologia do Azure Monitor
Alterações significativas foram feitas ao Azure Monitor recentemente, com diferentes serviços sendo consolidados para simplificar o monitoramento para os clientes do Azure. Este artigo descreve as alterações recentes de nomes e de terminologia na documentação do Azure Monitor.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Outubro de 2019-log de diagnóstico para o log de recursos
"Logs de diagnóstico" foram alterados para "logs de recursos" para melhor correspondência do que realmente está sendo coletado. O termo "configurações de diagnóstico" permanece o mesmo.  

## <a name="february-2019---log-analytics-terminology"></a>Fevereiro de 2019 – terminologia do Log Analytics
Após a consolidação de serviços diferentes no Azure Monitor, estamos chegando à próxima etapa, modificando a terminologia em nossa documentação para descrever melhor o serviço do Azure Monitor e seus componentes diferentes. 

### <a name="log-analytics"></a>Log Analytics
Os dados de log do Azure Monitor ainda estão armazenados em um espaço de trabalho do Log Analytics e ainda são coletados e analisados pelo mesmo serviço do Log Analytics, mas estamos alterando o termo _Log Analytics_ em muitos locais para _logs do Azure Monitor_. Esse termo reflete melhor sua função no Azure Monitor e fornece maior consistência com as [métricas em Azure monitor](platform/data-platform-metrics.md).

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
- Saiba mais sobre a [transição do portal do OMS](./platform/oms-portal-transition.md).
