---
title: "Solução de capacidade e desempenho do Log Analytics do Azure | Microsoft Docs"
description: "Use a solução de capacidade e desempenho no Log Analytics para ajudá-lo a compreender a capacidade dos servidores Hyper-V."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: banders
ms.openlocfilehash: 5ca005127721092b8efcf0ac83cc967ab15fe72d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Planeje a capacidade da máquina virtual do Hyper-V com a solução de capacidade e desempenho (visualização)

![Símbolo de capacidade e desempenho](./media/log-analytics-capacity/capacity-solution.png)

Você pode usar a solução de capacidade e desempenho no Log Analytics para ajudá-lo a compreender a capacidade dos servidores Hyper-V. A solução fornece percepções sobre seu ambiente Hyper-V e apresentando uma visão geral da utilização (CPU, memória e disco) dos hosts e das VMs em execução nos hosts do Hyper-V. As métricas são coletadas para a CPU, memória e discos em todos os hosts e VMs em execução.

A solução:

-   Mostra os hosts com maior e menor utilização de CPU e memória
-   Mostra as VMs com maior e menor utilização de CPU e memória
-   Mostra as VMs com a maior e menor utilização da taxa de transferência e IOPS
-   Mostra quais VMs são executadas em quais hosts
-   Mostra os discos superiores com alta taxa de transferência, IOPS e latência em volumes compartilhados do cluster
- Permite personalizar e filtrar com base em grupos

> [!NOTE]
> A versão anterior da solução de capacidade e desempenho chamada Gerenciamento de capacidade requer tanto o System Center Operations Manager quanto o System Center Virtual Machine Manager. A solução atualizada não tem essas dependências.


## <a name="connected-sources"></a>Fontes conectadas

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suporte | Descrição |
|---|---|---|
| [Agentes do Windows](log-analytics-windows-agents.md) | Sim | A solução coleta informações de dados de desempenho e capacidade de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não    | A solução não coleta informações de dados de desempenho e capacidade de agentes do Linux diretos.|
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Sim |A solução coleta dados de desempenho e capacidade de agentes em um grupo de gerenciamento do SCOM conectado. Não é necessário ter uma conexão direta do agente SCOM com o OMS. Os dados são encaminhados do grupo de gerenciamento para o repositório do OMS.|
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | O armazenamento do Azure não inclui dados de desempenho, nem de capacidade.|

## <a name="prerequisites"></a>Pré-requisitos

- O Windows ou agentes do Operations Manager devem ser instalados no Windows Server 2012 ou nos hosts superiores do Hyper-V, e não nas máquinas virtuais.


## <a name="configuration"></a>Configuração

Execute o seguinte procedimento para adicionar a solução de capacidade e desempenho ao seu espaço de trabalho.

- Siga o processo descrito em [Como adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar a solução de capacidade e desempenho ao seu espaço de trabalho do OMS.

## <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do SCOM estiver conectado ao seu espaço de trabalho do OMS, os pacotes de gerenciamento a seguir serão instalados no SCOM quando você adicionar essa solução. Não é necessária nenhuma configuração nem a manutenção desses pacotes de gerenciamento.

- Microsoft.IntelligencePacks.CapacityPerformance

O evento 1201 é semelhante:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Quando a solução de capacidade e desempenho é atualizada, o número da versão é alterado.

Para obter mais informações sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md).

## <a name="using-the-solution"></a>Usando a solução

Ao adicionar a solução de capacidade e desempenho ao seu espaço de trabalho, a capacidade e o desempenho aparecem no seu painel de Visão geral. Esse bloco exibe uma contagem do número de hosts do Hyper-V ativos no momento e o número de máquinas virtuais ativas monitoradas durante o período selecionado.

![Bloco de capacidade e desempenho](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Como revisar a utilização

Clique no bloco de Capacidade e Desempenho para abrir o painel de capacidade e desempenho. O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais itens que correspondem aos critérios da coluna para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de log que retorna todos os registros clicando em **Ver todos** na parte inferior da coluna ou clicando no cabeçalho de coluna.

- **Hosts**
    - **Utilização de CPU de host** Mostra uma tendência gráfica de utilização da CPU dos computadores host e uma lista de hosts com base no período selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em um nome de host para abrir a pesquisa de logs e exibir os detalhes do contador da CPU das VMs hospedadas.
    - **Utilização de memória de host** Mostra uma tendência gráfica de utilização de memória de computadores host e uma lista de hosts com base no período selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em um nome de host para abrir a pesquisa de logs e exibir os detalhes do contador da memória das VMs hospedadas.
- **Máquinas virtuais**
    - **Utilização de CPU da VM** mostra uma tendência gráfica da utilização da CPU de máquinas virtuais e uma lista de máquinas virtuais com base no período selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as 3 VMs superiores. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome da VM para abrir a pesquisa de logs e exibir detalhes do contador da CPU agregada para a VM.
    - **Utilização de memória de VM** Mostra uma tendência gráfica de utilização de memória das máquinas virtuais e uma lista de máquinas virtuais com base no período selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as 3 VMs superiores. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome da VM para abrir a pesquisa de logs e exibir detalhes do contador da memória agregada para a VM.
    - **IOPS de disco total da VM** mostra uma tendência gráfica do IOPS de disco total para máquinas virtuais e uma lista de máquinas virtuais com o IOPS para cada uma delas com base no período selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as 3 VMs superiores. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome da VM para abrir a pesquisa de logs e exibir os detalhes do contador de IOPS de disco agregado para a VM.
    - **Taxa de transferência de disco total da VM** mostra uma tendência gráfica da taxa de transferência total do disco para máquinas virtuais e uma lista de máquinas virtuais com a taxa de transferência total para cada uma delas com base no período selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as 3 VMs superiores. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome da VM para abrir a pesquisa de logs e exibir detalhes do contador da taxa de transferência de disco total agregada para a máquina virtual.
- **Volumes compartilhados clusterizados**
    - **Taxa de transferência total** mostra a soma da leitura e escrita em volumes compartilhados clusterizados.
    - **IOPS total** mostra a soma das operações de entrada/saída por segundo em volumes compartilhados clusterizados.
    - **Latência total** mostra a latência total nos volumes compartilhados clusterizados.
- **Densidade de host** o bloco superior mostra o número total de hosts e de máquinas virtuais disponíveis para a solução. Clique no bloco superior para exibir detalhes adicionais na pesquisa de logs. Também lista todos os hosts e o número de máquinas virtuais que são hospedados. Clique em um host para analisar os resultados de VM em uma pesquisa de logs.


![folha de Hosts do painel](./media/log-analytics-capacity/dashboard-hosts.png)

![folha de máquinas virtuais do painel](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Desempenho da avaliação

Ambientes de computação de produção diferem muito de uma organização para outra. Além disso, a capacidade e o desempenho para cargas de trabalho podem depender de como as suas VMs estão operando, e o que você considera normal. Os procedimentos específicos para ajudá-lo a medir o desempenho provavelmente não se aplicam ao seu ambiente. Portanto, orientações prescritivas mais generalizadas são mais adequadas para ajudar. A Microsoft publica uma variedade de artigos de orientações prescritivas para ajudar a medir o seu desempenho.

Para resumir, a solução coleta dados de desempenho e capacidade de uma variedade de fontes, incluindo contadores de desempenho. Use esses dados de desempenho e capacidade apresentados em várias superfícies na solução e compare seus resultados com os do artigo [Medição de desempenho no Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx). Apesar deste artigo ter sido publicado há algum tempo, as métricas, considerações e diretrizes ainda são válidas. O artigo contém links para outros recursos úteis.


## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

A tabela a seguir fornece as pesquisas de log de exemplo para dados de desempenho e capacidade coletados e calculados pela solução.

| Consultar | Descrição |
|---|---|
| Todas as configurações de memória do host | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="Host Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Todas as configurações de memória da VM | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="VM Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Divisão dos IOPS de disco totais em todas as VMs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Reads/s" OR CounterName="VHD Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão da taxa de transferência de disco total em todas as VMs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Read MB/s" OR CounterName="VHD Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão de IOPS total em todos os CSVs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Reads/s" OR CounterName="CSV Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão da taxa de transferência total em todos os CSVs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read MB/s" OR CounterName="CSV Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão da latência total em todos os CSVs | <code> Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read Latency" OR CounterName="CSV Write Latency") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |

>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir.

> | Consultar | Descrição |
|:--- |:--- |
| Todas as configurações de memória do host | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "Host Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Todas as configurações de memória da VM | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "VM Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Divisão dos IOPS de disco totais em todas as VMs | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Reads/s" or CounterName == "VHD Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Divisão da taxa de transferência de disco total em todas as VMs | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Read MB/s" or CounterName == "VHD Write MB/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Divisão de IOPS total em todos os CSVs | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Divisão da taxa de transferência total em todos os CSVs | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Divisão da latência total em todos os CSVs | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Read Latency" or CounterName == "CSV Write Latency") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Próximas etapas
* Use [pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados de capacidade e desempenho.
