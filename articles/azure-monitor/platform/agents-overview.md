---
title: Visão geral agentes de monitoramento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral detalhada dos agentes do Azure disponíveis que oferecem suporte ao monitoramento de máquinas virtuais hospedadas no Azure ou no ambiente híbrido.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 89e73a4578134493a25ec3cbd2385433a2b36156
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775873"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Visão geral dos agentes de monitoramento do Azure 
O Microsoft Azure fornece várias maneiras de coletar diferentes tipos de dados de máquinas virtuais que executam o Microsoft Windows e o Linux hospedados no Azure, no seu datacenter ou em outros provedores de nuvem. Os três tipos de agentes disponíveis para monitorar uma VM são:

* Extensões de diagnóstico do Azure
* Agente do log Analytics para Linux e Windows
* Agente de dependência

Este artigo descreve as diferenças entre eles e seus recursos para determinar qual deles oferecerá suporte ao gerenciamento de serviços de TI ou aos requisitos gerais de monitoramento.  

## <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
A [extensão do Diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-overview.md) (comumente chamada de extensão Diagnostic do Windows Azure (WAD) ou Linux Azure Diagnostic (LAD)), que foi fornecida para os Serviços de Nuvem do Azure desde que se tornou disponível em 2010, é uma agente que fornece uma coleta simples de dados de diagnóstico de um recurso de computação do Azure, como uma VM, e os mantém no armazenamento do Azure. Uma vez no armazenamento, você opta por exibir com uma das várias ferramentas disponíveis, como [Gerenciador de Servidores o Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) e [Gerenciador de Armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Você pode optar por coletar:

* Um conjunto predefinido de contadores de desempenho do sistema operacional e logs de eventos ou pode especificar qual deseja coletar. 
* Todas as solicitações de e/ou solicitações com falha para um servidor Web IIS
* Logs de saída de rastreamento de aplicativos .NET
* Eventos de ETW (Rastreamento de Eventos para Windows) 
* Coletar eventos de log do syslog  
* Despejos de falhas 

O agente do Azure Diagnostics deve ser usado quando você quiser:

* Arquivar logs e métricas para o armazenamento do Azure
* Integre dados de monitoramento com ferramentas de terceiros. Essas ferramentas usam uma variedade de métodos, incluindo a consulta à conta de armazenamento, encaminhados para [Hubs de Eventos](../../event-hubs/event-hubs-about.md) ou consultando com a [API REST do Azure Monitoring](../../azure-monitor/platform/rest-api-walkthrough.md)
* Carregar dados no Azure Monitor para criar gráficos de métricas no portal do Azure ou criar [alertas de métrica](../../azure-monitor/platform/alerts-metric-overview.md) quase em tempo real. 
* Conjuntos de dimensionamento de máquina virtual de escala automática e Serviços de nuvem clássicos com base em métricas de sistema operacional convidado.
* Investigue os problemas de inicialização da VM com o [ Boot Diagnostics ](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Entenda o desempenho de seus aplicativos e identifique proativamente os problemas que os afetam com [Application Insights](../../azure-monitor/overview.md).
* Configurar o Azure Monitor para importar as métricas e log de dados coletados de serviços de nuvem, VMs clássicas, e nós do Service Fabric são armazenados em uma conta de armazenamento do Azure.

## <a name="log-analytics-agent"></a>Agente do log Analytics
Para o monitoramento avançado em que você precisa coletar mais de um subconjunto de logs e métricas, o agente do Log Analytics para Windows (também conhecido como o agente MMA (Microsoft Monitoring)) e o Linux é necessário. O agente Log Analytics foi desenvolvido para gerenciamento abrangente em máquinas físicas e virtuais no local, computadores monitorados pelo System Center Operations Manager e VMs hospedados em outras nuvens. Os agentes do Windows e Linux se conectar a um espaço de trabalho do Log Analytics no Azure Monitor para coletar dados baseados em soluções de monitoramento, bem como fontes de dados personalizadas que você configurar.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

O agente do Log Analytics deve ser usado quando você quiser:

* Colete dados de várias origens no Azure, em outros provedores de nuvem e em recursos locais. 
* Usando uma das soluções de monitoramento, como o Azure Monitor [do Azure Monitor para VMs](../insights/vminsights-overview.md), [do Azure Monitor para contêineres](../insights/container-insights-overview.md), etc.  
* Use um dos serviços de gerenciamento do Azure, como [Central de segurança do Azure](../../security-center/security-center-intro.md), [automação do Azure](../../automation/automation-intro.md), etc.

Anteriormente, os vários serviços do Azure foram agrupados como o *Operations Management Suite*, e assim o agente do Log Analytics é compartilhado entre serviços, incluindo a Central de segurança do Azure e automação do Azure.  Isso inclui o conjunto completo de recursos que eles oferecem, oferecendo gerenciamento abrangente de suas VMs do Azure por meio de seu ciclo de vida.  Alguns exemplos disso são:

* [Gerenciamento de atualização de automação do Azure](../../automation/automation-update-management.md) de atualizações do sistema operacional.
* [Configuração de estado desejado do Automação do Azure](../../automation/automation-dsc-overview.md) para manter o estado de configuração consistente.
* Rastreie as alterações de configuração com [Rastreamento de alterações e inventário de automação do Azure](../../automation/automation-change-tracking.md).
* Serviços do Azure, como [Application Insights](https://docs.microsoft.com/azure/application-insights/) e [Azure Security Center](https://docs.microsoft.com/azure/security-center/), que armazenam de forma nativa seus dados diretamente no Log Analytics.  

## <a name="dependency-agent"></a>Agente de dependência
O agente de dependência foi desenvolvido como parte da solução Mapa do serviço, que não foi originalmente desenvolvida pela Microsoft. [O mapa do serviço](../insights/service-map.md) e [do Azure Monitor para VMs](../insights/vminsights-overview.md) exige um agente de dependência no Windows e Linux máquinas virtuais e ele se integra com o agente do Log Analytics para coletar dados de descobertas sobre processos em execução no virtual máquina e as dependências de processo externo. Ele armazena esses dados em um espaço de trabalho do Log Analytics e visualiza os componentes interconectados descobertos.

Você pode precisar de alguma combinação desses agentes para monitorar sua VM. Os agentes podem ser instalados lado a lado como extensões do Azure, no entanto, no Linux, o agente do Log Analytics *deve* ser instalado primeiro ou a instalação falhará. 

## <a name="next-steps"></a>Próximas etapas

- Confira [Visão geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para revisar os requisitos e métodos com suporte para implantar o agente em máquinas hospedadas no Azure, em seu datacenter ou em outro ambiente de nuvem.

