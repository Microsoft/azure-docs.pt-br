---
title: Exibir dados dinâmicos com informações de contêiner | Microsoft Docs
description: Este artigo descreve a exibição em tempo real de logs de kubernetes, eventos e métricas de Pod sem usar o kubectl em informações de contêiner.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: references_regions
ms.openlocfilehash: 5277f5051e291e9058255d8920ac0be950389704
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203191"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Como exibir logs de kubernetes, eventos e métricas de pod em tempo real

O contêiner insights inclui o recurso de dados dinâmicos, que é um recurso de diagnóstico avançado que permite o acesso direto aos seus logs de contêiner do AKS (serviço de kubernetes do Azure) (stdout/stderr), eventos e métricas Pod. Ele expõe acesso direto a `kubectl logs -c` , `kubectl get` eventos e `kubectl top pods` . Um painel de console mostra os logs, eventos e métricas gerados pelo mecanismo de contêiner para auxiliar ainda mais na solução de problemas em tempo real.

Este artigo fornece uma visão geral detalhada e ajuda você a entender como usar esse recurso.

Para obter ajuda sobre como configurar ou solucionar problemas do recurso de dados dinâmicos, examine nosso [Guia de instalação](container-insights-livedata-setup.md). Esse recurso acessa diretamente a API do kubernetes e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

## <a name="view-aks-resource-live-logs"></a>Exibir logs dinâmicos de recursos do AKS
Use o procedimento a seguir para exibir os logs ao vivo de pods, implantações e conjuntos de réplicas com ou sem informações de contêiner da exibição de recursos AKS.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. Selecione **cargas de trabalho** na seção **recursos de kubernetes** do menu.

3. Selecione um pod, implantação e conjunto de réplicas na guia respectivo.

4. Selecione **logs dinâmicos** no menu do recurso.

5. Selecione um pod para iniciar a coleta de dados dinâmicos.

    [![Logs ao vivo da implantação](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>Exibir logs

Você pode exibir dados de log em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós**, **controladores** e **contêineres** . Para exibir dados de log, execute as etapas a seguir.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. No painel do cluster AKS, em **monitoramento** no lado esquerdo, escolha **insights**.

3. Selecione a guia **nós**, **controladores** ou **contêineres** .

4. Selecione um objeto na grade de desempenho e, no painel Propriedades, localizado no lado direito, selecione a opção **exibir dados dinâmicos** . Se o cluster AKS estiver configurado com logon único usando o Azure AD, você será solicitado a autenticar no primeiro uso durante a sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.

    >[!NOTE]
    >Ao exibir os dados do seu espaço de trabalho do Log Analytics selecionando a opção **Exibir no Analytics** no painel Propriedades, os resultados da pesquisa de log mostrarão potencialmente **nós**, **conjuntos de daemon**, **conjuntos de réplicas**, **trabalhos**, **trabalhos cron**, **pods** e **contêineres** que talvez não existam mais. A tentativa de pesquisar logs de um contêiner que não está disponível `kubectl` também falhará aqui. Examine o recurso [Exibir no Analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre como exibir logs históricos, eventos e métricas.

Após a autenticação bem-sucedida, o painel do console de dados dinâmicos será exibido abaixo da grade de dados de desempenho, na qual você pode exibir dados de log em um fluxo contínuo. Se o indicador de status de busca mostrar uma marca de seleção verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começam a transmitir para o console.

![Opção Propriedades do nó exibição de dados do painel](./media/container-insights-livedata-overview/node-properties-pane.png)

O título do painel mostra o nome do pod com o qual o contêiner é agrupado.

## <a name="view-events"></a>Exibir eventos

Você pode exibir dados de eventos em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós**, **controladores**, **contêineres** e **implantações** quando um contêiner, Pod, nó, réplicaset, daemonset, trabalho, CronJob ou implantação é selecionado. Para exibir eventos, execute as etapas a seguir.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. No painel do cluster AKS, em **monitoramento** no lado esquerdo, escolha **insights**.

3. Selecione a guia **nós**, **controladores**, **contêineres** ou **implantações** .

4. Selecione um objeto na grade de desempenho e, no painel Propriedades, localizado no lado direito, selecione a opção **exibir dados dinâmicos** . Se o cluster AKS estiver configurado com logon único usando o Azure AD, você será solicitado a autenticar no primeiro uso durante a sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.

    >[!NOTE]
    >Ao exibir os dados do seu espaço de trabalho do Log Analytics selecionando a opção **Exibir no Analytics** no painel Propriedades, os resultados da pesquisa de log mostrarão potencialmente **nós**, **conjuntos de daemon**, **conjuntos de réplicas**, **trabalhos**, **trabalhos cron**, **pods** e **contêineres** que talvez não existam mais. A tentativa de pesquisar logs de um contêiner que não está disponível `kubectl` também falhará aqui. Examine o recurso [Exibir no Analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre como exibir logs históricos, eventos e métricas.

Após a autenticação bem-sucedida, o painel do console de dados dinâmicos será exibido abaixo da grade de dados de desempenho. Se o indicador de status de busca mostrar uma marca de seleção verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começam a transmitir para o console.

Se o objeto selecionado era um contêiner, selecione a opção **eventos** no painel. Se você selecionou um nó, Pod ou controlador, a exibição de eventos será selecionada automaticamente.

![Painel de propriedades do controlador exibir eventos](./media/container-insights-livedata-overview/controller-properties-live-event.png)

O título do painel mostra o nome do pod com o qual o contêiner é agrupado.

### <a name="filter-events"></a>Filtrar eventos

Ao exibir eventos, você pode limitar os resultados usando o **filtro** Pill encontrado à direita da barra de pesquisa. Dependendo do recurso que você selecionou, o Pill lista um pod, um namespace ou um cluster do qual escolher.

## <a name="view-metrics"></a>Métricas de exibição

Você pode exibir dados de métrica em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós** ou **controladores** somente quando um **Pod** é selecionado. Para exibir as métricas, execute as etapas a seguir.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. No painel do cluster AKS, em **monitoramento** no lado esquerdo, escolha **insights**.

3. Selecione a guia **nós** ou **controladores** .

4. Selecione um objeto **Pod** na grade desempenho e, no painel Propriedades, localizado no lado direito, selecione a opção **exibir dados dinâmicos** . Se o cluster AKS estiver configurado com logon único usando o Azure AD, você será solicitado a autenticar no primeiro uso durante a sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.

    >[!NOTE]
    >Ao exibir os dados do seu espaço de trabalho do Log Analytics selecionando a opção **Exibir no Analytics** no painel Propriedades, os resultados da pesquisa de log mostrarão potencialmente **nós**, **conjuntos de daemon**, **conjuntos de réplicas**, **trabalhos**, **trabalhos cron**, **pods** e **contêineres** que talvez não existam mais. A tentativa de pesquisar logs de um contêiner que não está disponível `kubectl` também falhará aqui. Examine o recurso [Exibir no Analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre como exibir logs históricos, eventos e métricas.

Após a autenticação bem-sucedida, o painel do console de dados dinâmicos será exibido abaixo da grade de dados de desempenho. Os dados de métrica são recuperados e começam a transmitir para o console para apresentação nos dois gráficos. O título do painel mostra o nome do pod com o qual o contêiner é agrupado.

![Exibir exemplo de métricas de Pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Usando exibições de dados dinâmicos
As seções a seguir descrevem a funcionalidade que você pode usar nas diferentes exibições de dados ao vivo.

### <a name="search"></a>Search
O recurso de dados dinâmicos inclui a funcionalidade de pesquisa. No campo de **pesquisa** , você pode filtrar os resultados digitando uma palavra-chave ou termo e quaisquer resultados correspondentes são realçados para permitir uma revisão rápida. Ao exibir eventos, você pode limitar os resultados usando o **filtro** Pill encontrado à direita da barra de pesquisa. Dependendo do recurso que você selecionou, o Pill lista um pod, um namespace ou um cluster do qual escolher.

![Exemplo de filtro do painel do console de dados dinâmicos](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![Painel do console de dados dinâmicos exemplo de filtro para implantação](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>Scroll Lock e Pause

Para suspender o AutoScroll e controlar o comportamento do painel, permitindo que você role manualmente os novos dados lidos, você pode usar a opção de **rolagem** . Para reabilitar o AutoScroll, basta selecionar a opção de **rolagem** novamente. Você também pode pausar a recuperação de dados de log ou de evento selecionando a opção **Pause** e, quando estiver pronto para retomar, basta selecionar **reproduzir**.

![Painel de console de dados dinâmicos pausar exibição dinâmica](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![Painel de console de dados dinâmico pausar exibição dinâmica para implantação](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>É recomendável suspender ou pausar o AutoScroll por um curto período de tempo, enquanto soluciona um problema. Essas solicitações podem afetar a disponibilidade e a limitação da API kubernetes no cluster.

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante a operação deste recurso. Todas as informações capturadas durante a sessão são excluídas quando você fecha o navegador ou navega para fora dela. Os dados permanecem presentes apenas para visualização dentro da janela de cinco minutos do recurso de métricas; todas as métricas com mais de cinco minutos também são excluídas. O buffer de dados dinâmicos consulta dentro dos limites de uso de memória razoáveis.

## <a name="next-steps"></a>Próximas etapas

- Para continuar aprendendo a usar o Azure Monitor e monitorar outros aspectos do cluster do AKS, confira [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para criar alertas, visualizações ou executar análise adicional de seus clusters.
