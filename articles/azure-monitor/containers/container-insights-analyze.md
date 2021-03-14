---
title: Monitoramento de kubernetes com insights de contêiner | Microsoft Docs
description: Este artigo descreve como você pode exibir e analisar o desempenho de um cluster kubernetes com informações de contêiner.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 865a21e8c54d2cf569e04534fab6ec14f5519f34
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124305"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-container-insights"></a>Monitorar o desempenho do cluster kubernetes com insights de contêiner

Com o contêiner insights, você pode usar os gráficos de desempenho e o status de integridade para monitorar a carga de trabalho de clusters kubernetes hospedados no AKS (serviço kubernetes do Azure), Azure Stack ou outro ambiente de duas perspectivas. Você pode monitorar diretamente do cluster ou pode exibir todos os clusters em uma assinatura do Azure Monitor. A exibição de instâncias de contêiner do Azure também é possível ao monitorar um cluster AKS específico.

Este artigo ajuda você a entender as duas perspectivas e como Azure Monitor ajuda a avaliar, investigar e resolver problemas detectados rapidamente.

Para obter informações sobre como habilitar o insights de contêiner, consulte [insights de contêiner integrados](container-insights-onboard.md).

Azure Monitor fornece uma exibição de vários clusters que mostra o status de integridade de todos os clusters kubernetes monitorados que executam o Linux e o Windows Server 2019 implantados em grupos de recursos em suas assinaturas. Ele mostra os clusters descobertos em todos os ambientes que não são monitorados pela solução. Você pode entender imediatamente a integridade do cluster e, a partir daqui, pode fazer uma busca detalhada na página de desempenho do nó e do controlador ou navegar para ver os gráficos de desempenho do cluster. Para clusters AKS que foram descobertos e identificados como não monitorados, você pode habilitar o monitoramento para eles a qualquer momento.

As principais diferenças no monitoramento de um cluster do Windows Server com informações de contêiner em comparação a um cluster do Linux são descritas [aqui](container-insights-overview.md#what-does-container-insights-provide) no artigo Visão geral.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="multi-cluster-view-from-azure-monitor"></a>Exibição de vários clusters do Azure Monitor

Para exibir o status de integridade de todos os clusters do kubernetes implantados, selecione **Monitor** no painel esquerdo na portal do Azure. Na seção **Insights**, selecione **Contêineres**.

![Exemplo de painel com vários clusters no Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Você pode fazer o escopo dos resultados apresentados na grade para mostrar os clusters que são:

* Clusters **do Azure** -AKS e AKS-Engine hospedados no serviço kubernetes do Azure
* **Azure Stack (versão prévia)** – clusters AKS-Engine hospedados no Azure Stack
* Clusters **não Azure (visualização)** -kubernetes hospedados localmente
* **Todos** -exibe todos os clusters kubernetes hospedados no Azure, Azure Stack e ambientes locais que são integrados a insights de contêiner

Para exibir clusters de um ambiente específico, selecione-o nos **ambientes** do Pill no canto superior esquerdo da página.

![Exemplo de seletor do ambiente do Pill](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Na guia **clusters monitorados** , você aprende o seguinte:

- Quantos clusters estão em um estado crítico ou não íntegro, em comparação com quantas estão íntegras ou não relatando (referido como um estado desconhecido).
- Se todas as implantações do [mecanismo de kubernetes do Azure (AKs-Engine)](https://github.com/Azure/aks-engine) estão íntegras.
- Quantos nós e pods de sistema e de usuário são implantados por cluster.
- Quanto espaço em disco está disponível e se há um problema de capacidade.

Os status de integridade incluídos são:

* **Íntegro**: nenhum problema é detectado para a VM e está funcionando conforme necessário.
* **Crítico**: é detectado um ou mais problemas críticos que devem ser resolvidos para restaurar o estado operacional normal conforme o esperado.
* **Aviso**: um ou mais problemas detectados devem ser resolvidos ou a condição de integridade pode se tornar crítica.
* **Desconhecido**: se o serviço não fosse capaz de fazer uma conexão com o nó ou Pod, o status é alterado para um estado desconhecido.
* **Não encontrado**: o espaço de trabalho, o grupo de recursos ou a assinatura que contém o espaço de trabalho para esta solução foi excluído.
* **Não autorizado**: o usuário não tem as permissões necessárias para ler os dados no espaço de trabalho.
* **Erro**: ocorreu um erro ao tentar ler os dados do espaço de trabalho.
* **Configurado incorretamente: os** insights de contêiner não foram configurados adequadamente no espaço de trabalho especificado.
* **Sem dados**: os dados não foram relatados para o espaço de trabalho nos últimos 30 minutos.

O estado de integridade calcula o status geral do cluster como o *pior dos* três Estados com uma exceção. Se qualquer um dos três Estados for desconhecido, o estado geral do cluster mostrará **desconhecido**.

A tabela a seguir fornece uma análise do cálculo que controla os Estados de integridade de um cluster monitorado na exibição de vários clusters.

| Cluster monitorado |Status |Disponibilidade |
|-------|-------|-----------------|
|**Pod do usuário**| | |
| |Íntegros |100% |
| |Aviso |90 – 99% |
| |Crítico |<90% |
| |Unknown |Se não tiver sido relatado nos últimos 30 minutos |
|**Pod do sistema**| | |
| |Íntegros |100% |
| |Aviso |N/D |
| |Crítico |<100% |
| |Unknown |Se não tiver sido relatado nos últimos 30 minutos |
|**Nó** | | |
| |Íntegros |>85% |
| |Aviso |60 – 84% |
| |Crítico |<60% |
| |Unknown |Se não tiver sido relatado nos últimos 30 minutos |

Na lista de clusters, você pode fazer uma busca detalhada na página do **cluster** selecionando o nome do cluster. Em seguida, vá para a página de desempenho de **nós** selecionando o acúmulo de nós na coluna **nós** para esse cluster específico. Ou, você pode fazer uma busca detalhada na página de desempenho de **controladores** selecionando o acúmulo da coluna **pods do usuário** ou pods do **sistema** .

## <a name="view-performance-directly-from-a-cluster"></a>Exibir o desempenho diretamente de um cluster

O acesso a informações de contêiner está disponível diretamente de um cluster AKs selecionando o cluster **insights**  >   no painel esquerdo, ou quando você selecionou um cluster do modo de exibição de vários clusters. As informações sobre o cluster são organizadas em quatro perspectivas:

- Cluster
- Nós
- Controladores
- Contêineres

>[!NOTE]
>A experiência descrita no restante deste artigo também é aplicável para exibir o status de integridade e desempenho de seus clusters kubernetes hospedados em Azure Stack ou em outro ambiente quando selecionado na exibição de vários clusters.

A página padrão é aberta e exibe quatro gráficos de desempenho de linha que mostram as principais métricas de desempenho do cluster.

![Gráficos de desempenho de exemplo na guia de Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Os gráficos de desempenho exibem quatro métricas de desempenho:

- Utilização de CPU do nó: uma perspectiva agregada da utilização da CPU para todo o cluster. **&nbsp; %** Para filtrar os resultados para o intervalo de tempo, selecione **AVG**, **min**, **50 º**, **90 º**, **95 º** ou **Max** no seletor de percentils acima do gráfico. Os filtros podem ser usados individualmente ou combinados.
- Utilização de memória do nó: uma perspectiva agregada de utilização de memória para todo o cluster. **&nbsp; %** Para filtrar os resultados para o intervalo de tempo, selecione **AVG**, **min**, **50 º**, **90 º**, **95 º** ou **Max** no seletor de percentils acima do gráfico. Os filtros podem ser usados individualmente ou combinados.
- **Contagem de nós**: uma contagem de nós e o status do Kubernetes. Os status dos nós de cluster representados são total, pronto e não pronto. Eles podem ser filtrados individualmente ou combinados no seletor acima do gráfico.
- **Contagem de Pod ativo**: uma contagem de Pod e o status de kubernetes. Os status dos pods representados são total, pendente, em execução, desconhecido, com êxito ou falha. Eles podem ser filtrados individualmente ou combinados no seletor acima do gráfico.

Use as teclas de seta para a esquerda e para a direita para percorrer cada ponto de dados no gráfico. Use as teclas de seta para cima e para baixo para percorrer as linhas percentuais. Selecione o ícone de pino no canto superior direito de qualquer um dos gráficos para fixar o gráfico selecionado no último painel do Azure exibido. No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico do painel redireciona você para o contêiner insights e carrega o escopo e a exibição corretos.

O insights de contêiner também dá suporte a Azure Monitor [métricas Explorer](../essentials/metrics-getting-started.md), onde você pode criar seus próprios gráficos de plotagem, correlacionar e investigar tendências e fixar em painéis. No Metrics Explorer, você também pode usar os critérios definidos para visualizar suas métricas como a base de uma regra de [alerta baseada em métrica](../alerts/alerts-metric.md).

## <a name="view-container-metrics-in-metrics-explorer"></a>Exibir métricas de contêiner no Metrics Explorer

No Metrics Explorer, você pode exibir as métricas de utilização de nó e Pod agregadas do insights de contêiner. A tabela a seguir resume os detalhes para ajudá-lo a entender como usar os gráficos de métrica para visualizar as métricas de contêiner.

|Namespace | Métrica | Descrição |
|----------|--------|-------------|
| informações. contêiner/nós | |
| | cpuUsageMillicores | Medição agregada da utilização da CPU em todo o cluster. É um núcleo de CPU dividido em 1000 unidades (Mili = 1000). Usado para determinar o uso de núcleos em um contêiner em que muitos aplicativos podem estar usando um núcleo.|
| | cpuUsagePercentage | Utilização média de CPU agregada medida em percentual em todo o cluster.|
| | memoryRssBytes | Memória RSS do contêiner usada em bytes.|
| | memoryRssPercentage | Memória RSS do contêiner usada em percentual.|
| | memoryWorkingSetBytes | Memória do conjunto de trabalho do contêiner usada.|
| | memoryWorkingSetPercentage | Memória do conjunto de trabalho do contêiner usada em porcentagem. |
| | nodesCount | Uma contagem de nós de kubernetes.|
| percepções. Container/pods | |
| | PodCount | Uma contagem de pod de kubernetes.|

Você pode [dividir](../essentials/metrics-charts.md#apply-splitting) uma métrica para exibi-la por dimensão e visualizar como os diferentes segmentos de ti se comparam entre si. Para um nó, você pode segmentar o gráfico pela dimensão do *host* . De um pod, você pode segmentá-lo pelas seguintes dimensões:

* Controller
* Namespace kubernetes
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analisar nós, controladores e integridade do contêiner

Quando você alterna para as guias **nós**, **controladores** e **contêineres** , um painel de propriedades é exibido automaticamente no lado direito da página. Ele mostra as propriedades do item selecionado, que inclui os rótulos que você definiu para organizar objetos kubernetes. Quando um nó do Linux é selecionado, a seção **capacidade do disco local** também mostra o espaço em disco disponível e a porcentagem usada para cada disco apresentado ao nó. Selecione o **>>** link no painel para exibir ou ocultar o painel.

Como expandir os objetos na hierarquia, as atualizações de painel de propriedades com base no objeto selecionado. No painel, você também pode exibir os logs de contêiner do kubernetes (stdout/stderr), eventos e métricas de Pod selecionando o link **exibir dados dinâmicos (versão prévia)** na parte superior do painel. Para obter mais informações sobre a configuração necessária para conceder e controlar o acesso para exibir esses dados, consulte [configurar os dados dinâmicos (versão prévia)](container-insights-livedata-setup.md). Ao examinar os recursos de cluster, você pode ver esses dados do contêiner em tempo real. Para obter mais informações sobre esse recurso, consulte [como exibir logs de kubernetes, eventos e métricas de pod em tempo real](container-insights-livedata-overview.md). Para exibir os dados de log do kubernetes armazenados em seu espaço de trabalho com base em pesquisas de log predefinidas, selecione **Exibir logs de contêiner** na lista suspensa de **exibição na análise** . Para obter informações adicionais sobre este tópico, consulte [logs de pesquisa para analisar dados](container-insights-log-search.md#search-logs-to-analyze-data).

Use a opção **+ Adicionar filtro** na parte superior da página para filtrar os resultados da exibição por **serviço**, **nó**, **namespace** ou pool de **nós**. Depois de selecionar o escopo do filtro, selecione um dos valores mostrados no campo **Selecionar valor (es)** . Depois que o filtro é configurado, ele é aplicado globalmente ao exibir qualquer perspectiva do cluster AKS. A fórmula dá suporte apenas ao sinal de igual. Você pode adicionar mais filtros depois do primeiro para restringir ainda mais os resultados. Por exemplo, se você especificar um filtro por **nó**, só poderá selecionar **serviço** ou **namespace** para o segundo filtro.

A especificação de um filtro em uma guia continua sendo aplicada quando você seleciona outra. Ele é excluído depois que você seleciona o símbolo **x** ao lado do filtro especificado.

Alterne para a guia **nós** e a hierarquia de linhas segue o modelo de objeto kubernetes, que começa com um nó no cluster. Expanda o nó para exibir um ou mais pods em execução no nó. Se mais de um contêiner for agrupado a um pod, eles serão exibidos como a última linha na hierarquia. Você também pode exibir quantas cargas de trabalho não-Pod estão em execução no host se o host tiver pressão de memória ou processador.

![Exemplo da hierarquia do nó kubernetes no modo de exibição de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Os contêineres do Windows Server que executam o sistema operacional Windows Server 2019 são mostrados após todos os nós baseados em Linux na lista. Ao expandir um nó do Windows Server, você pode exibir um ou mais pods e contêineres que são executados no nó. Depois que um nó é selecionado, o painel Propriedades mostra informações de versão.

![Hierarquia de nós de exemplo com nós do Windows Server listados](./media/container-insights-analyze/nodes-view-windows.png)

Instâncias de contêiner do Azure nós virtuais que executam o sistema operacional Linux são mostradas após o último nó de cluster AKS na lista. Quando você expande um nó virtual de instâncias de contêiner, é possível exibir um pods e contêineres de instâncias de contêiner que são executados no nó. As métricas não são coletadas e relatadas para nós, somente para pods.

![Hierarquia de nós de exemplo com Instâncias de Contêiner listadas](./media/container-insights-analyze/nodes-view-aci.png)

Em um nó expandido, você pode fazer drill-down do Pod ou contêiner que é executado no nó para o controlador para exibir os dados de desempenho filtrados para esse controlador. Selecione o valor na coluna **controlador** para o nó específico.

![Captura de tela mostra a busca detalhada de nó para controlador no modo de exibição de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Selecione controladores ou contêineres na parte superior da página para examinar o status e a utilização de recursos para esses objetos. Para examinar a utilização de memória, na lista suspensa **métrica** , selecione **conjunto de trabalho** **memória RSS** ou memória. **RSS de Memória** só tem suporte para a versão do Kubernetes 1.8 e posteriores. Caso contrário, você exibirá valores para **min &nbsp; %** como *Nan &nbsp; %*, que é um valor de tipo de dados numérico que representa um valor indefinido ou inrepresentado.

![Exibição do desempenho de nós do contêiner](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Conjunto de trabalho de memória** mostra a memória residente e a memória virtual (cache) incluídas e é um total do que o aplicativo está usando. O **RSS de memória** mostra somente a memória principal (que não é nada, exceto a memória residente em outras palavras). Essa métrica mostra a capacidade real da memória disponível. Qual é a diferença entre a memória residente e a memória virtual?

- Memória residente ou memória principal, é a quantidade real de memória do computador disponível para os nós do cluster.

- A memória virtual é um espaço reservado no disco rígido (cache) usado pelo sistema operacional para trocar dados da memória em disco quando sob pressão de memória e, em seguida, busque-os de volta para a memória quando necessário.

Por padrão, os dados de desempenho são baseados nas últimas seis horas, mas você pode alterar a janela usando a opção **intervalo** de tempo no canto superior esquerdo. Você também pode filtrar os resultados dentro do intervalo de tempo selecionando **min**, **AVG**, **50 º**, **90 º**, **95 º** e **Max** no seletor de percentil.

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando você focaliza o gráfico de barras na coluna **tendência** , cada barra mostra o uso de CPU ou de memória, dependendo de qual métrica está selecionada, dentro de um período de exemplo de 15 minutos. Depois de selecionar o gráfico de tendências por meio de um teclado, use a tecla Alt + Page Up ou Alt + Page Down para percorrer cada barra individualmente. Você obterá os mesmos detalhes que faria se tiver focalizado a barra.

![Exemplo de foco de gráfico de barra de tendência](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

No próximo exemplo, para o primeiro nó na lista, *AKs-nodepool1-*, o valor para **contêineres** é 9. Esse valor é um ROLLUP do número total de contêineres implantados.

![ROLLUP de contêineres por exemplo por nó](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Essas informações podem ajudá-lo a identificar rapidamente se você tem um equilíbrio adequado de contêineres entre nós em seu cluster.

As informações apresentadas quando você exibe a guia **nós** é descrita na tabela a seguir.

| Coluna | Descrição |
|--------|-------------|
| Nome | O nome do host. |
| Status | Exibição de Kubernetes do status do nó. |
| Min &nbsp; %, AVG &nbsp; %, 50 º &nbsp; %, 90 º &nbsp; %, 95 º &nbsp; %, Max&nbsp;%  | Percentual médio de nós com base no percentil pela duração selecionada. |
| Min, AVG, 50 º, 90 º, 95 º, Max | Valor real dos nós médios com base no percentil durante o tempo de duração selecionado. O valor médio é medido do limite de CPU/memória definido para um nó. Para pods e contêineres, é o valor médio relatado pelo host. |
| Contêineres | Número de contêineres. |
| Tempo de atividade | Representa a hora desde que um nó foi iniciado ou reiniciado. |
| Controller | Somente para os contêineres e pods. Ele mostra em qual controlador ele reside. Nem todos os pods estão em um controlador, assim, alguns poderão exibir **N/D**. |
| % De tendência mín &nbsp; , média &nbsp; de%, 50 º &nbsp; %, 90 º &nbsp; %, 95 º &nbsp; %, máx&nbsp;% | Tendência de gráfico de barras representa o percentual médio de métrica de percentil do controlador. |

Você pode notar uma carga de trabalho depois de expandir um nó chamado **outro processo**. Ele representa processos não-contêineres que são executados no seu nó e inclui:

* Processos de Kubernetes autogerenciados ou gerenciados que não estão em contêineres

* Processos de tempo de execução do contêiner

* Kubelet

* Processos do sistema em execução no seu nó

* Outras cargas de trabalho que não são de Kubernetes em execução no hardware do nó ou em VM

Ele é calculado por: *uso total do*  -  *uso de CAdvisor do processo em contêineres*.

No seletor, selecione **Controladores**.

![Selecionar exibição de controladores](./media/container-insights-analyze/containers-controllers-tab.png)

Aqui você pode exibir a integridade do desempenho de seus controladores e das instâncias de contêiner controladores de nó virtual ou pods de nó virtual não conectados a um controlador.

![\<Exibição de desempenho de controladores de> de nome](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia de linhas começa com um controlador. Ao expandir um controlador, você exibe um ou mais pods. Expanda um pod e a última linha mostrará o contêiner agrupado para o pod. A partir de um controlador expandido, você pode fazer uma busca detalhada no nó em que ele está sendo executado para exibir os dados de desempenho filtrados para esse nó. As instâncias de contêiner pods não conectadas a um controlador são listadas por último na lista.

![Hierarquia de Controladores de exemplo com pods de Instâncias de Contêiner listados](./media/container-insights-analyze/controllers-view-aci.png)

Selecione o valor na coluna **nó** para o controlador específico.

![Exemplo de busca detalhada do controlador para o nó no modo de exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações exibidas quando você exibe controladores são descritas na tabela a seguir.

| Coluna | Descrição |
|--------|-------------|
| Nome | O nome do controlador.|
| Status | O status de rollup dos contêineres após a conclusão da execução com status, como *OK*, *encerrado*, *com falha*, *parado* ou em *pausa*. Se o contêiner estiver em execução, mas o status não tiver sido exibido corretamente ou não tiver sido selecionado pelo agente e não tiver respondido por mais de 30 minutos, o status será *desconhecido*. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Min &nbsp; %, AVG &nbsp; %, 50 º &nbsp; %, 90 º &nbsp; %, 95 º &nbsp; %, Max&nbsp;%| Média de rollup do percentual médio de cada entidade para a métrica e o percentil selecionados. |
| Min, AVG, 50 º, 90 º, 95 º, Max  | Rollup da média de milinúcleo de CPU ou desempenho da memória do contêiner para o percentil selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Contêineres | Número total de contêineres para o controlador ou pod. |
| Reinícios | Rollup da contagem de reinicialização dos contêineres. |
| Tempo de atividade | Representa o tempo desde o início de um contêiner. |
| Nó | Somente para os contêineres e pods. Ele mostra em qual controlador ele reside. |
| % De tendência mín &nbsp; , média &nbsp; de%, 50 º &nbsp; %, 90 º &nbsp; %, 95 º &nbsp; %, máx&nbsp;% | A tendência de gráfico de barras representa a métrica percentil do controlador. |

Os ícones no campo status indicam o status online dos contêineres.

| ícone | Status |
|--------|-------------|
| ![Ícone de status de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status aguardando ou pausado](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/container-insights-analyze/containers-grey-icon.png) | Última reportada em execução, mas não respondida por mais de 30 minutos|
| ![Ícone de status de êxito](./media/container-insights-analyze/containers-green-icon.png) | Parou com sucesso ou houve falha ao parar|

O ícone de status mostra uma contagem com base no que o pod fornece. Ele mostra os dois piores estados e, quando você passa o mouse sobre o status, mostra um status de rollup de todos os pods no contêiner. Se não houver um estado pronto, o valor de status mostrará **(0)**.

No seletor, selecione **Contêineres**.

![Selecionar exibição de contêineres](./media/container-insights-analyze/containers-containers-tab.png)

Aqui, você pode exibir a integridade de desempenho de seus contêineres do Kubernetes do Azure e das Instâncias de Contêiner do Azure.

![\<Exibição de desempenho de contêineres de nome>](./media/container-insights-analyze/containers-containers-view.png)

Em um contêiner, você pode fazer drill down até um pod ou nó para exibir dados de desempenho filtrados para esse objeto. Selecione o valor na coluna **Pod** ou **nó** para o contêiner específico.

![Exemplo de busca detalhada de nó para contêineres no modo de exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações exibidas quando você exibe contêineres são descritas na tabela a seguir.

| Coluna | Descrição |
|--------|-------------|
| Nome | O nome do controlador.|
| Status | Status dos contêineres, se houver. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Min &nbsp; %, AVG &nbsp; %, 50 º &nbsp; %, 90 º &nbsp; %, 95 º &nbsp; %, Max&nbsp;% | O rollup do percentual médio de cada entidade para a métrica e o percentil selecionados. |
| Min, AVG, 50 º, 90 º, 95 º, Max | O rollup da média do desempenho de memória ou do milinúcleo da CPU do contêiner para o percentual selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Pod | Contêiner no qual reside o pod.|
| Nó |  Nó em que reside o contêiner. |
| Reinícios | Representa o tempo desde o início de um contêiner. |
| Tempo de atividade | Representa a hora desde que um contêiner foi iniciado ou reiniciado. |
| % De tendência mín &nbsp; , média &nbsp; de%, 50 º &nbsp; %, 90 º &nbsp; %, 95 º &nbsp; %, máx&nbsp;% | A tendência de gráfico de barras representa o percentual métrico médio do contêiner. |

Os ícones no campo status indicam os status online de pods, conforme descrito na tabela a seguir.

| ícone | Status |
|--------|-------------|
| ![Ícone de status de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status aguardando ou pausado](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/container-insights-analyze/containers-grey-icon.png) | Relatado pela última vez como em execução, mas sem responder por mais de 30 minutos|
| ![Ícone de status encerrado](./media/container-insights-analyze/containers-terminated-icon.png) | Parou com sucesso ou houve falha ao parar|
| ![Ícone de status com falha](./media/container-insights-analyze/containers-failed-icon.png) | Estado com falha |

## <a name="monitor-and-visualize-network-configurations"></a>Monitorar e visualizar as configurações de rede
O Gerenciador de políticas de rede do Azure inclui métricas Prometheus informativas que permitem monitorar e entender melhor suas configurações de rede. Ele fornece visualizações internas nos laboratórios portal do Azure ou Grafana. Para obter detalhes, consulte [monitorar e visualizar as configurações de rede com o Azure NPM](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm).


## <a name="workbooks"></a>Pastas de trabalho

As pastas de trabalho combinam texto, consultas de log, métricas e parâmetros em relatórios interativos avançados que permitem analisar o desempenho do cluster. Confira [pastas de trabalho em informações de contêiner](../insights/container-insights-reports.md) para obter uma descrição das pastas de trabalho disponíveis para o contêiner insights.


## <a name="next-steps"></a>Próximas etapas

- Examine [criar alertas de desempenho com o contêiner insights](./container-insights-log-alerts.md) para saber como criar alertas para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps.

- Visualize [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar seus clusters.

- Exiba [monitorar a integridade do cluster](./container-insights-overview.md) para saber mais sobre como exibir o status de integridade do cluster kubernetes.