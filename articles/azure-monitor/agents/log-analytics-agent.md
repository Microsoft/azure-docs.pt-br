---
title: Visão geral do Agente do Log Analytics
description: Este tópico ajuda o reconhecimento de como coletar dados e monitorar computadores hospedados no Azure, no local ou em outro ambiente de nuvem com o Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 8b3b1d39766d556b820f21c1775210611b995d92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199894"
---
# <a name="log-analytics-agent-overview"></a>Visão geral do Agente do Log Analytics

O agente de Log Analytics do Azure coleta a telemetria de máquinas virtuais Windows e Linux em qualquer nuvem, máquinas locais e aquelas monitoradas pelo [System Center Operations Manager](/system-center/scom/) e envia dados coletados para o espaço de trabalho Log Analytics no Azure monitor. O agente de Log Analytics também dá suporte a informações e outros serviços em Azure Monitor como o [VM insights](../vm/vminsights-enable-overview.md), a [central de segurança do Azure](../../security-center/index.yml)e a [automação do Azure](../../automation/automation-intro.md). Este artigo fornece uma visão geral detalhada dos requisitos do agente, do sistema e da rede e dos métodos de implantação.

> [!NOTE]
> Você também pode ver o agente de Log Analytics referido como o Microsoft Monitoring Agent (MMA).

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparação com a extensão de Diagnóstico do Azure
A [extensão de Diagnóstico do Azure](./diagnostics-extension-overview.md) no Azure Monitor também pode ser usada para coletar dados de monitoramento do sistema operacional convidado de máquinas virtuais do Azure. Você pode optar por usar um ou ambos, dependendo dos requisitos. Confira [Visão geral dos agentes do Azure Monitor](../agents/agents-overview.md) para obter uma comparação detalhada dos agentes do Azure Monitor. 

As principais diferenças a serem consideradas são:

- A extensão de Diagnóstico do Azure só pode ser usada com máquinas virtuais do Azure. O agente do Log Analytics pode ser usado com máquinas virtuais no Azure, em outras nuvens e localmente.
- A extensão de Diagnóstico do Azure envia dados para o Armazenamento do Azure, para as [Métricas do Azure Monitor](../essentials/data-platform-metrics.md) (somente Windows) e para os Hubs de Eventos. O agente de Log Analytics envia dados para [Azure monitor logs](../logs/data-platform-logs.md).
- O agente de Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [informações de VM](../vm/vminsights-overview.md)e outros serviços, como a [central de segurança do Azure](../../security-center/index.yml).

## <a name="costs"></a>Custos

Não há nenhum custo para o Agente do Log Analytics, mas você pode incorrer em encargos por conta dos dados ingeridos. Confira [Gerenciar o uso e os custos com os Logs do Azure Monitor](../logs/manage-cost-storage.md) para obter informações detalhadas sobre os preços dos dados coletados em um workspace do Log Analytics.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

 Consulte [sistemas operacionais com suporte](../agents/agents-overview.md#supported-operating-systems) para obter uma lista das versões do sistema operacional Windows e Linux com suporte pelo agente de log Analytics. 

## <a name="data-collected"></a>Dados coletados

A tabela a seguir lista os tipos de dados que você pode configurar para um workspace do Log Analytics coletar de todos os agentes conectados. Confira [O que é monitorado pelo Azure Monitor?](../monitor-reference.md) para obter uma lista de informações, soluções e outras opções que usam o Agente do Log Analytics para coletar outros tipos de dados.

| fonte de dados | Descrição |
| --- | --- |
| [Logs de eventos do Windows](../agents/data-sources-windows-events.md) | Informações enviadas ao sistema de registro de evento do Windows. |
| [Syslog](../agents/data-sources-syslog.md)                     | Informações enviadas ao sistema de registro de evento do Linux. |
| [Desempenho](../agents/data-sources-performance-counters.md)  | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| [Logs do IIS](../agents/data-sources-iis-logs.md)                 | Informações de uso para sites do IIS em execução no sistema operacional convidado. |
| [Logs personalizados](../agents/data-sources-custom-logs.md)           | Eventos de arquivos de texto tanto em computadores Windows quanto Linux. |

## <a name="data-destinations"></a>Destinos de dados

O Agente do Log Analytics envia dados para um workspace do Log Analytics no Azure Monitor. O agente do Windows pode ser de hospedagem múltipla a fim de enviar dados para vários espaços de trabalho e grupos de gerenciamento do System Center Operations Manager. O agente do Linux pode enviar para um único destino, um espaço de trabalho ou um grupo de gerenciamento.

## <a name="other-services"></a>Outros serviços

O agente para Linux e Windows não é apenas para se conectar a Azure Monitor. Outros serviços, como a central de segurança do Azure e o Azure Sentinel, contam com o agente e seu espaço de trabalho Log Analytics conectado. O agente também dá suporte à automação do Azure para hospedar a função de Hybrid runbook Worker e outros serviços, como [controle de alterações](../../automation/change-tracking/overview.md), [Gerenciamento de atualizações](../../automation/update-management/overview.md)e a [central de segurança do Azure](../../security-center/security-center-introduction.md). Para obter mais informações sobre a função Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Automação do Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Limitações do grupo de gerenciamento e do espaço de trabalho

Consulte [Configurar o agente para relatar a um grupo de gerenciamento de Operations Manager](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) para obter detalhes sobre como conectar um agente a um grupo de gerenciamento de Operations Manager.

* Os agentes do Windows podem se conectar a até quatro espaços de trabalho, mesmo se estiverem conectados a um grupo de gerenciamento System Center Operations Manager.
* O agente do Linux não dá suporte a hospedagem múltipla e só pode se conectar a um único espaço de trabalho ou grupo de gerenciamento.

## <a name="security-limitations"></a>Limitações de segurança

* Os agentes do Windows e Linux oferecem suporte ao [padrão FIPS 140](/windows/security/threat-protection/fips-140-validation), mas [outros tipos de proteção podem não ter suporte](../agents/agent-linux.md#supported-linux-hardening).

## <a name="installation-options"></a>Opções de instalação

Há vários métodos para instalar o Agente do Log Analytics e conectar seu computador ao Azure Monitor, dependendo dos requisitos. As seções a seguir listam os possíveis métodos para diferentes tipos de máquina virtual.

> [!NOTE]
> Não há suporte para clonar um computador com o agente de Log Analytics já configurado. Se o agente já tiver sido associado a um espaço de trabalho, isso não funcionará para ' imagens Golden '.

### <a name="azure-virtual-machine"></a>Máquina virtual do Azure

- O [Revisions da VM](../vm/vminsights-enable-overview.md) fornece vários métodos que habilitam agentes em escala. Isso inclui a instalação do agente de Log Analytics e do agente de dependência. 
- [A central de segurança do Azure pode provisionar o agente de log Analytics](../../security-center/security-center-enable-data-collection.md) em todas as VMs do Azure com suporte e quaisquer novas que forem criadas se você habilitá-la para monitorar vulnerabilidades de segurança e ameaças.
- Log Analytics extensão de VM para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) pode ser instalada com o portal do Azure, CLI do Azure, Azure PowerShell ou um modelo de Azure Resource Manager.
- Instale o para máquinas virtuais individuais do Azure [manualmente a partir do portal do Azure](../vm/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Máquina virtual do Windows local ou em outra nuvem

- Use os [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md) para implantar e gerenciar a extensão de VM log Analytics.
- [Instale manualmente](../agents/agent-windows.md) o agente da linha de comando.
- Automatize a instalação com o [Azure DSC de automação](../agents/agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Usar um [modelo do Resource Manager com Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Máquina virtual Linux local ou em outra nuvem

- Use os [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md) para implantar e gerenciar a extensão de VM log Analytics.
- [Instale manualmente](../vm/quick-collect-linux-computer.md) o agente chamando um script wrapper hospedado no github.
- Integre [System Center Operations Manager](./om-agents.md) com Azure monitor para encaminhar dados coletados de computadores com Windows relatando a um grupo de gerenciamento.

## <a name="workspace-id-and-key"></a>ID e chave do espaço de trabalho

Independentemente do método de instalação usado, você precisará da ID e da chave do espaço de trabalho para o espaço de trabalho Log Analytics ao qual o agente se conectará. Selecione o espaço de trabalho no menu **log Analytics espaços de trabalho** na portal do Azure. Em seguida, selecione **Gerenciamento de agentes** na seção **configurações** . 

[![Detalhes do Workspace](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para os logs do Azure Monitor, incentivamos você a configurar o agente para usar pelo menos o protocolo TLS 1.2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**.  Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](../logs/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Requisitos de rede

O agente para Linux e Windows comunica a saída para o serviço de Azure Monitor pela porta TCP 443. Se o computador se conectar por meio de um firewall ou servidor proxy para se comunicar pela Internet, revise os requisitos abaixo para entender a configuração de rede necessária. Se suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet, você poderá configurar um [log Analytics gateway](gateway.md) e, em seguida, configurar o agente para se conectar por meio do gateway para Azure monitor. O agente pode receber informações de configuração e enviar dados coletados.

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

A tabela a seguir lista as informações de configuração de proxy e firewall necessárias para que os agentes do Linux e do Windows se comuniquem com os logs de Azure Monitor.

### <a name="firewall-requirements"></a>Requisitos de firewall

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |
|*.azure-automation.net |Porta 443 |Saída|Sim |

Para obter informações de firewall necessárias para o Azure Governamental, confira [Gerenciamento do Azure Governamental](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Se você planeja usar o Hybrid Runbook Worker de automação do Azure para se conectar e registrar-se com o serviço de automação para usar runbooks ou recursos de gerenciamento em seu ambiente, ele deve ter acesso ao número da porta e às URLs descritas em [configurar sua rede para o Hybrid runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning).

### <a name="proxy-configuration"></a>Configuração de Proxy

O agente do Windows e do Linux é compatível com a comunicação por meio de um servidor proxy ou de um gateway do Log Analytics para o Azure Monitor usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  Para o agente do Windows conectado diretamente ao serviço, a configuração do proxy é especificada durante a instalação ou [após a implementação](../agents/agent-manage.md#update-proxy-settings) no Painel de Controle ou no PowerShell.  

Para o agente Linux, o servidor proxy pode ser especificado durante a instalação ou modificando o arquivo de configuração proxy.conf [após a instalação](../agents/agent-manage.md#update-proxy-settings). O valor de configuração de proxy do agente do Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | HTTPS |
|usuário | Nome de usuário opcional para autenticação de proxy |
|password | Senha opcional para autenticação de proxy |
|proxyhost | Endereço ou FQDN do servidor proxy/gateway do Log Analytics |
|porta | Número da porta opcional para o servidor proxy/gateway do Log Analytics |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se você usar caracteres especiais, como “\@” em sua senha, receberá um erro de conexão de proxy porque o valor será analisado incorretamente.  Para contornar esse problema, codifique a senha na URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  

## <a name="next-steps"></a>Próximas etapas

* Consultar as [fontes de dados](../agents/agent-data-sources.md) para entender as fontes de dados disponíveis para coletar dados do sistema Windows ou Linux. 
* Saiba mais sobre [registrar consultas](../logs/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 
* Conheça as [soluções de monitoramento](../insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também coletam dados no espaço de trabalho do Log Analytics.
