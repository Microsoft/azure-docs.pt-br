---
title: Visão geral agentes de monitoramento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral detalhada dos agentes do Azure disponíveis que oferecem suporte ao monitoramento de máquinas virtuais hospedadas no Azure ou no ambiente híbrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2020
ms.openlocfilehash: 8b9a79ee32d7a81fee64e40b85272877b7d87589
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674191"
---
# <a name="overview-of-azure-monitor-agents"></a>Visão geral dos agentes de Azure Monitor

Máquinas virtuais e outros recursos de computação exigem um agente para coletar dados de monitoramento necessários para medir o desempenho e a disponibilidade de seu sistema operacional convidado e cargas de trabalho. Este artigo descreve os agentes usados pelo Azure Monitor e ajuda a determinar qual é necessário para atender aos requisitos para seu ambiente específico.

> [!NOTE]
> Atualmente, Azure Monitor tem vários agentes devido a uma consolidação recente de Azure Monitor e Log Analytics. Embora possa haver sobreposição em seus recursos, cada um tem recursos exclusivos. Dependendo dos seus requisitos, você pode precisar de um ou mais agentes em suas máquinas virtuais. 

Você pode ter um conjunto específico de requisitos que não podem ser atendidos completamente com um único agente para uma determinada máquina virtual. Por exemplo, talvez você queira usar alertas de métrica que exijam a extensão de diagnóstico do Azure, mas também deseja aproveitar a funcionalidade de Azure Monitor para VMs que requer o agente de Log Analytics e o agente de dependência. Em casos como esse, você pode usar vários agentes, e esse é um cenário comum para clientes que exigem funcionalidade de cada um.

## <a name="summary-of-agents"></a>Resumo dos agentes

As tabelas a seguir fornecem uma comparação rápida dos agentes de Azure Monitor para Windows e Linux. Mais detalhes sobre cada um são fornecidos na seção abaixo.

> [!NOTE]
> O agente de Azure Monitor está atualmente em visualização com recursos limitados. Esta tabela será atualizada 

### <a name="windows-agents"></a>Agentes do Windows

| | Agente do Azure Monitor (versão prévia) | Diagnósticos<br>extensão (WAD) | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|
| **Ambientes com suporte** | Azure<br>Outra nuvem (arco do Azure)<br>Local (Azure ARC)  | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | 
| **Requisitos do agente**  | Nenhum | Nenhum | Nenhum | Requer agente de Log Analytics |
| **Dados coletados** | Logs de eventos<br>Desempenho | Logs de eventos<br>eventos ETW<br>Desempenho<br>Logs baseados em arquivo<br>Logs IIS<br>Logs do aplicativo .NET<br>Despejos de memória<br>Logs de diagnóstico do agente | Logs de eventos<br>Desempenho<br>Logs baseados em arquivo<br>Logs IIS<br>Insights e soluções<br>Outros serviços | Dependências de processo<br>Métricas de conexão de rede |
| **Dados enviados para** | Logs do Azure Monitor<br>Métricas do Azure Monitor | Armazenamento do Azure<br>Métricas do Azure Monitor<br>Hub de evento | Logs do Azure Monitor | Logs do Azure Monitor<br>(por meio do agente de Log Analytics) |
| **Serviços e**<br>**Features**<br>**porta** | Log Analytics<br>Metrics Explorer | Metrics Explorer | Azure Monitor para VMs<br>Log Analytics<br>Automação do Azure<br>Central de Segurança do Azure<br>Azure Sentinel | Azure Monitor para VMs<br>Mapa do Serviço |

### <a name="linux-agents"></a>Agentes do Linux

| | Agente do Azure Monitor (versão prévia) | Diagnósticos<br>extensão (LAD) | Telegraf<br>agente | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|:---|
| **Ambientes com suporte** | Azure<br>Outra nuvem (arco do Azure)<br>Local (arco ARC) | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local |
| **Requisitos do agente**  | Nenhum | Nenhum | Nenhum | Nenhum | Requer agente de Log Analytics |
| **Dados coletados** | syslog<br>Desempenho | syslog<br>Desempenho | Desempenho | syslog<br>Desempenho| Dependências de processo<br>Métricas de conexão de rede |
| **Dados enviados para** | Logs do Azure Monitor<br>Métricas do Azure Monitor | Armazenamento do Azure<br>Hub de evento | Métricas do Azure Monitor | Logs do Azure Monitor | Logs do Azure Monitor<br>(por meio do agente de Log Analytics) |
| **Serviços e**<br>**Features**<br>**porta** | Log Analytics<br>Metrics Explorer | | Metrics Explorer | Azure Monitor para VMs<br>Log Analytics<br>Automação do Azure<br>Central de Segurança do Azure<br>Azure Sentinel | Azure Monitor para VMs<br>Mapa do Serviço |


## <a name="azure-monitor-agent-preview"></a>Agente do Azure Monitor (versão prévia)
O [agente de Azure monitor](azure-monitor-agent-overview.md) está atualmente em visualização e substituirá o agente de log Analytics e o agente Telegraf para máquinas virtuais Windows e Linux. Ele pode enviar dados para os logs de Azure Monitor e Azure Monitor métricas e usa [as regras de coleta de dados (DCR)](data-collection-rule-overview.md) , que fornecem um método mais escalonável de configurar a coleta e os destinos de dados para cada agente.

Use o agente de Azure Monitor se você precisar:

- Coletar logs e métricas de convidado de qualquer máquina virtual no Azure, em outras nuvens ou localmente. (Arco do Azure necessário para máquinas virtuais fora do Azure.) 
- Envie dados para Azure Monitor logs e métricas de Azure Monitor para análise com Azure Monitor. 
- Envie dados para o armazenamento do Azure para arquivamento.
- Envie dados para ferramentas de terceiros usando [hubs de eventos do Azure](diagnostics-extension-stream-event-hubs.md).
- Gerencie a segurança de suas máquinas virtuais usando a [central de segurança do Azure](../../security-center/security-center-introduction.md)  ou o [Azure Sentinel](../../sentinel/overview.md). (Não disponível na versão prévia.)

As limitações do agente de Azure Monitor incluem:

- Atualmente em visualização pública. Consulte [limitações atuais](azure-monitor-agent-overview.md#current-limitations) para obter uma lista de limitações durante a visualização pública.

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O [agente de log Analytics](log-analytics-agent.md) coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais no Azure, outros provedores de nuvem e locais. Ele envia dados para um espaço de trabalho Log Analytics. O agente de Log Analytics é o mesmo agente usado pelo System Center Operations Manager, e você pode usar computadores de agente multihome para se comunicar com o grupo de gerenciamento e Azure Monitor simultaneamente. Esse agente também é exigido por determinadas informações em Azure Monitor e outros serviços no Azure.


> [!NOTE]
> O agente de Log Analytics para Windows é geralmente conhecido como Microsoft Monitoring Agent (MMA). O agente de Log Analytics para Linux é geralmente conhecido como agente do OMS.



Use o agente de Log Analytics se você precisar:

* Colete logs e dados de desempenho de máquinas virtuais ou físicas dentro ou fora do Azure. 
* Envie dados para um espaço de trabalho Log Analytics para aproveitar os recursos com suporte dos [logs de Azure monitor](data-platform-logs.md) , como [consultas de log](../log-query/log-query-overview.md).
* Use [Azure monitor para VMs](../insights/vminsights-overview.md) que permite monitorar suas máquinas virtuais em escala e monitora seus processos e dependências em outros recursos e processos externos.  
* Gerencie a segurança de suas máquinas virtuais usando a [central de segurança do Azure](../../security-center/security-center-introduction.md)  ou o [Azure Sentinel](../../sentinel/overview.md).
* Use o [Gerenciamento de atualizações da automação do Azure](../../automation/update-management/overview.md), a [configuração de estado da automação do azure](../../automation/automation-dsc-overview.md)ou a [controle de alterações de automação do Azure e o inventário](../../automation/change-tracking/overview.md) para fornecer gerenciamento abrangente de suas VMs do Azure
* Use [soluções](../monitor-reference.md#insights-and-core-solutions) diferentes para monitorar um determinado serviço ou aplicativo.

As limitações do agente de Log Analytics incluem:

- Não é possível enviar dados para Azure Monitor métricas, armazenamento do Azure ou hubs de eventos do Azure.
- Dificuldade em configurar definições de monitoramento exclusivas para agentes individuais.
- Difícil de gerenciar em escala, uma vez que cada máquina virtual tem uma configuração exclusiva.

## <a name="azure-diagnostics-extension"></a>Extensão de diagnóstico do Azure

A [extensão diagnóstico do Azure](diagnostics-extension-overview.md) coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Ele coleta principalmente dados no armazenamento do Azure, mas também permite que você defina coletores de dados para também enviar dados a outros destinos, como Azure Monitor métricas e hubs de eventos do Azure.

Use a extensão de diagnóstico do Azure se você precisar:

- Envie dados para o armazenamento do Azure para arquivamento ou para analisá-los com ferramentas como [Gerenciador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envie dados para [Azure monitor métricas](data-platform-metrics.md) para analisá-lo com o [Metrics Explorer](metrics-getting-started.md) e aproveitar os recursos como [alertas de métrica](./alerts-metric-overview.md) quase em tempo real e [dimensionamento automático](autoscale-overview.md) (somente Windows).
- Envie dados para ferramentas de terceiros usando [hubs de eventos do Azure](diagnostics-extension-stream-event-hubs.md).
- Colete o [diagnóstico de inicialização](../../virtual-machines/troubleshooting/boot-diagnostics.md) para investigar problemas de inicialização da VM.

As limitações da extensão de diagnóstico do Azure incluem:

- Só pode ser usado com recursos do Azure.
- Capacidade limitada de enviar dados para Azure Monitor logs.

## <a name="telegraf-agent"></a>Agente Telegraf

O [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) é usado para coletar dados de desempenho de computadores Linux para Azure monitor métricas.

Use o agente do Telegraf se você precisar:

* Envie dados para [Azure monitor métricas](data-platform-metrics.md) para analisá-lo com o [Metrics Explorer](metrics-getting-started.md) e aproveitar os recursos, como [alertas de métrica](./alerts-metric-overview.md) quase em tempo real e [dimensionamento automático](autoscale-overview.md) (somente Linux). 



## <a name="dependency-agent"></a>Agente de dependência

O agente de dependência coleta dados descobertos sobre processos em execução na máquina virtual e dependências de processo externo. 

Use o Dependency Agent se você precisar:

* Use o recurso de mapa [Azure monitor para VMs](../insights/vminsights-overview.md) ou a solução de [mapa do serviço](../insights/service-map.md) .

Considere o seguinte ao usar o Dependency Agent:

- O agente de dependência requer que o agente de Log Analytics seja instalado na mesma máquina virtual.
- Em VMs do Linux, o agente de Log Analytics deve ser instalado antes da extensão de diagnóstico do Azure.

## <a name="virtual-machine-extensions"></a>Extensões de máquina virtual

A extensão de Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) instala o agente de log Analytics em máquinas virtuais do Azure. A extensão de dependência Azure Monitor para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instala o Dependency Agent em máquinas virtuais do Azure. Esses são os mesmos agentes descritos acima, mas permitem que você os gerencie por meio de [extensões de máquina virtual](../../virtual-machines/extensions/overview.md). Você deve usar extensões para instalar e gerenciar os agentes sempre que possível.


## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis
As tabelas a seguir listam os sistemas operacionais que têm suporte dos agentes de Azure Monitor. Consulte a documentação de cada agente para obter considerações exclusivas e sobre o processo de instalação. Consulte a documentação do Telegraf para seus sistemas operacionais com suporte. Todos os sistemas operacionais são considerados x64. Não há suporte para x86 em nenhum sistema operacional.

### <a name="windows"></a>Windows

| Sistema operacional | Agente do Azure Monitor | Agente do Log Analytics | Agente de dependência | Extensão de diagnóstico | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(incluindo várias sessões) e pro<br>(Somente cenários de servidor)  | X | X | X | X |
| Windows 8 Enterprise e Pro<br>(Somente cenários de servidor)  |   | X | X |   |
| Windows 7 SP1<br>(Somente cenários de servidor)                 |   | X | X |   |


### <a name="linux"></a>Linux

| Sistema operacional | Agente do Azure Monitor | Agente do Log Analytics | Agente de dependência | Extensão de diagnóstico | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                     |   | X |   |   |
| CentOS Linux 8                                           |   | X |   |   |
| CentOS Linux 7                                           | X | X |   | X |
| CentOS Linux 7,8                                         | X | X | X | X |
| CentOS Linux 7,6                                         | X | X | X | X |
| CentOS Linux 6                                           |   | X |   |   |
| CentOS Linux 6.5 +                                        |   | X |   | X |
| Debian 10                                                | X |   |   |   |
| Debian 9                                                 | X | X | x | X |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1+                                           |   |   |   | X |
| Oracle Linux 8                                           |   | X |   |   |
| Oracle Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           |   | X |   |   |
| Oracle Linux 6.4+                                        |   | X |   | X |
| Red Hat Enterprise Linux Server 8                        |   | X |   |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7 +                     |   | X | X | X |
| SUSE Linux Enterprise Server 15,1                        |   | X |   |   |
| SUSE Linux Enterprise Server 15                          | X | X |   |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 20.04 LTS                                         |   | X |   |   |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14, 4 LTS                                         |   | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>Agente de dependência suporte a kernel do Linux
Como o Dependency Agent funciona no nível do kernel, o suporte também depende da versão do kernel. A tabela a seguir lista a versão principal e secundária do sistema operacional Linux e as versões de kernel com suporte para o Dependency Agent.

| Distribuição | Versão do SO | Versão do kernel |
|:---|:---|:---|
|  Red Hat Linux 7   | 7.6     | 3.10.0-957  |
|                    | 7,5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOSPlus         | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 18.04   | 5.3.0-1020<br>5,0 (inclui kernel ajustado pelo Azure)<br>4,18 *<br> 4,15* |
|                    | 16.04.3 | 4,15. * |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP4 | 4,12. * (inclui kernel ajustado para o Azure) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4.9  | 


## <a name="next-steps"></a>Próximas etapas

Obtenha mais detalhes sobre cada um dos agentes no seguinte:

- [Visão geral do agente de Log Analytics](log-analytics-agent.md)
- [Visão geral da extensão de Diagnóstico do Azure](diagnostics-extension-overview.md)
- [Coletar métricas personalizadas para uma VM do Linux com o Agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
