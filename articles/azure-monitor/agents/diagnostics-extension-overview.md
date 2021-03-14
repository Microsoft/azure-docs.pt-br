---
title: Visão geral da extensão de Diagnóstico do Azure
description: Usar o diagnóstico do Azure para depurar, medir o desempenho, monitorar e analisar o tráfego em serviços de nuvem, em máquinas virtuais e no Service Fabric
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 90addb74811819dec563ba072e9a6aea2cf72ca4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036530"
---
# <a name="azure-diagnostics-extension-overview"></a>Visão geral da extensão de Diagnóstico do Azure
A extensão de Diagnóstico do Azure é um [agente no Azure Monitor](../agents/agents-overview.md) que coleta dados de monitoramento do sistema operacional convidado de recursos de computação do Azure, incluindo máquinas virtuais. Este artigo fornece uma visão geral da extensão de Diagnóstico do Azure, incluindo funcionalidades específicas às quais ele dá suporte e opções para instalação e configuração. 

> [!NOTE]
> A extensão de Diagnóstico do Azure é um dos agentes disponíveis para coletar dados de monitoramento do sistema operacional convidado dos recursos de computação. Confira [Visão geral dos agentes do Azure Monitor](../agents/agents-overview.md) para obter uma descrição dos diferentes agentes e diretrizes sobre como selecionar os agentes apropriados para seus requisitos.

## <a name="primary-scenarios"></a>Principais cenários
Os principais cenários abordados pela extensão de diagnóstico são:

- Coletar métricas de convidado em Métricas do Azure Monitor.
- Enviar logs e métricas de convidado ao armazenamento do Azure para arquivamento.
- Enviar métricas e logs de convidado aos hubs de eventos do Azure para enviar para fora do Azure.


## <a name="comparison-to-log-analytics-agent"></a>Comparação com o agente do Log Analytics
O agente do Log Analytics no Azure Monitor também pode ser usado para coletar dados de monitoramento do sistema operacional convidado das máquinas virtuais. Você pode optar por usar um ou ambos, dependendo dos seus requisitos. Confira [Visão geral dos agentes do Azure Monitor](../agents/agents-overview.md) para obter uma comparação detalhada dos agentes do Azure Monitor. 

As principais diferenças a serem consideradas são:

- A extensão de Diagnóstico do Azure só pode ser usada com máquinas virtuais do Azure. O agente do Log Analytics pode ser usado com máquinas virtuais no Azure, em outras nuvens e localmente.
- A extensão de Diagnóstico do Azure envia dados para o Armazenamento do Azure, para as [Métricas do Azure Monitor](../essentials/data-platform-metrics.md) (somente Windows) e para os Hubs de Eventos. O agente do Log Analytics coleta dados para os [Logs do Azure Monitor](../logs/data-platform-logs.md).
- O agente de Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [informações de VM](../vm/vminsights-overview.md)e outros serviços, como a [central de segurança do Azure](../../security-center/index.yml).

## <a name="costs"></a>Custos
Não há nenhum custo para a extensão de Diagnóstico do Azure, mas pode haver encargos pelos dados ingeridos. Verifique os [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para o destino em que você está coletando dados.

## <a name="data-collected"></a>Dados coletados
As tabelas a seguir listam os dados que podem ser coletados pela extensão de diagnóstico do Windows e do Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| fonte de dados | Descrição |
| --- | --- |
| Logs de Eventos do Windows   | Eventos do log de eventos do Windows. |
| Contadores de desempenho | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| Logs IIS             | Informações de uso para sites do IIS em execução no sistema operacional convidado. |
| Logs de aplicativo     | Rastreio de mensagens gravadas pelo seu aplicativo. |
| Logs do .NET EventSource |Eventos de gravação de código usando a classe [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) do .NET |
| [Logs do ETW baseados no manifesto](/windows/desktop/etw/about-event-tracing) |Rastreamento de eventos para eventos do Windows gerados por qualquer processo. |
| Despejos de memória (logs)   | Informações sobre o estado do processo se um aplicativo falhar. |
| Logs baseados em arquivo    | Logs criados por seu aplicativo ou serviço. |
| Logs de diagnóstico do agente | Informações sobre o próprio Diagnóstico do Azure. |


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico do Linux (LAD)

| fonte de dados | Descrição |
| --- | --- |
| syslog | Eventos enviados para o sistema de log de eventos do Linux.   |
| Contadores de desempenho  | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| Arquivos de log | Entradas enviadas a um log baseado em arquivo.  |

## <a name="data-destinations"></a>Destinos de dados
A extensão de Diagnóstico do Azure para Windows e Linux sempre coleta dados em uma conta do Armazenamento do Azure. Confira [Instalar e configurar a extensão WAD (Diagnóstico do Windows Azure)](diagnostics-extension-windows-install.md) e [Usar a Extensão de Diagnóstico do Linux para monitorar métricas e logs](../../virtual-machines/extensions/diagnostics-linux.md) para obter uma lista de tabelas e blobs específicos em que esses dados são coletados.

Configure um ou mais *coletores de dados* para enviar dados a outros destinos adicionais. As seções a seguir listam os coletores disponíveis para a extensão de diagnóstico do Windows e do Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Destino | Descrição |
|:---|:---|
| Métricas do Azure Monitor | Coletar dados de desempenho para as Métricas do Azure Monitor. Confira [Enviar Métricas do SO convidado para o banco de dados de métricas do Azure Monitor](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Hubs de Eventos | Use os Hubs de Eventos do Azure para enviar dados fora do Azure. Confira [Transmitir dados do Diagnóstico do Azure para os Hubs de Eventos](diagnostics-extension-stream-event-hubs.md) |
| Blobs do Armazenamento do Azure | Grave em dados em blobs no Armazenamento do Azure, além de tabelas. |
| Application Insights | Colete dados de aplicativos em execução em sua VM para o Application Insights a fim de fazer a integração com outro monitoramento de aplicativo. Confira [Enviar dados de diagnóstico para o Application Insights](diagnostics-extension-to-application-insights.md). |

Você também pode coletar dados do WAD do armazenamento para um workspace do Log Analytics a fim de analisá-los com Logs do Azure Monitor, embora o agente do Log Analytics seja normalmente usado para essa funcionalidade. Ele pode enviar dados diretamente para um workspace do Log Analytics e dá suporte a soluções e informações que fornecem funcionalidade adicional.  Confira [Coletar logs de diagnóstico do Azure do Armazenamento do Azure](../agents/diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico do Linux (LAD)
O LAD grava dados em tabelas no Armazenamento do Azure. Ele dá suporte aos coletores na tabela a seguir.

| Destino | Descrição |
|:---|:---|
| Hubs de Eventos | Use os Hubs de Eventos do Azure para enviar dados fora do Azure. |
| Blobs do Armazenamento do Azure | Grave em dados em blobs no Armazenamento do Azure, além de tabelas. |
| Métricas do Azure Monitor | Instale o agente do Telegraf, além do LAD. Confira [Coletar métricas personalizadas para uma VM Linux com o agente InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalação e configuração
A extensão de Diagnóstico é implementada como uma [extensão da máquina virtual](../../virtual-machines/extensions/overview.md) no Azure. Portanto, dá suporte às mesmas opções de instalação usando modelos do Resource Manager, o PowerShell e a CLI. Confira [Recursos e extensões de máquina virtual para Windows](../../virtual-machines/extensions/features-windows.md) e [Recursos e extensões de máquina virtual para Linux](../../virtual-machines/extensions/features-linux.md) a fim de obter detalhes gerais sobre como instalar e manter as extensões de máquina virtual.

Você também pode instalar e configurar a extensão de diagnóstico do Windows e Linux no portal do Azure em **Configurações de diagnóstico** na seção **Monitoramento** do menu da máquina virtual.

Confira os artigos a seguir para obter detalhes sobre como instalar e configurar a extensão de diagnóstico para Windows e Linux.

- [Instalar e configurar a extensão WAD (Diagnóstico do Windows Azure)](diagnostics-extension-windows-install.md)
- [Usar a Extensão de Diagnóstico do Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Outra documentação

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Funções Web e de Trabalho do Serviço de Nuvem do Azure (clássico)
- [Introdução ao Monitoramento do Serviço de Nuvem](../../cloud-services/cloud-services-how-to-monitor.md)
- [Habilitar o Diagnóstico do Azure nos Serviços de Nuvem do Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights para serviços de nuvem do Azure](../app/cloudservices.md)<br>[Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador local](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Próximas etapas


* Saiba como [usar os Contadores de Desempenho no Diagnóstico do Azure](../../cloud-services/diagnostics-performance-counters.md).
* Caso tenha problemas com o início do diagnóstico ou a localização de seus dados nas tabelas de armazenamento do Azure, confira [Solução de problemas do Diagnóstico do Azure](diagnostics-extension-troubleshooting.md)