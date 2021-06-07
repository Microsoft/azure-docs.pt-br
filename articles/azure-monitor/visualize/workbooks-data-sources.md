---
title: Fontes de dados de Azure Monitor pastas de trabalho | Microsoft docs
description: Simplifique relatórios complexos com pastas de trabalho Azure Monitor parametrizadas predefinidas e personalizadas criadas com base em várias fontes de dados
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 862c6c5253c1bb4481476b67c7cfb203c2568e24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700568"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Fontes de dados de Azure Monitor pastas de trabalho

As pastas de trabalho são compatíveis com um grande número de fontes de dados. Este artigo explicará as fontes de dados que estão disponíveis atualmente para Azure Monitor pastas de trabalho.

## <a name="logs"></a>Logs

As pastas de trabalho permitem consultar logs das seguintes fontes:

* Logs de Azure Monitor (recursos de Application Insights e espaços de trabalho de Log Analytics.)
* Dados centrados em recursos (logs de atividade)

Os autores da pasta de trabalho podem usar consultas KQL que transformam os dados de recurso subjacentes para selecionar um conjunto de resultados que pode ser visualizado como texto, gráficos ou grades.

![Captura de tela da interface de relatório de logs de pastas de trabalho](./media/workbooks-data-sources/logs.png)

Os autores da pasta de trabalho podem facilmente consultar em vários recursos, criando uma experiência de relatório sofisticada verdadeiramente unificada.

## <a name="metrics"></a>Métricas

Os recursos do Azure emitem [métricas](../essentials/data-platform-metrics.md) que podem ser acessadas via pastas de trabalho. As métricas podem ser acessadas em pastas de trabalho por meio de um controle especializado que permite especificar os recursos de destino, as métricas desejadas e sua agregação. Esses dados podem então ser plotados em gráficos ou grades.

![Captura de tela de gráficos de métricas de pasta de trabalho de utilização da CPU](./media/workbooks-data-sources/metrics-graph.png)

![Captura de tela da interface de métricas da pasta de trabalho](./media/workbooks-data-sources/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

As pastas de trabalho dão suporte à consulta de recursos e seus metadados usando o grafo de recursos do Azure (ARG). Essa funcionalidade é usada principalmente para criar escopos de consulta personalizados para relatórios. O escopo do recurso é expresso por meio de um subconjunto de KQL com suporte de ARG, que geralmente é suficiente para casos de uso comuns.

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa tipo de consulta para escolher o grafo de recursos do Azure e selecione as assinaturas a serem direcionadas. Use o controle de consulta para adicionar o ARG KQL-subconjunto que seleciona um subconjunto de recursos interessante.

![Captura de tela da consulta KQL do grafo de recursos do Azure](./media/workbooks-data-sources/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

A pasta de trabalho dá suporte a operações REST Azure Resource Manager. Isso permite que a capacidade de consultar o ponto de extremidade do management.azure.com sem a necessidade de fornecer seu próprio token de cabeçalho de autorização.

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa fonte de dados para escolher Azure Resource Manager. Forneça os parâmetros apropriados, como método http, caminho da URL, cabeçalhos, parâmetros de URL e/ou corpo.

> [!NOTE]
> Somente `GET` `POST` `HEAD` as operações, e têm suporte no momento.

## <a name="azure-data-explorer"></a>Azure Data Explorer

As pastas de trabalho agora têm suporte para a consulta de clusters do [Azure data Explorer](/azure/data-explorer/) com a poderosa linguagem de consulta [Kusto](/azure/kusto/query/index) .

![Captura de tela da janela de consulta do Kusto](./media/workbooks-data-sources/data-explorer.png)

## <a name="workload-health"></a>Integridade da carga de trabalho

Azure Monitor tem funcionalidade que monitora de forma proativa a disponibilidade e o desempenho de sistemas operacionais convidados Windows ou Linux. O Azure Monitor modela os principais componentes e suas relações, critérios de como medir a integridade desses componentes e quais componentes o alertam quando uma condição não íntegra é detectada. As pastas de trabalho permitem que os usuários usem essas informações para criar relatórios interativos sofisticados.

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa **tipo de consulta** para escolher integridade da carga de trabalho e selecione assinatura, grupo de recursos ou recursos de VM para destino. Use os menus suspensos de filtro de integridade para selecionar um subconjunto interessante de incidentes de integridade para suas necessidades analíticas.

![Captura de tela da consulta de alertas](./media/workbooks-data-sources/workload-health.png)

## <a name="azure-resource-health"></a>Azure Resource Health

As pastas de trabalho dão suporte à obtenção do Azure Resource Health e à combinação deles com outras fontes de dados para criar relatórios de integridade avançados e interativos

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa **tipo de consulta** para escolher a integridade do Azure e selecione os recursos para direcionar. Use os menus suspensos de filtro de integridade para selecionar um subconjunto interessante de problemas de recursos para suas necessidades analíticas.

![Captura de tela da consulta de alertas que mostra as listas de filtros de integridade.](./media/workbooks-data-sources/resource-health.png)

## <a name="change-analysis-preview"></a>Análise de alterações (versão prévia)

Para tornar um controle de consulta usando a [análise de alterações do aplicativo](../app/change-analysis.md) como a fonte de dados, use a lista suspensa fonte de *dados* e escolha *alterar análise (versão prévia)* e selecione um único recurso. As alterações até os últimos 14 dias podem ser mostradas. A lista suspensa *nível* pode ser usada para filtrar entre alterações "importantes", "normais" e "ruidosas", e essa lista suspensa dá suporte a parâmetros de pasta de trabalho do tipo [menu](workbooks-dropdowns.md)suspenso.

> [!div class="mx-imgBorder"]
> ![Uma captura de tela de uma pasta de trabalho com análise de alterações](./media/workbooks-data-sources/change-analysis-data-source.png)

## <a name="merge-data-from-different-sources"></a>Mesclar dados de fontes diferentes

Geralmente, é necessário reunir dados de diferentes fontes que melhoram a experiência de informações. Um exemplo é aumentar as informações de alerta ativas com dados de métrica relacionados. Isso permite que os usuários vejam não apenas o efeito (um alerta ativo), mas também as possíveis causas (por exemplo, alto uso da CPU). O domínio de monitoramento tem várias fontes de dados correlacionadas que geralmente são críticas para o fluxo de trabalho de triagem e diagnóstico.

As pastas de trabalho permitem não apenas a consulta de diferentes fontes de dados, mas também fornecem controles simples que permitem que você mescle ou ingresse os dados para fornecer ideias sofisticadas. O `merge` controle é a maneira de obtê-lo.

O exemplo a seguir combina dados de alerta com dados de desempenho de VM do log Analytics para obter uma grade de insights sofisticada.

> [!div class="mx-imgBorder"]
> ![Uma captura de tela de uma pasta de trabalho com um controle de mesclagem que combina dados de alerta e log Analytics](./media/workbooks-data-sources/merge-control.png)

As pastas de trabalho oferecem suporte a várias mesclagens:

* Junção exclusiva interna
* Junção interna completa
* Junção externa completa
* Junção externa esquerda
* Junção externa direita
* Semijunção à esquerda
* Semijunção à direita
* Anti-junção esquerda
* Anti-junção direita
* Union
* Duplicar tabela

## <a name="json"></a>JSON

O provedor JSON permite que você crie um resultado de consulta de conteúdo JSON estático. Ele é mais comumente usado em parâmetros para criar parâmetros de lista suspensa de valores estáticos. Os objetos ou matrizes JSON simples serão convertidos automaticamente em linhas e colunas de grade.  Para comportamentos mais específicos, você pode usar a guia resultados e as configurações de JSONPath para configurar colunas.

Este provedor dá suporte a [JSONPath](workbooks-jsonpath.md).

## <a name="alerts-preview"></a>Alertas (versão prévia)

> [!NOTE]
> A maneira sugerida de consultar as informações de alerta do Azure é usando a fonte de dados do [grafo de recursos do Azure](#azure-resource-graph) consultando a `AlertsManagementResources` tabela.
>
> Consulte a [referência de tabela do grafo de recursos do Azure](../../governance/resource-graph/reference/supported-tables-resources.md)ou o [modelo de alertas](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) para obter exemplos.
>
> A fonte de dados de alertas permanecerá disponível por um período de tempo enquanto os autores fazem a transição para usando ARG. O uso desta fonte de dados em modelos não é recomendado. 

As pastas de trabalho permitem que os usuários visualizem os alertas ativos relacionados aos seus recursos. Limitações: a fonte de dados de alertas requer acesso de leitura à assinatura para consultar recursos e pode não mostrar tipos de alertas mais recentes. 

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa _fonte de dados_ para escolher _alertas (versão prévia)_ e selecione as assinaturas, grupos de recursos ou recursos a serem direcionados. Use os menus suspensos de filtro de alerta para selecionar um subconjunto interessante de alertas para suas necessidades analíticas.

## <a name="custom-endpoint"></a>Ponto de extremidade personalizado

As pastas de trabalho dão suporte à obtenção de dados de qualquer fonte externa. Se seus dados estiverem fora do Azure, você poderá colocá-los em pastas de trabalho usando esse tipo de fonte de dados.

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa _fonte de dados_ para escolher _ponto de extremidade personalizado_. Forneça os parâmetros apropriados, como `Http method` , `url` , `headers` `url parameters` e/ou `body` . Verifique se a fonte de dados dá suporte a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) , caso contrário, a solicitação falhará.

Para evitar fazer chamadas automaticamente para hosts não confiáveis ao usar modelos, o usuário precisa marcar os hosts usados como confiáveis. Isso pode ser feito clicando no botão _Adicionar como confiável_ ou adicionando-o como um host confiável nas configurações da pasta de trabalho. Essas configurações serão salvas em [navegadores que dão suporte a IndexDb com Web Workers](https://caniuse.com/#feat=indexeddb).

> [!NOTE]
> Não grave nenhum segredo em nenhum dos campos (,, `headers` `parameters` `body` , `url` ), pois eles ficarão visíveis para todos os usuários da pasta de trabalho.

Este provedor dá suporte a [JSONPath](workbooks-jsonpath.md).

## <a name="next-steps"></a>Próximas etapas

* [Comece a aprender mais](./workbooks-overview.md#visualizations) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](./workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.
* [Dicas de otimização de consulta Log Analytics](../logs/query-optimization.md)