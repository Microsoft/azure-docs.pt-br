---
title: Como plotar o desempenho com insights da VM
description: O desempenho é um recurso das informações da VM que descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre como usá-lo em vários cenários.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/31/2020
ms.openlocfilehash: 02cb6a0c69fef1a33dd9327ddbd86e02b376a60e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046458"
---
# <a name="how-to-chart-performance-with-vm-insights"></a>Como plotar o desempenho com insights da VM

O virtual insights inclui um conjunto de gráficos de desempenho que visam vários KPIs (indicadores chave de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Os gráficos mostram a utilização de recursos durante um período de tempo para que você possa identificar afunilamentos, anomalias ou alternar para uma perspectiva listando cada máquina para exibir a utilização de recursos com base na métrica selecionada. Embora haja inúmeros elementos a serem considerados ao lidar com o desempenho, o virtual insights monitora os principais indicadores de desempenho do sistema operacional relacionados ao processador, à memória, ao adaptador de rede e à utilização de disco. O desempenho complementa o recurso de monitoramento de integridade e ajuda a expor problemas que indicam uma possível falha do componente do sistema, suporte ao ajuste e otimização para obter eficiência ou suportar o planejamento da capacidade.  

## <a name="limitations"></a>Limitações
A seguir estão as limitações na coleta de desempenho com o VM insights.

- **A memória disponível** não está disponível para máquinas virtuais que executam o Red Hat Linux (RHEL) 6. Essa métrica é calculada de **MemAvailable** que foi introduzido no [kernel versão 3.14](http://www.man7.org/linux/man-pages/man1/free.1.html).
- As métricas só estão disponíveis para discos de dados em máquinas virtuais do Linux usando o sistema de arquivos XFS ou a família de sistema de arquivos EXT (EXT2, EXT3, EXT4).

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspectiva de várias VMs do Azure Monitor

No Azure Monitor, o recurso Desempenho fornece uma exibição de todas as VMs monitoradas implementadas em grupos de trabalho em suas assinaturas ou em seu ambiente. Para acessar do Monitor do Azure, execute as etapas a seguir. 

1. No portal do Azure, selecione **Monitor**. 
2. Escolha **Máquinas Virtuais** na seção **Soluções**.
3. Selecione o **desempenho** guia.

![Exibição de desempenho superior N lista de insights VM](media/vminsights-performance/vminsights-performance-aggview-01.png)

Na guia **Top N Charts**, se você tiver mais de um workspace do Log Analytics, escolha a área de trabalho habilitada com a solução do seletor **Workspace** na parte superior da página. O seletor **Grupo** retornará assinaturas, grupos de recursos, [grupos de computadores](../logs/computer-groups.md) e conjuntos de computadores em escala virtual relacionados ao workspace selecionado que você pode usar para filtrar ainda mais os resultados apresentados nos gráficos esta página e nas outras páginas. Sua seleção só se aplica ao recurso Performance e não é transferida para Health ou Map.  

Por padrão, os gráficos mostram as últimas 24 horas. Usando o seletor **TimeRange**, você pode consultar intervalos de tempo históricos de até 30 dias para mostrar como o desempenho parecia no passado.

Os gráficos de utilização de cinco capacidade mostrados na página são:

* % De utilização da CPU - mostra as cinco principais máquinas com a utilização média mais alta do processador 
* Memória disponível - mostra as cinco principais máquinas com a menor quantidade média de memória disponível 
* Espaço em disco lógico usado% - mostra as cinco principais máquinas com o maior espaço em disco usado% em todos os volumes de disco 
* Bytes Sent Rate - mostra as cinco principais máquinas com a maior média de bytes enviados 
* Taxa de Recebimento em Bytes - mostra as cinco principais máquinas com a maior média de bytes recebidos 

Clicar no ícone de pino no canto superior direito de um dos cinco gráficos fixará o gráfico selecionado no último painel do Azure que você exibiu por último.  No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel irá redirecioná-lo para o VM insights e carregar o escopo e a exibição corretos.  

Clique no ícone localizado à esquerda do ícone de pino em um dos cinco gráficos para abrir a exibição **Lista de N Primeiros**.  Aqui você pode ver a utilização de recursos para essa métrica de desempenho por VM individual em uma exibição de lista e qual máquina está tendências mais alta.  

![Exibição de lista de N superior de uma métrica de desempenho selecionados](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando você clica na máquina virtual, o painel **Propriedades** é expandido à direita para mostrar as propriedades do item selecionado, como as informações do sistema relatadas pelo sistema operacional, as propriedades da VM do Azure etc. Clicar em uma das opções na seção **Links rápidos** o redirecionará para esse recurso diretamente da VM selecionada.  

![Painel de propriedades da máquina virtual](./media/vminsights-performance/vminsights-properties-pane-01.png)

Alterne para a guia **Gráficos agregados** para visualizar as métricas de desempenho filtradas por medidas médias ou de percentis.  

![Visão geral do desempenho das informações de VM](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Os seguintes gráficos de utilização de capacidade são fornecidos:

* CPU Utilization% - padrões que mostram o percentual médio e superior 95 
* Memória disponível - padrões mostrando a média, o 5º e o 10º percentil 
* Espaço em disco lógico usado% - padrões mostrando a média e o percentil 95 
* Bytes Sent Rate - padrões mostrando os bytes médios enviados 
* Taxa de recebimento de bytes - padrões que mostra a média de bytes recebidos

Você também pode alterar a granularidade dos gráficos dentro do intervalo de tempo, selecionando **Avg**, **Min**, **Max**, **percentis 50**,  **90 º**, e **95 º** no seletor de percentil.

Para exibir a utilização de recursos por VM individual em uma exibição de lista e ver qual máquina está tendências com maior utilização, selecione a guia **Top N lista**.  A página **Top N List** mostra as 20 principais máquinas classificadas pelas mais utilizadas pelo percentil 95 da métrica *CPU Utilization%* .  Você pode ver mais máquinas, selecionando **carga mais**, e os resultados se expandem para mostrar as máquinas da parte superior a 500. 

>[!NOTE]
>A lista não é possível mostrar mais de 500 máquinas cada vez.  
>

![Exemplo de página de lista de N superior](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Para filtrar os resultados em uma máquina virtual específica na lista, insira o nome do computador na caixa de texto **Pesquisar por nome**.  

Se você em vez disso, seria exibir utilização de uma métrica de desempenho diferentes, do **métrica** lista suspensa, selecione **memória disponível**, **% de espaço em disco lógico usado**,  **Bytes/s de recebimento de rede**, ou **bytes/s de rede enviados** e a lista é atualizada para mostrar a utilização no escopo dessa métrica.  

A seleção de uma máquina virtual a partir da lista abre o painel **Propriedades** no lado direito da página e, a partir daqui, você pode selecionar **Detalhes de desempenho**.  A página **Detalhes da Máquina Virtual** é aberta e tem o escopo definido para essa VM, com experiência semelhante ao acessar o VM Insights Performance diretamente da VM do Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Exibir desempenho diretamente de uma VM do Azure

Para acessar diretamente a partir de uma máquina virtual, execute o seguinte procedimento.

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha uma máquina virtual e, na seção **Monitoramento**, escolha **Insights**.  
3. Selecione o **desempenho** guia. 

Esta página inclui não apenas gráficos de utilização de desempenho, mas também uma tabela mostrando cada disco lógico descoberto, sua capacidade, utilização e média total de cada medida.  

Os seguintes gráficos de utilização de capacidade são fornecidos:

* CPU Utilization% - padrões que mostram o percentual médio e superior 95 
* Memória disponível - padrões mostrando a média, o 5º e o 10º percentil 
* Espaço em disco lógico usado% - padrões mostrando a média e o percentil 95 
* IOPS de disco lógico - padrões mostrando a média e o percentil 95
* MB / s de disco lógico - padrões mostrando a média e o percentil 95
* Max Logical Disk Used% - padrões mostrando a média e o percentil 95
* Bytes Sent Rate - padrões mostrando os bytes médios enviados 
* Taxa de recebimento de bytes - padrões que mostra a média de bytes recebidos

Clicar no ícone de pino no canto superior direito de um dos gráficos fixará o gráfico selecionado no último painel do Azure que você exibiu. No painel, você pode redimensionar e reposicionar o gráfico. Selecionar o gráfico no painel redireciona você para o VM insights e carrega a exibição de detalhes de desempenho para a VM.  

![Diretamente o insights VM-desempenho da VM a exibir](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Exibir desempenho diretamente de um conjunto de dimensionamento de máquinas virtuais do Azure

Para acessar diretamente a partir de um conjunto de dimensionamento de máquinas virtuais do Azure, execute o seguinte procedimento.

1. No portal do Azure, selecione **Conjuntos de dimensionamento de máquinas virtuais**.
2. Na lista, escolha uma máquina virtual e, na seção **Monitoramento**, escolha **Insights** para ver a guia **Desempenho**.

Esta página carrega a exibição de desempenho do Azure Monitor, no escopo do conjunto de dimensionamento selecionado. Isso permite que você veja as primeiras N instâncias no conjunto de dimensionamento no conjunto de métricas monitoradas, exiba o desempenho agregado em todo o conjunto de dimensionamento e veja as tendências para métricas selecionadas em todas as instâncias individuais no conjunto de dimensionamento. A seleção de uma instância na exibição de lista permite que você carregue o mapa ou navegue até um modo de exibição de desempenho detalhado para essa instância.

Clicar no ícone de pino no canto superior direito de um dos gráficos fixará o gráfico selecionado no último painel do Azure que você exibiu. No painel, você pode redimensionar e reposicionar o gráfico. Selecionar o gráfico no painel redireciona você para o VM insights e carrega a exibição de detalhes de desempenho para a VM.  

![Desempenho de insights de VM diretamente da exibição de um conjunto de dimensionamento de máquinas virtuais](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Você também pode acessar um modo de exibição de desempenho detalhado para uma instância específica do modo de exibição de instâncias para seu conjunto de dimensionamento. Navegue até **Instâncias** na seção **Configurações** e escolha **Insights**.



## <a name="next-steps"></a>Próximas etapas

- Saiba como usar [pastas de trabalho](vminsights-workbooks.md) incluídas com o virtual insights para analisar ainda mais as métricas de desempenho e rede.  

- Para saber mais sobre dependências de aplicativos descobertos, consulte [Exibir o mapa de informações de VM](vminsights-maps.md).
