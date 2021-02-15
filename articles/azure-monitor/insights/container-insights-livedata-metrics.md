---
title: Exibir métricas em tempo real com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve a exibição em tempo real de métricas sem usar o kubectl com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 81d7210778fd6b5d75fb4b4fa8e066d2e015174f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85338025"
---
# <a name="how-to-view-metrics-in-real-time"></a>Como exibir métricas em tempo real

Azure Monitor para contêineres de dados dinâmicos (versão prévia) permite que você visualize métricas sobre o estado de nó e pod em um cluster em tempo real. Ele emula o acesso direto aos `kubectl top nodes` comandos, `kubectl get pods –all-namespaces` e `kubectl get nodes` para chamar, analisar e visualizar os dados em gráficos de desempenho que estão incluídos com essa percepção.

Este artigo fornece uma visão geral detalhada e ajuda você a entender como usar esse recurso.

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não têm suporte com esse recurso. Esse recurso depende diretamente do acesso à API kubernetes por meio de um servidor proxy do seu navegador. Habilitar a segurança de rede para bloquear a API do kubernetes desse proxy bloqueará esse tráfego.

Para obter ajuda com a configuração ou solução de problemas do recurso de dados dinâmicos (versão prévia), examine nosso [Guia de instalação](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Como funciona

O recurso de dados dinâmicos (versão prévia) acessa diretamente a API kubernetes e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Esse recurso executa uma operação de sondagem em relação aos pontos de extremidade de métricas (incluindo `/api/v1/nodes` , `/apis/metrics.k8s.io/v1beta1/nodes` e `/api/v1/pods` ), que é a cada cinco segundos por padrão. Esses dados são armazenados em cache no navegador e são mostrados nos quatro gráficos de desempenho incluídos no Azure Monitor para contêineres na guia **cluster** selecionando **Go Live (visualização)**. Cada sondagem subsequente é inserida em uma janela de visualização de cinco minutos sem interrupção.

![Opção Go Live na exibição do cluster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

O intervalo de sondagem é configurado na lista suspensa **definir intervalo** , permitindo que você defina sondagem para novos dados a cada 1, 5, 15 e 30 segundos.

![Ir para intervalo de sondagem suspensa de ativação](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>É recomendável definir o intervalo de sondagem para um segundo enquanto soluciona um problema por um curto período de tempo. Essas solicitações podem afetar a disponibilidade e a limitação da API kubernetes no cluster. Depois, reconfigure para um intervalo de sondagem mais longo.

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante a operação deste recurso. Todas as informações capturadas durante essa sessão são excluídas imediatamente quando você fecha o navegador ou navega para fora do recurso. Os dados permanecem presentes apenas para visualização dentro da janela de cinco minutos; todas as métricas com mais de cinco minutos também são excluídas permanentemente.

Esses gráficos não podem ser fixados no último painel do Azure exibido no modo dinâmico.

## <a name="metrics-captured"></a>Métricas capturadas

### <a name="node-cpu-utilization---node-memory-utilization-"></a>% De utilização de CPU do nó%/utilização de memória do nó%

Esses dois gráficos de desempenho são mapeados para um equivalente de invocar `kubectl top nodes` e capturar os resultados das colunas **CPU%** e **% de memória** para o respectivo gráfico.

![Resultados de exemplo de nós principais do Kubectl](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Gráfico de porcentagem de utilização da CPU dos nós](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Gráfico de porcentagem de utilização de memória do nó](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Os cálculos de percentil funcionarão em clusters maiores para ajudar a identificar nós de exceção no cluster. Por exemplo, para entender se os nós são subutilizados para fins de redução. Utilizando a agregação **min** , você pode ver quais nós têm baixa utilização no cluster. Para investigar melhor, selecione a guia **nós** e Classifique a grade por utilização de CPU ou memória.

Isso também ajuda a entender quais nós estão sendo enviados por push para seus limites e se a expansão pode ser necessária. Utilizar as agregações **Max** e **P95** pode ajudá-lo a ver se há nós no cluster com alta utilização de recursos. Para uma investigação mais aprofundada, alterne novamente para a guia **nós** .

### <a name="node-count"></a>Contagem de nós

Esse gráfico de desempenho é mapeado para um equivalente de invocar `kubectl get nodes` e mapear a coluna **status** para um gráfico agrupado por tipos de status.

![Resultados de exemplo do Kubectl Get Nodes](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Gráfico de contagem de nós](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Os nós são relatados em um estado **pronto** ou **não pronto** . Eles são contados (e uma contagem total é criada) e os resultados dessas duas agregações são gráficos.
Por exemplo, para entender se os nós estão se enquadrando em Estados com falha. Utilizando a agregação **não pronta** , você pode ver rapidamente o número de nós no cluster atualmente no estado **não pronto** .

### <a name="active-pod-count"></a>Contagem de Pod ativo

Esse gráfico de desempenho é mapeado para um equivalente de invocar `kubectl get pods –all-namespaces` e mapear a coluna de **status** do gráfico agrupado por tipos de status.

![Resultados de exemplo do Kubectl Get pods](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Gráfico de contagem de pod dos nós](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Os nomes de status como interpretados pelo `kubectl` podem não corresponder exatamente ao gráfico.

## <a name="next-steps"></a>Próximas etapas

Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para criar alertas, visualizações ou executar análise adicional de seus clusters.
