---
title: Solucionar problemas de VM insights
description: Solucionar problemas de instalação do VM insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555233"
---
# <a name="troubleshoot-vm-insights"></a>Solucionar problemas de VM insights
Este artigo fornece informações de solução de problemas para quando você tiver problemas ao habilitar ou usar o VM insights.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Não é possível habilitar as informações da VM em um computador
Ao integrar uma máquina virtual do Azure da portal do Azure, ocorrem as seguintes etapas:

- Um espaço de trabalho de Log Analytics padrão será criado se essa opção tiver sido selecionada.
- O agente de Log Analytics é instalado em máquinas virtuais do Azure usando uma extensão de VM se o agente já estiver instalado.
- O Dependency Agent é instalado em máquinas virtuais do Azure usando uma extensão, se determinado for necessário.
  
Durante o processo de integração, cada uma dessas etapas é verificada e um status de notificação mostrado no Portal. A configuração do workspace e a instalação do agente normalmente levam de 5 a 10 minutos. Levará mais 5 a 10 minutos para que os dados fiquem disponíveis para exibição no Portal.

Se você receber uma mensagem informando que a máquina virtual precisa ser integrada depois que você tiver executado o processo de integração, aguarde até 30 minutos para que o processo seja concluído. Se o problema persistir, consulte as seções a seguir para obter as possíveis causas.

### <a name="is-the-virtual-machine-running"></a>A máquina virtual está em execução?
 Se a máquina virtual tiver sido desligada por um tempo, estiver desativada no momento ou tiver sido ativada recentemente, você não terá dados a serem exibidos por um pouco até chegar aos dados.

### <a name="is-the-operating-system-supported"></a>Há suporte para o sistema operacional?
Se o sistema operacional não estiver na lista de [sistemas operacionais com suporte](vminsights-enable-overview.md#supported-operating-systems) , a extensão não será instalada e você verá essa mensagem que estamos aguardando a chegada dos dados.

### <a name="did-the-extension-install-properly"></a>A extensão foi instalada corretamente?
Se você ainda vir uma mensagem informando que a máquina virtual precisa ser integrada, isso pode significar que uma ou ambas as extensões não foram instaladas corretamente. Verifique a página de **extensões** de sua máquina virtual no portal do Azure para verificar se as extensões a seguir estão listadas.

| Sistema operacional | Agentes | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Se você não vir as duas extensões para seu sistema operacional na lista de extensões instaladas, elas precisarão ser instaladas. Se as extensões estiverem listadas, mas seu status não aparecer conforme o *provisionamento for bem-sucedido*, a extensão deverá ser removida e reinstalada.

### <a name="do-you-have-connectivity-issues"></a>Você tem problemas de conectividade?
Para computadores Windows, você pode usar a ferramenta  *TestCloudConnectivity* para identificar o problema de conectividade. Essa ferramenta é instalada por padrão com o agente na pasta *%systemroot%\Program Comuns\microsoft Monitoring Agent\Agent*. Execute a ferramenta em um prompt de comandos com privilégios elevados. Ele retornará os resultados e realçará o local em que o teste falha. 

![Ferramenta TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Mais solução de problemas de agente

Consulte os seguintes artigos para solucionar problemas com o agente de Log Analytics:

- [Como solucionar problemas com o agente do Log Analytics para Windows](../agents/agent-windows-troubleshoot.md)
- [Como solucionar problemas com o agente do Log Analytics para Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>O modo de exibição de desempenho não tem dados
Se os agentes parecerem estar instalados corretamente, mas você não vir nenhum dado no modo de exibição de desempenho, consulte as seções a seguir para ver as possíveis causas.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Seu espaço de trabalho de Log Analytics atingiu seu limite de dados?
Verifique as [reservas de capacidade e o preço da ingestão de dados](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Sua máquina virtual está enviando dados de desempenho e de log para Azure Monitor logs?

Abra Log Analytics de **logs** no menu Azure Monitor na portal do Azure. Execute a seguinte consulta para seu computador:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Se você não vir nenhum dado, poderá ter problemas com o agente. Consulte a seção acima para obter informações de solução de problemas do agente.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>A máquina virtual não aparece na exibição de mapa

### <a name="is-the-dependency-agent-installed"></a>O agente de dependência está instalado?
 Use as informações nas seções acima para determinar se o agente de dependência está instalado e funcionando corretamente.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Você está na camada gratuita Log Analytics?
A [camada gratuita log Analytics](https://azure.microsoft.com/pricing/details/monitor/) é um plano de preços herdado que permite até cinco máquinas de mapa do serviço exclusivas. Quaisquer servidores subsequentes serão exibidos no Mapa do Serviço, mesmo que os cinco anteriores não estejam enviando dados.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Sua máquina virtual está enviando dados de desempenho e de log para Azure Monitor logs?
Use a consulta de log na [exibição de desempenho não tem nenhuma](#performance-view-has-no-data) seção de dados para determinar se os dados estão sendo coletados para a máquina virtual. Se os dados não estiverem sendo coletados, use a ferramenta TestCloudConnectivity descrita acima para determinar se você tem problemas de conectividade.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>A máquina virtual aparece no modo de exibição de mapa, mas tem dados ausentes
Se a máquina virtual estiver na exibição de mapa, o agente de dependência será instalado e em execução, mas o driver de kernel não foi carregado. Verifique o arquivo de log nos seguintes locais:

| Sistema operacional | Log | 
|:---|:---|
| Windows | C:\Arquivos de Programas\microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

As últimas linhas do arquivo devem indicar por que o kernel não foi carregado. Por exemplo, se o kernel tiver sido atualizado por você, talvez o Linux não dê suporte a ele.
## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre a integração de agentes de informações de VM, consulte [habilitar visão geral do VM insights](vminsights-enable-overview.md).
