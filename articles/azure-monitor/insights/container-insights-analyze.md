---
title: Monitorar o desempenho de cluster do AKS com o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode exibir e analisar dados de desempenho e de log com o Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: bbfc8cc61571de8b76ef1f7f0216501ef6d2cdee
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377478"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Compreender o desempenho de cluster do AKS com o Azure Monitor para contêineres 
Com o Azure Monitor para contêineres, você pode usar os gráficos de desempenho e o status de integridade para monitorar a carga de trabalho dos clusters do AKS (Serviço de Kubernetes do Azure) por meio de duas perspectivas: diretamente em um cluster do AKS ou todos os clusters do AKS em uma assinatura no Azure Monitor. Também é possível exibir ACI (Instâncias de Contêiner do Azure) ao monitorar um cluster específico do AKS.

Este artigo ajudará você a entender a experiência com as duas perspectivas e como elas ajudam a avaliar, investigar e resolver rapidamente os problemas detectados.

Para obter informações sobre como habilitar o Azure Monitor para contêineres, consulte [Integrar o Azure Monitor para contêineres](container-insights-onboard.md).

Azure Monitor fornece uma exibição de vários clusters mostrando o status de integridade de todos os clusters AKS monitorados que executam o Linux e o Windows Server 2019 implantados em grupos de recursos em suas assinaturas.  Ele mostra clusters do AKS descobertos que não são monitorados pela solução. Imediatamente você saberá a integridade do cluster e, então, poderá fazer drill down até a página de desempenho do nó e do controlador ou navegar para ver gráficos de desempenho do cluster.  Para clusters do AKS descobertos e identificados como não monitorados, é possível habilitar o monitoramento para esse cluster a qualquer momento.  

As principais diferenças de monitoramento de um cluster do Windows Server com Azure Monitor para contêineres em comparação com um cluster do Linux são as seguintes:

- A métrica de RSS de memória não está disponível para o nó e contêineres do Windows.
- As informações de capacidade de armazenamento em disco não estão disponíveis para nós do Windows.
- O suporte a logs dinâmicos está disponível com exceção dos logs de contêiner do Windows.
- Somente ambientes de Pod são monitorados, não ambientes de Docker.
- Com a versão de visualização, há suporte para um máximo de 30 contêineres do Windows Server. Essa limitação não se aplica a contêineres do Linux.  

## <a name="sign-in-to-the-azure-portal"></a>Entrar no portal do Azure

Entre no [Portal do Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Exibição de vários clusters do Azure Monitor

Para exibir o status de integridade de todos os clusters do AKS implantados, selecione **Monitor** no painel esquerdo no portal do Azure.  Na seção **Insights**, selecione **Contêineres**.  

![Exemplo de painel com vários clusters no Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Na guia **Clusters monitorados**, você pode obter as seguintes informações:

1. Quantos clusters estão com estado crítico ou não íntegro, e quantos estão íntegros ou não estão informando um estado (conhecidos como estado desconhecido)?
2. Todos as minhas implantações do [(mecanismo AKS) Mecanismo de Kubernetes do Azure](https://github.com/Azure/aks-engine) estão íntegras?
3. Quantos nós, usuários e pods de sistema são implantados por cluster?
4. Quanto espaço em disco está disponível e há um problema de capacidade?

Os status de integridade incluídos são: 

* **Integridade** - nenhum problema detectado para a VM e está funcionando conforme necessário. 
* **Crítico** – foram detectados um ou mais problemas críticos, que precisam ser resolvidos para restaurar o estado operacional normal esperado.
* **Aviso** – foram detectados um ou mais problemas, que precisam ser resolvidos para que a condição de integridade não se torne crítica.
* **Desconhecido** – se o serviço não conseguir se conectar ao nó ou ao pod, o status mudará para um estado desconhecido.
* **Não encontrado** – o workspace, o grupo de recursos ou a assinatura que contém o workspace dessa solução foi excluída.
* **Não autorizado** – o usuário não tem as permissões necessárias para ler os dados no workspace.
* **Erro** – ocorreu um erro ao tentar ler dados do workspace.
* Configurado incorretamente **-Azure monitor** para contêineres não foi configurado adequadamente no espaço de trabalho especificado.
* **Nenhum dado** – não houve relato de dados ao workspace nos últimos 30 minutos.

O estado de integridade calcula o status geral do cluster como o *pior dos* três Estados com uma exceção – se qualquer um dos três Estados for *desconhecido*, o estado geral do cluster mostrará **desconhecido**.  

A tabela a seguir fornece um detalhamento do cálculo que controla os estados de integridade de um cluster monitorado na exibição de vários clusters.

| |Status |Disponibilidade |  
|-------|-------|-----------------|  
|**Pod de usuários**| | |  
| |Adequado |100% |  
| |Aviso |90 – 99% |  
| |Crítica |<90% |  
| |Unknown |Se não tiver sido relatado nos últimos 30 minutos |  
|**Pod de sistemas**| | |  
| |Adequado |100% |
| |Aviso |N/D |
| |Crítica |<100% |
| |Unknown |Se não tiver sido relatado nos últimos 30 minutos |
|**Node** | | |
| |Adequado |>85% |
| |Aviso |60 – 84% |
| |Crítica |<60% |
| |Unknown |Se não tiver sido relatado nos últimos 30 minutos |

Na lista de clusters, você pode fazer drill down até a página **Cluster** clicando no nome do cluster, até a página de desempenho dos **Nós** clicando no rollup de nós na coluna **Nós** do cluster específico ou pode fazer drill down até a página de desempenho de **Controladores** clicando no rollup da coluna **Pods de usuários** ou **Pods de sistema**.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Exibir desempenho diretamente de um cluster do AKS

O acesso ao Azure Monitor para contêineres está disponível diretamente de um cluster do AKS selecionando **Insights** no painel esquerdo. A exibição das informações sobre o cluster do AKS é organizada em quartos perspectivas:

- HDInsight
- Nós 
- Controladores  
- Contêineres

A página padrão aberta quando você clica em **Insights** é **Cluster**, que inclui quatro gráficos de desempenho de linha que exibem as principais métricas de desempenho de seu cluster. 

![Gráficos de desempenho de exemplo na guia de Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

O gráfico de desempenho exibe quatro métricas de desempenho:

- **Utilização da CPU do Nó&nbsp;%** : Uma perspectiva agregada da utilização da CPU para todo o cluster. Você pode filtrar os resultados para o intervalo de tempo selecionando **Méd.** , **Mín.** , **Máx.** , **50º**, **90º** e **95º** no seletor de percentis acima do gráfico, sejam individualmente ou combinados. 
- **Utilização de memória do nó&nbsp;%** : Uma perspectiva agregada da utilização de memória para todo o cluster. Você pode filtrar os resultados para o intervalo de tempo selecionando **Méd.** , **Mín.** , **Máx.** , **50º**, **90º** e **95º** no seletor de percentis acima do gráfico, sejam individualmente ou combinados. 
- **Contagem de nós**: Uma contagem de nós e o status do Kubernetes. Os status de nós do cluster representado são *Todos*, *Pronto*, e *Não pronto*, e podem ser filtrados individualmente ou de modo combinado no seletor acima do gráfico. 
- **Contagem de pod atividades**: Uma contagem de pod e o status do Kubernetes. Os status dos pods representados são *Todos*, *Pendente*, *Em execução* e *Desconhecido* e podem ser filtrados individualmente ou de modo combinado no seletor acima do gráfico. 

Você pode usar as teclas de seta para a esquerda/direita para percorrer cada ponto de dados no gráfico e as teclas de seta para cima/para baixo para percorrer as linhas de percentil. Clicar no ícone de pino no canto superior direito de qualquer um dos gráficos fixará o gráfico selecionado no último painel do Azure que você exibiu pela última vez. No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel irá redirecioná-lo para Azure Monitor para contêineres e carregar o escopo e a exibição corretos.

O Azure Monitor para contêineres também dá suporte a Azure Monitor [métricas Explorer](../platform/metrics-getting-started.md), onde você pode criar seus próprios gráficos de plotagem, correlacionar e investigar tendências e fixar em painéis. No Metrics Explorer, você também pode usar os critérios definidos para visualizar suas métricas como a base de uma regra de [alerta baseada em métrica](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Exibir métricas de contêiner no Metrics Explorer

No Metrics Explorer, você pode exibir as métricas de utilização de nó e Pod agregadas de Azure Monitor para contêineres. A tabela a seguir resume os detalhes para ajudá-lo a entender como usar os gráficos de métrica para visualizar as métricas de contêiner.

|Namespace | Métrica |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| percepções. Container/pods | |
| | PodCount |

Você pode aplicar a [divisão](../platform/metrics-charts.md#apply-splitting-to-a-chart) de uma métrica para exibi-la por dimensão e visualizar como os diferentes segmentos de ti se comparam entre si. Para um nó, você pode segmentar o gráfico pela dimensão do *host* e, a partir de um pod, você pode segmentá-lo pelas seguintes dimensões:

* Controlador
* Namespace kubernetes
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analisar nós, controladores e integridade do contêiner

Quando você alterna para os **Nós**, **Controladores**, e guia **Contêineres**, exibido automaticamente no lado direito da página está o painel de propriedade. Ele mostra as propriedades do item selecionado, incluindo os rótulos que você define para organizar objetos kubernetes. Quando um nó do Linux é selecionado, ele também é mostrado na seção espaço em disco disponível no **disco local** e percentual usado para cada disco apresentado ao nó. Clique no **>>** link no painel de visualizar\ocultar o painel. 

![Exemplo do painel de propriedades de perspectivas de Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Como expandir os objetos na hierarquia, as atualizações de painel de propriedades com base no objeto selecionado. No painel também, você também pode exibir eventos de Kubernetes com pesquisas de log predefinidas, clicando no link **Exibir logs de eventos de Kubernetes** na parte superior do painel. Para obter mais informações sobre como exibir dados de log do Kubernetes, confira [Pesquisar logs para analisar dados](container-insights-log-search.md). Ao examinar os recursos de cluster, você pode ver logs de contêiner e eventos em tempo real. Para obter mais informações sobre esse recurso e a configuração necessária para conceder e controlar o acesso, consulte [como exibir logs em tempo real com o Azure monitor para contêineres](container-insights-live-logs.md). 

Use a opção **+ Adicionar filtro** na parte superior da página para filtrar os resultados da exibição por **serviço**, **nó**, **namespace**ou pool de **nós** e, depois de selecionar o escopo do filtro, selecione um dos valores mostrados no **Selecione o campo valor (es)** .  Após ser configurado, o filtro é aplicado globalmente ao exibir qualquer perspectiva do cluster do AKS.  A fórmula dá suporte apenas ao sinal de igual.  Você pode adicionar mais filtros depois do primeiro para restringir ainda mais os resultados.  Por exemplo, se você tiver especificado um filtro por **Nó**, o segundo filtro permitirá selecionar apenas **Serviço** ou **Namespace**.  

![Exemplo de uso do filtro para restringir os resultados](./media/container-insights-analyze/add-filter-option-01.png)

Um filtro especificado em uma guia continua sendo aplicado quando você seleciona outro filtro e é excluído após você clicar no símbolo **x** ao lado dele.   

Alterne para a guia **Nós**, a hierarquia de linhas seguirá o modelo de objeto do Kubernetes começando com um nó no cluster. Expanda o nó e poderá exibir um ou mais pods em execução no nó. Se mais de um contêiner for agrupado em um pod, eles serão exibidos como a última linha na hierarquia. Você também poderá exibir quantas cargas de trabalho não relacionadas a pod estão em execução no host se o host tiver pressão de memória ou processador.

![Exemplo de hierarquia de nós do Kubernetes no modo de exibição de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Os contêineres do Windows Server que executam o sistema operacional Windows Server 2019 são mostrados após todos os nós baseados em Linux na lista. Ao expandir um nó do Windows Server, você pode exibir um ou mais pods e contêineres em execução no nó. Quando um nó é selecionado, o painel Propriedades mostra informações de versão, excluindo as informações do agente, pois os nós do Windows Server não têm um agente instalado.  

![Hierarquia de nós de exemplo com nós do Windows Server listados](./media/container-insights-analyze/nodes-view-windows.png) 

Os Nós Virtuais de Instâncias de Contêiner do Azure executando o sistema operacional Linux são mostrados após o último nó de cluster do AKS na lista.  Ao expandir um Nó Virtual de ACI, você pode exibir um ou mais pods e contêineres de ACI em execução no nó.  As métricas não são coletadas e relatadas para nós, somente para os pods.

![Hierarquia de nós de exemplo com Instâncias de Contêiner listadas](./media/container-insights-analyze/nodes-view-aci.png)

Em um nó expandido, é possível fazer drill down do pod ou contêiner em execução no nó até o controlador para exibir dados de desempenho filtrados para esse controlador. Clique no valor sob a coluna **Controlador** para o nó específico.   
![Exemplo de drill down do nó até o controlador na exibição de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Você pode selecionar controladores ou contêineres na parte superior da página e examinar o status e a utilização de recursos para esses objetos.  Se, em vez disso, você quiser examinar a utilização de memória, na lista suspensa **Métrica**, selecione **RSS de Memória** ou **Conjunto de trabalho de memória**. **RSS de Memória** só tem suporte para a versão do Kubernetes 1.8 e posteriores. Caso contrário, você exibirá valores para **Mín.&nbsp;%** como *NaN&nbsp;%* , que é um valor de tipo de dados numérico que representa um valor indefinido ou não representável.

Conjunto de trabalho de memória mostra a memória residente e a memória virtual (cache) incluídas e é um total do que o aplicativo está usando. A memória RSS mostra apenas a memória principal, que é a memória residente. Essa métrica está mostrando a capacidade real da memória disponível.

![Exibição do desempenho de nós do contêiner](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Por padrão, os dados de Desempenho são baseados nas últimas seis horas, mas você pode alterar a janela usando a opção **Intervalo de Tempo** na parte superior esquerda. Você também pode filtrar os resultados dentro do intervalo de tempo selecionando **Méd.** , **Mín.** , **Máx.** , **50º**, **90º** e **95º** no seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando você passa o mouse sobre o gráfico de barras na coluna **Tendência**, cada barra mostra o uso de CPU ou de memória, dependendo de qual métrica está selecionada, em de um período de amostra de 15 minutos. Depois de selecionar o gráfico de tendência usando um teclado, você pode usar as teclas Alt+PageUp ou Alt+PageDown para percorrer cada barra individualmente e obter os mesmos detalhes que obteria passando o mouse por elas.

![Exemplo de foco de gráfico de barra de tendência](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

No próximo exemplo, observe que, para o primeiro da lista, nó *aks-nodepool1-* , o valor de **Contêineres** é 9, que é o valor acumulado do número total de contêineres implantados.

![Exemplo de acumulação de contêineres por nó](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Ele pode ajudá-lo a identificar rapidamente se você tem um balanço adequado de contêineres entre nós no seu cluster. 

As informações apresentadas quando você exibe Nós são descritas na tabela a seguir:

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do host. |
| Status | Exibição de Kubernetes do status do nó. |
| Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | Percentual médio de nós com base no percentil pela duração selecionada. |
| Méd., Mín., Máx., 50º, 90º | Valor real de nós médio com base no percentil durante a duração de tempo selecionada. O valor médio é medido usando o limite de memória/CPU definido para um nó; para contêineres e pods, é o valor médio relatado pelo host. |
| Contêineres | Número de contêineres. |
| Tempo de atividade | Representa a hora desde que um nó foi iniciado ou reiniciado. |
| Controladores | Somente para os contêineres e pods. Mostra em que controlador ele está residindo. Nem todos os pods estão em um controlador, assim, alguns poderão exibir **N/D**. | 
| Tendência Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | Tendência de gráfico de barras representa o percentual médio de métrica de percentil do controlador. |

No seletor, selecione **Controladores**.

![Selecionar modo de exibição de controladores](./media/container-insights-analyze/containers-controllers-tab.png)

Aqui você pode exibir a integridade do desempenho de seus controladores e controladores de Nó Virtual do ACI ou de pods de Nó Virtual não conectados a um controlador.

![\<Exibição de desempenho de controladores de > de nome](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia de linhas começa com um controlador e, ao expandir um controlador, você visualizará um ou mais pods.  Expanda o pod e a última linha exibe o contêiner agrupado no pod. Em um controlador expandido, é possível fazer drill down até o nó que em que ele está em execução para exibir dados de desempenho filtrados para esse nó. Os pods do ACI não conectados a um controlador são listados por último na lista.

![Hierarquia de Controladores de exemplo com pods de Instâncias de Contêiner listados](./media/container-insights-analyze/controllers-view-aci.png)

Clique no valor sob a coluna **Nó** para o controlador específico.   

![Exemplo de drill down do nó até o controlador na exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações que são exibidas quando você exibe controladores são descritas na tabela a seguir:

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador.|
| Status | O status de rollup dos contêineres quando a execução é concluída com status, como *OK*, *Encerrado*, *Com falha* *Parado* ou *Em pausa*. Se o contêiner estiver em execução, mas o status não tiver sido devidamente exibido ou não tiver sido selecionado pelo agente e não tiver respondido por mais de 30 minutos, o status será *Desconhecido*. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | Média acumulada do percentual médio de cada entidade para a métrica selecionada e o percentil. |
| Méd., Mín., Máx., 50º, 90º  | Acumulação da média de millicore da CPU ou desempenho da memória do contêiner para o percentual selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Contêineres | Número total de contêineres para o controlador ou pod. |
| Reinícios | Acumulação da contagem de reinicialização dos contêineres. |
| Tempo de Atividade | Representa o tempo desde o início de um contêiner. |
| Nó | Somente para os contêineres e pods. Mostra quais são os controladores residentes. | 
| Tendência Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;%| A tendência de gráfico de barras representa a métrica percentil do controlador. |

Os ícones no campo status indicam o status online dos contêineres:
 
| ícone | Status | 
|--------|-------------|
| ![Ícone de status de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status de espera ou em pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/container-insights-analyze/containers-grey-icon.png) | Última execução relatada, mas sem responder por mais de 30 minutos|
| ![Ícone de status de êxito](./media/container-insights-analyze/containers-green-icon.png) | Parou com sucesso ou houve falha ao parar|

O ícone de status mostra uma contagem com base no que o pod fornece. Ele mostra os dois piores estados e, quando você passa o mouse sobre o status, mostra um status de rollup de todos os pods no contêiner. Se não houver um estado pronto, o valor de status mostrará **(0)** . 

No seletor, selecione **Contêineres**.

![Selecionar modo de exibição de contêineres](./media/container-insights-analyze/containers-containers-tab.png)

Aqui, você pode exibir a integridade de desempenho de seus contêineres do Kubernetes do Azure e das Instâncias de Contêiner do Azure.  

![\<Exibição de desempenho de controladores de > de nome](./media/container-insights-analyze/containers-containers-view.png)

Em um contêiner, você pode fazer drill down até um pod ou nó para exibir dados de desempenho filtrados para esse objeto. Clique no valor sob a coluna **Pod** ou **Nó** para o contêiner específico.   

![Exemplo de drill down do nó até o controlador na exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações que são exibidas quando você exibe contêineres são descritas na tabela a seguir:

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador.|
| Status | Status dos contêineres, se houver. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | O rollup do percentual médio de cada entidade para a métrica e o percentil selecionados. |
| Méd., Mín., Máx., 50º, 90º  | O rollup da média do desempenho de memória ou do milinúcleo da CPU do contêiner para o percentual selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Pod | Contêiner no qual reside o pod.| 
| Nó |  Nó em que reside o contêiner. | 
| Reinícios | Representa o tempo desde o início de um contêiner. |
| Tempo de atividade | Representa a hora desde que um contêiner foi iniciado ou reiniciado. |
| Tendência Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | A tendência de gráfico de barras representa o percentual métrico médio do contêiner. |

Os ícones no campo status indicam os status online de pods, conforme descrito na tabela a seguir:
 
| ícone | Status |  
|--------|-------------|  
| ![Ícone de status de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|  
| ![Ícone de status de espera ou em pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|  
| ![Ícone do status de execução informado pela última vez](./media/container-insights-analyze/containers-grey-icon.png) | Relatado pela última vez como em execução, mas sem responder por mais de 30 minutos|  
| ![Ícone de status encerrado](./media/container-insights-analyze/containers-terminated-icon.png) | Parou com sucesso ou houve falha ao parar|  
| ![Ícone de status com falha](./media/container-insights-analyze/containers-failed-icon.png) | Estado com falha |  

## <a name="workbooks"></a>Pastas de Trabalho

As pastas de trabalho combinam texto, [consultas de log](../log-query/query-language.md), [métricas](../platform/data-platform-metrics.md)e parâmetros em relatórios interativos sofisticados. As Pastas de Trabalho são editáveis por qualquer membro da equipe com acesso aos mesmos recursos do Azure.

Azure Monitor para contêineres inclui quatro pastas de trabalho para você começar:

- **Capacidade do disco**: Apresenta gráficos de uso de disco interativo para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - % De uso do disco para todos os discos
    - Espaço livre em disco para todos os discos
    - Uma grade que mostra para cada disco de nós, seu% espaço usado, tendência de% espaço usado, espaço livre em disco (GiB) e tendência de espaço livre em disco (GiB). Quando uma linha é selecionada na tabela,% espaço usado e espaço livre em disco (GiB) é mostrado abaixo 

- **E/s de disco**: Apresenta gráficos de utilização de disco interativo para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - E/s de disco resumida em todos os discos por meio de bytes de leitura/s, bytes de gravação/s e tendências de leitura e gravação de bytes/s 
    - Oito gráficos de desempenho mostrando os principais indicadores de desempenho para ajudar a medir e identificar afunilamentos de e/s de disco.

- **Kubelet**: Inclui duas grades que mostram as estatísticas operacionais do nó de chave:

    - Visão geral por grade de nós resume a operação total, os erros totais e as operações bem-sucedidas por porcentagem e tendência para cada nó.
    - Visão geral por tipo de operação resume para cada operação a operação total, os erros totais e as operações bem-sucedidas por porcentagem e tendência.

- **Rede**: Apresenta gráficos de utilização de rede interativa para cada adaptador de rede de nós e uma grade apresentando os principais indicadores de desempenho para ajudar a medir o desempenho dos adaptadores de rede.  

Você acessa essas pastas de trabalho selecionando cada uma na lista suspensa **exibir pastas de trabalho** .  

![Exibir lista suspensa de pastas de trabalho](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Próximas etapas

- Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps. 

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.
