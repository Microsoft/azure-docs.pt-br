---
title: Visão geral do agente de Azure Monitor
description: Visão geral do AMA (agente de Azure Monitor), que coleta dados de monitoramento do sistema operacional convidado de máquinas virtuais.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 0487e5bd8109c02605e785e74d45589dd5fde5b9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039590"
---
# <a name="azure-monitor-agent-overview-preview"></a>Visão geral do agente de Azure Monitor (versão prévia)
O agente de Azure Monitor (AMA) coleta dados de monitoramento do sistema operacional convidado de máquinas virtuais e as entrega ao Azure Monitor. Este artigo fornece uma visão geral do agente de Azure Monitor, incluindo como instalá-lo e como configurar a coleta de dados.

## <a name="relationship-to-other-agents"></a>Relação com outros agentes
O agente de Azure Monitor substitui os seguintes agentes que são usados atualmente pelo Azure Monitor para coletar dados de convidado de máquinas virtuais:

- [Log Analytics Agent](./log-analytics-agent.md) – envia dados para log Analytics espaço de trabalho e oferece suporte a soluções de monitoramento e informações de VM.
- [Extensão de diagnóstico](./diagnostics-extension-overview.md) – envia dados para Azure monitor métricas (somente Windows), hubs de eventos do Azure e armazenamento do Azure.
- [Agente Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) – envia dados para Azure monitor métricas (somente Linux).

Além de consolidar essa funcionalidade em um único agente, o agente de Azure Monitor fornece os seguintes benefícios em relação aos agentes existentes:

- Escopo do monitoramento. Configure centralmente a coleta para diferentes conjuntos de dados de diferentes conjuntos de VMs.  
- Hospedagem múltipla do Linux: enviar dados de VMs do Linux para vários espaços de trabalho.
- Filtragem de eventos do Windows: use consultas XPATH para filtrar quais eventos do Windows são coletados.
- Gerenciamento de extensão aprimorado: o agente de Azure Monitor usa um novo método de tratamento de extensibilidade que é mais transparente e controlável do que os pacotes de gerenciamento e plug-ins do Linux nos agentes de Log Analytics atuais.

### <a name="changes-in-data-collection"></a>Alterações na coleta de dados
Os métodos para definir a coleta de dados para os agentes existentes são distintos diferentes uns dos outros, e cada um tem desafios que são resolvidos com o agente de Azure Monitor.

- Log Analytics Agent obtém sua configuração de um espaço de trabalho Log Analytics. Isso é fácil de configurar de forma centralizada, mas difícil de definir definições independentes para diferentes máquinas virtuais. Ele só pode enviar dados para um espaço de trabalho Log Analytics.

- A extensão de diagnóstico tem uma configuração para cada máquina virtual. Isso é fácil de definir definições independentes para diferentes máquinas virtuais, mas difícil de gerenciar de forma centralizada. Ele só pode enviar dados para Azure Monitor métricas, hubs de eventos do Azure ou armazenamento do Azure. Para agentes do Linux, o agente Telegraf de código-fonte aberto é necessário para enviar dados para Azure Monitor métricas.

O agente de Azure Monitor usa [as regras de coleta de dados (DCR)](data-collection-rule-overview.md) para configurar os dados a serem coletados de cada agente. As regras de coleta de dados habilitam a gerenciabilidade das configurações de coleta em escala e ainda habilitam configurações exclusivas e com escopo para subconjuntos de computadores. Eles são independentes do espaço de trabalho e independentes da máquina virtual, o que permite que eles sejam definidos uma vez e reutilizados em computadores e ambientes. Consulte [Configurar a coleta de dados para o agente de Azure monitor (versão prévia)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Devo mudar para Azure Monitor Agent?
O agente de Azure Monitor coexiste com os [agentes geralmente disponíveis para Azure monitor](agents-overview.md), mas você pode considerar a transição de suas VMs para fora dos agentes atuais durante o período de visualização pública do Azure monitor Agent. Considere os seguintes fatores ao fazer essa determinação.

- **Requisitos de ambiente.** O agente de Azure Monitor tem um conjunto mais limitado de sistemas operacionais, ambientes e requisitos de rede com suporte do que os agentes atuais. O suporte de ambiente futuro, como novas versões de sistema operacional e tipos de requisitos de rede, provavelmente será fornecido apenas no agente Azure Monitor. Você deve avaliar se o seu ambiente tem suporte do Azure Monitor Agent. Caso contrário, você precisará continuar com o agente atual. Se Azure Monitor Agent der suporte ao seu ambiente atual, você deverá considerar a transição para ele.
- **Tolerância a riscos de visualização pública.** Embora o agente de Azure Monitor tenha sido totalmente testado para os cenários com suporte no momento, o agente ainda está em visualização pública. As atualizações de versão e melhorias de funcionalidade ocorrerão com frequência e podem apresentar bugs. Você deve avaliar o risco de um bug no agente em suas VMs que podem parar a coleta de dados. Se uma lacuna na coleta de dados não tiver um impacto significativo sobre seus serviços, continue com o agente de Azure Monitor. Se você tiver uma tolerância baixa para qualquer instabilidade, você deverá permanecer com os agentes geralmente disponíveis até que Azure Monitor agente atinja esse status.
- **Requisitos de recursos atuais e novos.** O agente de Azure Monitor introduz vários recursos novos, como filtragem, escopo e hospedagem múltipla, mas ainda não está na paridade com os agentes atuais para outras funcionalidades, como a coleta de log personalizada e a integração com soluções. A maioria dos novos recursos do Azure Monitor só será disponibilizada com o agente do Azure Monitor, assim, ao longo do tempo, mais funcionalidades só estarão disponíveis no novo agente. Você deve considerar se Azure Monitor Agent tem os recursos necessários e se há alguns recursos que você pode fazer temporariamente sem para obter outros recursos importantes no novo agente. Se Azure Monitor Agent tiver todos os recursos principais que você precisa, considere a possibilidade de fazer a transição para ele. Se houver recursos críticos que você precisa, continue com o agente atual até que Azure Monitor agente atinja a paridade.
- **Tolerância para retrabalho.** Se você estiver configurando um novo ambiente com recursos como scripts de implantação e modelos de integração, você deve considerar se você poderá retrabalhar com eles quando Azure Monitor agente ficar disponível ao público geral. Se o esforço para essa retrabalho for mínimo, continue com os agentes atuais por enquanto. Se for necessário uma quantidade significativa de trabalho, considere configurar seu novo ambiente com o novo agente. Espera-se que o agente de Azure Monitor seja disponibilizado para o público geral e uma data de reprovação publicada para os agentes de Log Analytics no 2021. Os agentes atuais terão suporte por vários anos após o início da reprovação.



## <a name="current-limitations"></a>Limitações atuais
As seguintes limitações se aplicam durante a visualização pública do agente de Azure Monitor:

- O agente de Azure Monitor não oferece suporte a soluções e informações como o virtual insights e a central de segurança do Azure. O único cenário com suporte no momento é coletar dados usando as regras de coleta de dados que você configura. 
- As regras de coleta de dados devem ser criadas na mesma região que qualquer Log Analytics espaço de trabalho usado como destino.
- Atualmente, há suporte para máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais e servidores habilitados para Arc do Azure. O serviço kubernetes do Azure e outros tipos de recursos de computação não têm suporte no momento.
- A máquina virtual deve ter acesso aos seguintes pontos de extremidade HTTPS:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Coexistência com outros agentes
O agente de Azure Monitor pode coexistir com os agentes existentes para que você possa continuar a usar sua funcionalidade existente durante a avaliação ou migração. Isso é particularmente importante devido às limitações da visualização pública no suporte a soluções existentes. Você deve ter cuidado ao coletar dados duplicados, pois isso poderia distorcer os resultados da consulta e resultar em cobranças adicionais para a ingestão e retenção de dados.

Por exemplo, o Revisions de VM usa o agente de Log Analytics para enviar dados de desempenho para um espaço de trabalho Log Analytics. Você também pode ter configurado o espaço de trabalho para coletar eventos do Windows e de syslog de agentes. Se você instalar o agente de Azure Monitor e criar uma regra de coleta de dados para esses mesmos eventos e dados de desempenho, isso resultará em dados duplicados.


## <a name="costs"></a>Custos
Não há nenhum custo para Azure Monitor Agent, mas você pode incorrer em encargos pelos dados ingeridos. Consulte [Azure monitor preços](https://azure.microsoft.com/pricing/details/monitor/) para obter detalhes sobre coleta e retenção de dados de log Analytics e métricas de clientes.

## <a name="data-sources-and-destinations"></a>Fontes de dados e destinos
A tabela a seguir lista os tipos de dados que você pode coletar atualmente com o agente de Azure Monitor usando regras de coleta de dados e onde você pode enviar esses dados. Veja [o que é monitorado pelo Azure monitor?](../monitor-reference.md) para obter uma lista de informações, soluções e outras soluções que usam o agente de Azure monitor para coletar outros tipos de dados.


O agente de Azure Monitor envia dados para Azure Monitor métricas ou um espaço de trabalho Log Analytics que oferece suporte a logs de Azure Monitor.

| Fonte de dados | Destinos | Descrição |
|:---|:---|:---|
| Desempenho        | Métricas do Azure Monitor<br>Espaço de trabalho do Log Analytics | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| Log de eventos do Windows | Espaço de trabalho do Log Analytics | Informações enviadas ao sistema de registro de evento do Windows. |
| syslog             | Espaço de trabalho do Log Analytics | Informações enviadas ao sistema de registro de evento do Linux. |


## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
Consulte [sistemas operacionais com suporte](agents-overview.md#supported-operating-systems) para obter uma lista das versões do sistema operacional Windows e Linux com suporte no momento pelo agente de Azure monitor.



## <a name="security"></a>Segurança
O agente de Azure Monitor não requer nenhuma chave, mas requer uma [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Você deve ter uma identidade gerenciada atribuída pelo sistema habilitada em cada máquina virtual antes de implantar o agente.

## <a name="networking"></a>Rede
O agente de Azure Monitor dá suporte a marcas de serviço do Azure (as marcas AzureMonitor e AzureResourceManager são necessárias), mas ainda não funcionam com escopos de links privados Azure Monitor ou proxies diretos.


## <a name="next-steps"></a>Próximas etapas

- [Instale o agente de Azure monitor](azure-monitor-agent-install.md) em máquinas virtuais Windows e Linux.
- [Crie uma regra de coleta de dados](data-collection-rule-azure-monitor-agent.md) para coletar dados do agente e enviá-los para Azure monitor.