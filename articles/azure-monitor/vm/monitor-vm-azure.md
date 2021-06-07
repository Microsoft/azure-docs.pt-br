---
title: Monitorar máquinas virtuais do Azure com o Azure Monitor
description: Descreve como coletar e analisar dados de monitoramento de máquinas virtuais no Azure usando o Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 2c93471436030f9260f4fa0d95d656c27d382346
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047036"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorar máquinas virtuais do Azure com o Azure Monitor
Este artigo descreve como usar o Azure Monitor para coletar e analisar dados de monitoramento de máquinas virtuais do Azure para manter sua integridade. As máquinas virtuais podem ser monitoradas quanto à disponibilidade e ao desempenho com Azure Monitor como qualquer [outro recurso do Azure](../essentials/monitor-azure-resource.md), mas são exclusivas de outros recursos, já que você também precisa monitorar o sistema operacional e as cargas de trabalho convidadas que são executadas nelas. 

> [!NOTE]
> Este artigo fornece uma visão geral completa dos conceitos e das opções para monitorar máquinas virtuais no Azure Monitor. Para começar a monitorar suas máquinas virtuais rapidamente sem se concentrar nos conceitos subjacentes, consulte [Início Rápido: Monitorar uma máquina virtual do Azure com o Azure Monitor](./quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Diferenças de outros recursos do Azure
O artigo [Monitorar recursos do Azure com o Azure Monitor](../essentials/monitor-azure-resource.md) descreve os dados de monitoramento gerados pelos recursos do Azure e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados. Você pode coletar os mesmos dados de monitoramento e atuar sobre eles de máquinas virtuais do Azure com as seguintes diferenças:

-  As [métricas de plataforma](../essentials/data-platform-metrics.md) são coletadas automaticamente para máquinas virtuais, mas somente para o [host de máquina virtual](#monitoring-data). Você precisa de um agente para coletar dados de desempenho do sistema operacional convidado. 
- As máquinas virtuais não geram [logs de recursos](../essentials/platform-logs-overview.md) que fornecem informações sobre as operações que foram executadas em um recurso do Azure. Você usará um agente para coletar dados de log do sistema operacional convidado.
- Você pode criar [configurações de diagnóstico](../essentials/diagnostic-settings.md) para uma máquina virtual para enviar métricas de plataforma a outros destinos, como armazenamento e hubs de eventos, mas não é possível definir essas configurações de diagnóstico no portal do Azure. 

## <a name="monitoring-data"></a>Dados de monitoramento
As máquinas virtuais no Azure geram [logs](../logs/data-platform-logs.md) e [métricas](../essentials/data-platform-metrics.md) , conforme mostrado no diagrama a seguir.

![Visão geral](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host da máquina virtual
As máquinas virtuais no Azure geram os seguintes dados para o host da máquina virtual da mesma forma que outros recursos do Azure, conforme descrito em [Dados de monitoramento](../essentials/monitor-azure-resource.md#monitoring-data).

- [Métricas de plataforma](../essentials/data-platform-metrics.md) - Valores numéricos que são coletados automaticamente em intervalos regulares e descrevem algum aspecto de um recurso em um determinado momento. As métricas de plataforma são coletadas para o host da máquina virtual, mas você precisa da extensão de diagnóstico para coletar métricas para o sistema operacional convidado.
- [Log de atividades](../essentials/platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso do Azure na assinatura desde a parte exterior (o plano de gerenciamento). Para uma máquina virtual, isso inclui informações como quando ela foi iniciada e qualquer alteração de configuração.


### <a name="guest-operating-system"></a>Sistema operacional convidado
Para coletar dados do sistema operacional convidado de uma máquina virtual, você precisa de um agente, que é executado localmente em cada máquina virtual e envia dados para Azure Monitor. Vários agentes estão disponíveis para Azure Monitor, e cada um deles coleta dados diferentes e grava dados em locais diferentes. Obtenha uma comparação detalhada dos diferentes agentes em [Visão geral dos agentes de Azure Monitor](../agents/agents-overview.md). 

- [Agente do Log Analytics](../agents/agents-overview.md#log-analytics-agent) - Disponível para máquinas virtuais no Azure, outros ambientes de nuvem e locais. Coleta dados para os logs do Azure Monitor. Dá suporte a soluções de monitoramento e informações de VM. Esse é o mesmo agente usado para System Center Operations Manager.
- [Agente de dependência](../agents/agents-overview.md#dependency-agent) - Coleta dados sobre os processos em execução na máquina virtual e suas dependências. O se baseia no agente de Log Analytics para transmitir dados para o Azure e oferece suporte a soluções de VM, Mapa do Serviço e Wire Data 2.0.
- [Extensão Azure Diagnostic](../agents/agents-overview.md#azure-diagnostics-extension) - Disponível somente para máquinas virtuais Azure Monitor. Pode coletar dados para vários locais, mas usados principalmente para coletar dados de desempenho de convidado em métricas de Azure Monitor para máquinas virtuais do Windows.
- [Agente do Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) - Coletar dados de desempenho de VMs do Linux para métricas do Azure Monitor.


## <a name="configuration-requirements"></a>Requisitos de configuração
Para habilitar todos os recursos do Azure Monitor para monitoramento de máquina virtual, você precisará coletar dados de monitoramento dos sistemas operacionais host e convidados da máquina virtual para [Métricas do Azure Monitor](../logs/data-platform-logs.md) e [Logs do Azure Monitor](../logs/data-platform-logs.md). A tabela a seguir lista a configuração que deve ser executada para habilitar essa coleta. Você pode optar por não executar todas essas etapas dependendo de seus requisitos específicos.

| Etapa de configuração | Ações concluídas | Recursos habilitados |
|:---|:---|:---|
| Nenhuma configuração | - Métricas de plataforma de host coletadas para métricas.<br>- Coleta de logs de atividades. | - Metrics Explorer para host.<br>- Alertas de métricas para host.<br>- Alertas do log de atividades. |
| [Habilitar insights da VM](#enable-vm-insights) | - Agente do Log Analytics instalado.<br>- Agente de dependência instalado.<br>- Dados de desempenho de convidado coletados para logs.<br>- Detalhes de processo e dependência coletados para logs. | - Gráficos de desempenho e pastas de trabalho para dados de desempenho de convidado.<br>- Consultas de log para dados de desempenho de convidado.<br>- Alertas de log para dados de desempenho de convidado.<br>- Mapa de dependências. |
| [Instalar a extensão de diagnóstico e o agente Telegraf](#enable-diagnostics-extension-and-telegraf-agent) | - Dados de desempenho de convidado coletados para métricas. | - Metrics Explorer para convidado.<br>- Alertas de métricas para convidado.  |
| [Configurar um espaço de trabalho do Log Analytics](#configure-log-analytics-workspace) | - Eventos coletados do convidado. | - Consultas de log para eventos de convidado.<br>- Alertas de log para eventos de convidado. |
| [Criar a configuração de diagnóstico para a máquina virtual](#collect-platform-metrics-and-activity-log) | - Métricas de plataforma coletadas para Logs.<br>- Log de atividades coletados para Logs. | -Log de consultas para métricas de host.<br>- Alertas de log para métricas do host.<br>- Consultas de log para log de atividades.

Cada uma dessas etapas de configuração é descrita nas seções a seguir.

### <a name="enable-vm-insights"></a>Habilitar insights da VM
As informações de [VM](../vm/vminsights-overview.md) são uma [percepção](../monitor-reference.md) em Azure monitor que é a principal ferramenta para monitorar máquinas virtuais no Azure monitor. Ele fornece o seguinte valor adicional em relação aos recursos de Azure Monitor padrão.

- Integração simplificada do agente de Log Analytics e do agente de dependência para habilitar o monitoramento de cargas de trabalho e sistema operacional convidado de máquina virtual. 
- Gráficos predefinidos de desempenho de tendências e pastas de trabalho que permitem que você analise as métricas de desempenho principais do sistema operacional convidado da máquina virtual.
- Mapa de dependências que exibe os processos em execução em cada máquina virtual e os componentes interconectados com outros computadores e fontes externas.

![Exibição de desempenho de informações de VM](media/monitor-vm-azure/vminsights-01.png)

![Exibição de mapas de informações de VM](media/monitor-vm-azure/vminsights-02.png)


Habilite o Revisions da VM da opção **insights** no menu máquina virtual do portal do Azure. Consulte [habilitar visão geral do VM insights](vminsights-enable-overview.md) para obter detalhes e outros métodos de configuração.

![Habilitar insights da VM](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurar o espaço de trabalho do Log Analytics
O agente de Log Analytics usado pelo Revisions de VM envia dados para um [espaço de trabalho log Analytics](../logs/data-platform-logs.md). Você pode habilitar a coleta de dados de desempenho adicionais, eventos e outros dados de monitoramento do agente, configurando o espaço de trabalho Log Analytics. Ele só precisa ser configurado uma vez, uma vez que qualquer agente que se conecta ao espaço de trabalho baixará automaticamente a configuração e começará imediatamente a coletar os dados definidos. 

Você pode acessar a configuração do espaço de trabalho diretamente de informações da VM, selecionando **configuração do espaço de trabalho** na **introdução**. Clique no nome do espaço de trabalho para abrir o menu.

![Configuração do workspace](media/monitor-vm-azure/workspace-configuration.png)

Selecione **Configurações Avançadas** do menu do espaço de trabalho e, em seguida, **Dados** para configurar fontes de dados. Para agentes do Windows, selecione **Logs de eventos do Windows** e adicione logs de eventos comuns, como do *Sistema* e do *Aplicativo*. Para agentes do Linux, selecione **Syslog** e adicione instalações comuns, como *kerning* e *daemon*. Consulte [Fontes de dados do agente no Azure Monitor](../agents/agent-data-sources.md) para obter uma lista das fontes de dados disponíveis e detalhes sobre como configurá-las. 

![Configurar eventos](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Você pode configurar os contadores de desempenho a serem coletados da configuração do espaço de trabalho, mas isso pode ser redundante com os contadores de desempenho coletados pelo VM insights. O Revisions da VM coleta o conjunto de contadores mais comum a uma frequência de uma vez por minuto. Configure somente os contadores de desempenho a serem coletados pelo espaço de trabalho se você quiser coletar contadores que ainda não foram coletados pelo insights da VM ou se você tiver consultas existentes usando dados de desempenho.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Habilitar a extensão de diagnóstico e o agente Telegraf
As informações de VM se baseiam no agente de Log Analytics que envia dados para um espaço de trabalho Log Analytics. Isso é compatível com vários recursos de Azure Monitor, como [consultas de log](../logs/log-query-overview.md), [alertas de log](../alerts/alerts-log.md) e [pastas de trabalho](../visualize/workbooks-overview.md). A [extensão de diagnóstico](../agents/diagnostics-extension-overview.md) coleta dados de desempenho do sistema operacional convidado de máquinas virtuais do Windows para o armazenamento do Azure e, opcionalmente, envia dados de desempenho para [Azure Monitor Metrics](../essentials/data-platform-metrics.md). Para máquinas virtuais do Linux, o [agente do Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) é necessário para enviar dados para as métricas do Azure.  Isso habilita outros recursos de Azure Monitor como [Metrics Explorer](../essentials/metrics-getting-started.md) e [alertas de métricas](../alerts/alerts-metric.md). Você também pode configurar a extensão de diagnóstico para enviar eventos e dados de desempenho fora do Azure Monitor usando os hubs de eventos do Azure.

Instale a extensão de diagnóstico para uma única máquina virtual do Windows no portal do Azure na opção **Configuração de diagnóstico** no menu da VM. Selecione a opção para habilitar **Azure Monitor** na guia **Coletores**. Para habilitar a extensão de um modelo ou linha de comando para várias máquinas virtuais, consulte [Instalação e configuração](../agents/diagnostics-extension-overview.md#installation-and-configuration). Ao contrário do agente de Log Analytics, os dados a serem coletados são definidos na configuração da extensão em cada máquina virtual.

![Configuração de diagnóstico](media/monitor-vm-azure/diagnostic-setting.png)

Consulte [Instalar e configurar o Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) para obter detalhes sobre como configurar os agentes do Telegraf em máquinas virtuais do Linux. A opção de menu **Configuração de diagnóstico** está disponível para Linux, mas só permitirá que você envie dados para o armazenamento do Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Coletar métricas de plataforma e o log de atividades
Você pode exibir as métricas de plataforma e o log de atividades coletados para cada host de máquina virtual na portal do Azure. Colete esses dados no mesmo espaço de trabalho Log Analytics como o virtual insights para analisá-los com os outros dados de monitoramento coletados para a máquina virtual. Essa coleta é configurada com uma [configuração de diagnóstico](../essentials/diagnostic-settings.md). Colete o log de atividades com uma [configuração de diagnóstico para a de assinatura](../essentials/diagnostic-settings.md#create-in-azure-portal).

Colete métricas de plataforma com uma configuração de diagnóstico para a máquina virtual. Ao contrário de outros recursos do Azure, você não pode criar uma configuração de diagnóstico para uma máquina virtual no portal do Azure, mas deve usar [outro método](../essentials/diagnostic-settings.md#create-using-powershell). Os exemplos a seguir mostram como coletar métricas para uma máquina virtual usando o PowerShell e a CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```azurecli
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Monitoramento no portal do Azure 
Depois de configurar a coleta de dados de monitoramento para uma máquina virtual, você terá várias opções para acessá-la no portal do Azure:

- Use o menu **Azure Monitor** para acessar dados de todos os recursos monitorados. 
- Use o VM insights para monitorar conjuntos de máquinas virtuais em escala.
- Analise os dados de uma única máquina virtual em seu menu no portal do Azure. A tabela a seguir lista opções diferentes para monitorar o menu de máquinas virtuais.

![Monitoramento no portal do Azure](media/monitor-vm-azure/monitor-menu.png)

| Opção de menu | Descrição |
|:---|:---|
| Visão geral | Exibe [métricas de plataforma](../essentials/data-platform-metrics.md) para o host da máquina virtual. Clique em um grafo para trabalhar com esses dados no [Metrics Explorer](../essentials/metrics-getting-started.md). |
| Log de atividades | Entradas de [log de atividades](../essentials/activity-log.md#view-the-activity-log) filtradas para a máquina virtual atual. |
| Insights | Abre o [VM insights](../vm/vminsights-overview.md) com o mapa para a máquina virtual atual selecionada. |
| Alertas | Exibe os [alertas](../alerts/alerts-overview.md) para o tamanho atual da máquina virtual.  |
| Métricas | Abra o [Metrics Explorer](../essentials/metrics-getting-started.md) com o escopo definido para a máquina virtual atual. |
| Configurações de Diagnóstico | Habilite e configure a [extensão de diagnóstico](../agents/diagnostics-extension-overview.md) para a máquina virtual atual. |
| Recomendações do Assistente | Recomendações para a máquina virtual atual de [Azure Advisor](../../advisor/index.yml). |
| Logs | Abra o [Log Analytics](../logs/log-analytics-overview.md) com o [escopo](../logs/scope.md) definido para a máquina virtual atual. |
| Monitor de conexão | Abra [Monitor de conexão do observador de rede](../../network-watcher/connection-monitor-overview.md) para monitorar as conexões entre a máquina virtual atual e outras máquinas virtuais. |


## <a name="analyzing-metric-data"></a>Analisando dados de métrica
Você pode analisar métricas para máquinas virtuais usando o Metrics Explorer abrindo **Métricas** no menu da máquina virtual. Consulte [Introdução ao Azure Metrics Explorer](../essentials/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta. 

Há três namespaces usados por máquinas virtuais para métricas:

| Namespace | Descrição | Requisito |
|:---|:---|:---|
| Host de Máquina Virtual | Métricas do host coletadas automaticamente para todas as máquinas virtuais do Azure. Lista detalhada de métricas em [Microsoft. Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines). | Coletado automaticamente sem nenhuma configuração necessária. |
| Convidado (clássico) | Conjunto limitado de dados de desempenho do aplicativo e do sistema operacional convidado. Disponível no Metrics Explorer, mas não em outros recursos Azure Monitor, como alertas de métricas.  | [Extensão de diagnóstico](../agents/diagnostics-extension-overview.md) instalada. Os dados são lidos no armazenamento do Azure.  |
| Convidado de Máquina Virtual | O sistema operacional convidado e os dados de desempenho do aplicativo estão disponíveis para todos os recursos Azure Monitor usando métricas. | Para Windows, [extensão de diagnóstico instalada](../agents/diagnostics-extension-overview.md) com o coletor Azure Monitor habilitado. Para Linux, [agente do Telegraf instalado](../essentials/collect-custom-metrics-linux-telegraf.md). |

![Gerenciador de métricas no portal do Azure](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analisando dados de log
As máquinas virtuais do Azure coletarão os dados a seguir para logs do Azure Monitor. 

O Revisions de VM permite a coleta de um conjunto predeterminado de contadores de desempenho que são gravados na tabela *InsightsMetrics* . Esta é a mesma tabela usada por [insights de contêiner](../containers/container-insights-overview.md). 

| Fonte de dados | Requisitos | Tabelas |
|:---|:---|:---|
| Insights da VM | Habilite em cada máquina virtual. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Consulte [como consultar logs de informações da VM](../vm/vminsights-log-search.md) para obter detalhes. |
| Log de atividades | Configuração de diagnóstico para a assinatura. | AzureActivity |
| Métricas de host | Configuração de diagnóstico para a máquina virtual. | AzureMetrics |
| Fontes de dados do sistema operacional convidado | Habilite o agente do Log Analytics e configure as fontes de dados. | Consulte a documentação para cada fonte de dados. |


> [!NOTE]
> Os dados de desempenho coletados pelo agente de Log Analytics são gravados na tabela *perf* enquanto o virtual insights o coletará na tabela *InsightsMetrics* . São os mesmos dados, mas as tabelas têm uma estrutura diferente. Se você tiver consultas existentes com base em *Perf*, será necessário reescrever para usar *InsightsMetrics*.


## <a name="alerts"></a>Alertas
Os [alertas](../alerts/alerts-overview.md) no Azure Monitor notificam-no proativamente quando condições importantes forem encontradas nos dados de monitoramento e potencialmente iniciem uma ação, como iniciar um aplicativo lógico ou chamar um webhook. As regras de alerta definem a lógica usada para determinar quando um alerta deve ser criado. O Azure Monitor coleta os dados usados pelas regras de alerta, mas você precisa criar regras para definir condições de alerta em sua assinatura do Azure.

As seções a seguir descrevem os tipos de regras de alerta e recomendações sobre quando você deve usar cada um. Essa recomendação se baseia na funcionalidade e no custo do tipo de regra de alerta. Para obter detalhes sobre os preços dos alertas, confira os [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Regras de alerta do log de atividades
As [regras de alerta do log de atividades](../alerts/alerts-activity-log.md) acionadas quando uma entrada que corresponde a critérios específicos é criada no log de atividades. Eles não têm nenhum custo, portanto, devem ser sua primeira opção se a lógica necessária estiver no log de atividades. 

O recurso de destino para alertas do log de atividades pode ser uma máquina virtual específica, todas as máquinas virtuais em um grupo de recursos ou todas as máquinas virtuais em uma assinatura.

Por exemplo, crie um alerta se uma máquina virtual crítica for interrompida selecionando *Desligar a máquina virtual* para o nome do sinal.

![Alerta do log de atividades](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regras de alerta de métrica
As [regras de alerta de métrica](../alerts/alerts-metric.md) são acionadas quando um valor de métrica excede um limite. Você pode definir um valor de limite específico ou permitir que o Azure Monitor determine dinamicamente um limite com base em dados históricos.  Use alertas de métrica sempre que possível com dados de métrica, pois eles custam menos e são mais responsivos do que as regras de alerta de log. Eles também estão com monitoração de estado, o que significa que eles se resolverão quando a métrica cair abaixo do limite.

O recurso de destino para alertas do log de atividades pode ser uma máquina virtual específica ou todas as máquinas virtuais em um grupo de recursos.

Por exemplo, para criar um alerta quando o processador de uma máquina virtual exceder um valor específico, crie uma regra de alerta de métrica usando *Percentual de CPU* como o tipo de sinal. Defina um valor de limite específico ou permita que Azure Monitor defina um limite dinâmico. 

![Alerta de métrica](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alertas de registro
As [regras de alerta de log](../alerts/alerts-log.md) são acionadas quando os resultados de uma consulta de log agendada correspondem a determinados critérios. Os alertas de consulta de log são os mais caros e menos responsivos das regras de alerta, mas têm acesso aos dados mais diversos e podem executar uma lógica complexa que não pode ser executada pelas outras regras de alerta. 

O recurso de destino para uma consulta de log é um espaço de trabalho Log Analytics. Filtrar computadores específicos na consulta.

Por exemplo, para criar um alerta que verifica se alguma máquina virtual em um grupo de recursos específico está offline, use a consulta a seguir, que retorna um registro para cada computador que perdeu uma pulsação nos últimos dez minutos. Use um limite de 1 que é acionado se pelo menos um computador tiver uma pulsação perdida.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alerta de log para pulsação perdida](media/monitor-vm-azure/log-alert-01.png)

Para criar um alerta se um número excessivo de logons com falha tiver ocorrido em qualquer máquina virtual do Windows na assinatura, use a consulta a seguir que retorna um registro para cada evento de logon com falha na última hora. Use um limite definido para o número de logons com falha que você permitirá. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![Alerta de log para logons com falha](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager fornece monitoramento granular de cargas de trabalho em máquinas virtuais. Consulte o [Guia de monitoramento de nuvem](/azure/cloud-adoption-framework/manage/monitor/) para obter uma comparação de plataformas de monitoramento e estratégias diferentes para implementação.

Se você tiver um ambiente de Operations Manager existente que pretende continuar usando, você poderá integrá-lo ao Azure Monitor para fornecer funcionalidade adicional. O agente de Log Analytics usado pelo Azure Monitor é o mesmo usado para Operations Manager para que você monitore as máquinas virtuais para ambos. Você ainda precisa adicionar o agente ao Revisions da VM e configurar o espaço de trabalho para coletar dados adicionais, conforme especificado acima, mas as máquinas virtuais podem continuar a executar seus pacotes de gerenciamento existentes em um ambiente de Operations Manager sem modificação.

Os recursos de Azure Monitor que aumentam os recursos existentes do Operations Manager incluem o seguinte:

- Use Log Analytics para analisar interativamente seus dados de desempenho e de log.
- Use alertas de log para definir condições de alerta em várias máquinas virtuais e usando tendências de longo prazo que não são possíveis usando alertas no Operations Manager.   

Consulte [conectar Operations Manager ao Azure monitor](../agents/om-agents.md) para obter detalhes sobre como conectar seu grupo de gerenciamento existente do Operations Manager ao seu espaço de trabalho do log Analytics.


## <a name="next-steps"></a>Próximas etapas

* [Saiba como analisar dados em logs de Azure Monitor usando consultas de log.](../logs/get-started-queries.md)
* [Saiba mais sobre alertas usando métricas e logs em Azure Monitor.](../alerts/alerts-overview.md)