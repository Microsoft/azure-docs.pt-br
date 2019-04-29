---
title: Monitorar o uso de recursos e métricas de consulta de um serviço de pesquisa – Azure Search
description: Habilite o registro em log, obtenha métricas de atividades de consulta, uso de recursos e outros dados do sistema com o serviço Azure Search.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f4a0cba18f27c9cabfc03d1934469e6899c5cd18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564694"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Monitorar a atividade de consulta e o consumo de recursos no Azure Search

Na página Visão geral do serviço Azure Search, é possível exibir os dados do sistema sobre o uso de recursos, as métricas de consulta e quanto da cota está disponível para criar mais índices, indexadores e fontes de dados. Também é possível usar o portal para configurar o Log Analytics ou outro recurso usado para coleta de dados persistentes. 

A configuração de logs é útil para o autodiagnóstico e a preservação do histórico operacional. Internamente, os logs existem no back-end por um curto período de tempo, o suficiente para investigação e análise, caso você registre um tíquete de suporte. Se quiser controlar e acessar as informações de log, é preciso configurar uma das soluções descritas neste artigo.

Neste artigo, saiba mais sobre as opções de monitoramento, como habilitar o registro e o armazenamento de logs, e como exibir o conteúdo do log.

## <a name="metrics-at-a-glance"></a>Visão rápida de métricas

As seções **Uso** e **Monitoramento** incorporadas na página Visão geral informam o consumo de recursos e as métricas de execução de consulta. Essas informações ficam disponíveis assim que você começar a usar o serviço, sem a necessidade de configuração. Esta página é atualizada em períodos de poucos minutos. Se estiver decidindo sobre [qual camada usar para cargas de trabalho de produção](search-sku-tier.md), ou se quiser [ajustar o número de partições e réplicas ativas](search-capacity-planning.md), essas métricas podem ajudá-lo nessas decisões ao mostrar o quão rápido os recursos são consumidos e o quão bem a configuração atual lida com a carga existente.

A guia **Uso** mostra a disponibilidade de recursos em relação aos [limites](search-limits-quotas-capacity.md) atuais. A ilustração a seguir aplica-se ao serviço gratuito, que está limitado a três objetos de cada tipo e 50 MB de armazenamento. Um serviço Basic ou Standard tem limites maiores e, se você aumentar a quantidade de partições, o armazenamento máximo aumenta proporcionalmente.

![Status de uso em relação aos limites efetivos](./media/search-monitor-usage/usage-tab.png
 "Status de uso em relação aos limites efetivos")

## <a name="queries-per-second-qps-and-other-metrics"></a>QPS (consultas por segundo) e outras métricas

A guia **Monitoramento** mostra a média móvel das métricas, como pesquisar *QPS (Consultas por segundo)*, agregadas por minuto. 
*Pesquisar latência* demonstra a quantidade de tempo que o serviço de pesquisa precisou para processar consultas de pesquisa, agregadas por minuto. A *Porcentagem das consultas de pesquisa acelerada* (não exibida) é o percentual de consultas de pesquisa que foram limitadas, também agregadas por minuto.

Esses números são aproximados e têm a intenção de fornecer uma ideia geral de quão bem o seu sistema está atendendo às solicitações. O QPS real pode ser maior ou menor que o número informado no portal.

![Consultas por segundo de atividade](./media/search-monitor-usage/monitoring-tab.png "Consultas por segundo de atividade")

## <a name="activity-logs"></a>Logs de atividade

O **Log de atividades** coleta as informações do Azure Resource Manager. Exemplos de informações encontradas no log de atividades incluem a criação ou exclusão de um serviço, a atualização de um grupo de recursos, a verificação da disponibilidade do nome ou a obtenção da chave de acesso do serviço para lidar com uma solicitação. 

Você pode acessar o **Log de atividades** no painel de navegação à esquerda, em Notificações na barra de comando da janela superior ou na página **Diagnosticar e solucionar problemas**.

Para tarefas dentro de serviços, como criar um índice ou excluir uma fonte de dados, você vê notificações genéricas, como "Obter a chave de administrador" para cada solicitação, mas não a ação específica em si. Para esse nível de informações, é necessário ativar uma solução de monitoramento do complemento.

## <a name="add-on-monitoring-solutions"></a>Soluções de monitoramento do complemento

O Azure Search não armazena nenhum dado além dos objetos que ele gerencia, o que significa que os dados de log devem ser armazenados externamente. Você pode configurar qualquer um dos recursos abaixo se quiser manter os dados de log. 

A tabela a seguir compara as opções para armazenar logs, adicionar monitoramento detalhado das operações de serviço e consultar cargas de trabalho por meio do Application Insights.

| Resource | Usado para |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Eventos registrados em log e métricas de consulta, com base nos esquemas de abaixo, correlacionadas com eventos de usuário em seu aplicativo. Essa é a única solução que considera as ações ou os sinais do usuário, os eventos de mapeamento de pesquisas iniciadas pelo usuário, ao invés das solicitações de filtro enviadas pelo código do aplicativo. Para usar essa abordagem, copie e cole o código de instrumentação em seus arquivos de origem para direcionar as informações de solicitação para o Application Insights. Para saber mais, confira [Análise de tráfego de pesquisa](search-traffic-analytics.md). |
| [Logs do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Eventos registrados em log e métricas de consulta, com base nos esquemas de abaixo. Eventos são registrados para um espaço de trabalho do Log Analytics. É possível executar as consultas em um espaço de trabalho para retornar informações detalhadas do log. Para obter mais informações, consulte [começar com os logs do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Eventos registrados em log e métricas de consulta, com base nos esquemas de abaixo. Os eventos são registrados em um contêiner de blob e armazenados em arquivos JSON. Use um editor de JSON para exibir o conteúdo do arquivo.|
| [Hub de Evento](https://docs.microsoft.com/azure/event-hubs/) | Eventos registrados em log e métricas de consulta, com base nos esquemas documentados neste artigo. Escolha esta opção como um serviço de coleta de dados alternativo para logs muito grandes. |

Logs do Azure Monitor e o armazenamento de BLOBs estão disponíveis como um serviço gratuito compartilhado para que você pode experimentar sem custo para o tempo de vida da assinatura do Azure. O Application Insights é gratuito para se inscrever e usar, desde que o tamanho de dados do aplicativo esteja abaixo de certos limites (confira a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para saber mais).

A próxima seção o orienta sobre as etapas de ativação e uso do armazenamento de blobs do Azure para coletar e acessar dados de log criados por operações do Azure Search.

## <a name="enable-logging"></a>Habilitar o registro em log

O registro em log para cargas de trabalho de indexação e consulta está desativado por padrão e depende das soluções de complemento para a infraestrutura de log e o armazenamento externo de longo prazo. Por si só, os únicos dados persistentes no Azure Search são os objetos que ele cria e gerencia, portanto, os logs devem ser armazenados em outro lugar.

Nesta seção, você aprenderá como usar o armazenamento de blob para armazenar dados de métricas e eventos registrados em log.

1. [Crie uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) se ainda não tiver uma. Você pode colocá-la no mesmo grupo de recursos do Azure Search para simplificar a limpeza posteriormente, se quiser excluir todos os recursos usados neste exercício.

2. Abra a página Visão geral do serviço de pesquisa. No painel de navegação à esquerda, role para baixo até **Monitoramento** e clique em **Habilitar o monitoramento**.

   ![Habilitar o monitoramento](./media/search-monitor-usage/enable-monitoring.png "Habilitar monitoramento")

3. Escolha os dados que deseja exportar: Logs, Métricas ou ambos. Você pode copiá-lo para uma conta de armazenamento, enviá-lo para um hub de eventos ou exportá-lo para os logs do Azure Monitor.

   Para arquivamento no armazenamento de blobs, só deve existir a conta de armazenamento. Contêineres e blobs são criados quando os dados de log são exportados.

   ![Configurar arquivamento de armazenamento de blobs](./media/search-monitor-usage/configure-blob-storage-archive.png "Configurar arquivamento de armazenamento de blobs")

4. Salve o perfil.

5. Teste o registro em log criando ou excluindo objetos (cria eventos de log) e enviando consultas (gera métricas). 

O registro em log é habilitado após salvar o perfil. Os contêineres são criados apenas quando há uma atividade para registrar ou medir. Quando os dados são copiados para uma conta de armazenamento, eles são formatados como JSON e são colocados em dois contêineres:

* insights-logs-operationlogs: para logs de tráfego de pesquisa
* insights-metrics-pt1m: para métrica

**Ele leva uma hora antes dos contêineres aparecerão no armazenamento de BLOBs. Há um blob por hora, por contêiner.**

Você pode usar o [Visual Studio Code](#download-and-open-in-visual-studio-code) ou outro editor de JSON para exibir os arquivos. 

### <a name="example-path"></a>Exemplo de caminho

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Esquema do log
Os blobs que contêm os logs de tráfego do serviço de pesquisa são estruturados como descrito nesta seção. Cada blob tem um objeto-raiz chamado **records** contendo uma matriz de objetos do log. Cada blob contém registros de todas as operações que ocorreram durante a mesma hora.

| NOME | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Carimbo de data/hora da operação |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Seu ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2017-11-11" |A api-version usada |
| category |string |"OperationLogs" |constante |
| resultType |string |"Success" |Valores possíveis: Êxito ou Falha |
| resultSignature |int |200 |Código do resultado HTTP |
| durationMS |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |confira a seguinte tabela |Objeto que contém os dados específicos da operação |

**Esquema de propriedades**

| NOME | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| DESCRIÇÃO |string |"GET /indexes('content')/docs" |Ponto de extremidade da operação |
| Consultar |string |"?search=AzureSearch&$count=true&api-version=2017-11-11" |Parâmetros da consulta |
| Documentos |int |42 |Número de documentos processados |
| IndexName |string |"testindex" |Nome do índice associado à operação |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para solicitações de consulta.

| NOME | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |id do recurso |
| metricName |string |"Latency" |o nome da métrica |
| tempo real |datetime |"2018-12-07T00:00:43.6872559Z" |carimbo de data/hora da operação |
| média |int |64 |O valor médio das amostras brutas no intervalo de agregação da métrica |
| mínimo |int |37 |O valor mínimo das amostras brutas no intervalo de agregação da métrica |
| máximo |int |78 |O valor máximo das amostras brutas no intervalo de agregação da métrica |
| total |int |258 |O valor total das amostras brutas no intervalo de agregação da métrica |
| count |int |4 |O número de amostras brutas usadas para gerar a métrica |
| intervalo de tempo |string |"PT1M" |O intervalo de agregação da métrica no ISO 8601 |

Todas as métricas são reportadas em intervalos de um minuto. Cada métrica expõe valores mínimo, máximo e médios por minuto.

Para a métrica SearchQueriesPerSecond, o valor mínimo é o valor mais baixo para consultas de pesquisa por segundo que foram registradas durante esse minuto. O mesmo se aplica ao valor máximo. O valor médio é a agregação durante todo o minuto.
Considere este cenário durante um minuto, um segundo com uma carga muito alta, que é o valor máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga mediana, e, por fim, um segundo com apenas uma consulta, que será o valor mínimo.

Para ThrottledSearchQueriesPercentage, os valores mínimo, máximo, médio e total possuem o mesmo valor: a porcentagem de consultas de pesquisa que foram limitadas, a partir do no número total de consultas de pesquisa durante um minuto.

## <a name="download-and-open-in-visual-studio-code"></a>Baixar e abrir no Visual Studio Code

Você pode usar qualquer editor de JSON para exibir o arquivo de log. Se você não tem um, recomendamos usar o [Visual Studio Code](https://code.visualstudio.com/download).

1. No portal do Azure, abra sua conta de armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Você deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Esses contêineres são criados pelo Azure Search quando os dados de logs são exportados para o armazenamento de blobs.

3. Clique para baixo na hierarquia de pastas até alcançar o arquivo .json.  Use o menu de contexto para baixar o arquivo.

Depois de baixar o arquivo, abra-o em um editor de JSON para exibir o conteúdo.

## <a name="use-system-apis"></a>Usar APIs do sistema
A API REST do Azure Search e o SDK do .NET fornecem acesso via programação a informações sobre métricas de serviços, índices e indexadores, além de contagens de documentos.

* [Obter estatísticas de serviços](/rest/api/searchservice/get-service-statistics)
* [Obter estatísticas de índice](/rest/api/searchservice/get-index-statistics)
* [Contar documentos](/rest/api/searchservice/count-documents)
* [Obter o status do indexador](/rest/api/searchservice/get-indexer-status)

Para habilitar usando o PowerShell ou a CLI do Azure, consulte a documentação [aqui](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>Próximas etapas

Confira [Gerenciar o seu serviço do Search no Microsoft Azure](search-manage.md), para saber mais sobre a administração do serviço, e [Desempenho e otimização](search-performance-optimization.md), para obter diretrizes de ajuste.