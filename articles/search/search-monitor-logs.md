---
title: Coletar dados de log
titleSuffix: Azure Cognitive Search
description: Colete e analise dados de log habilitando uma configuração de diagnóstico e, em seguida, use a linguagem de consulta Kusto para explorar os resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3c8dd5cd9da2fd1e741635a6471c0662066d147e
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709932"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Coletar e analisar dados de log para o Azure Pesquisa Cognitiva

Os logs de diagnóstico ou operacionais fornecem informações sobre as operações detalhadas do Azure Pesquisa Cognitiva e são úteis para monitorar processos de serviço e carga de trabalho. Internamente, a Microsoft preserva as informações do sistema no back-end por um curto período de tempo (cerca de 30 dias), suficiente para investigação e análise se você arquivar um tíquete de suporte. No entanto, se você quiser a propriedade sobre os dados operacionais, deverá definir uma configuração de diagnóstico para especificar onde as informações de log serão coletadas.

O log de diagnóstico é habilitado por meio da integração com o [Azure monitor](../azure-monitor/index.yml). 

Ao configurar o log de diagnóstico, você será solicitado a especificar um mecanismo de armazenamento. A tabela a seguir enumera as opções para coletar e manter dados.

| Recurso | Usado para |
|----------|----------|
| [Enviar para o workspace do Log Analytics](../azure-monitor/essentials/tutorial-resource-logs.md) | Os eventos e as métricas são enviados para um espaço de trabalho Log Analytics, que pode ser consultado no portal para retornar informações detalhadas. Para obter uma introdução, consulte Introdução [aos logs de Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md) |
| [Arquivar com armazenamento de BLOBs](../storage/blobs/storage-blobs-overview.md) | Os eventos e as métricas são arquivados em um contêiner de BLOB e armazenados em arquivos JSON. Os logs podem ser bastante granulares (por hora/minuto), úteis para pesquisar um incidente específico, mas não para investigação aberta. Use um editor de JSON para exibir um arquivo de log bruto ou Power BI para agregar e Visualizar dados de log.|
| [Transmitir para o Hub de eventos](../event-hubs/index.yml) | Os eventos e as métricas são transmitidos para um serviço de hubs de eventos do Azure. Escolha esta opção como um serviço de coleta de dados alternativo para logs muito grandes. |

## <a name="prerequisites"></a>Pré-requisitos

Crie recursos com antecedência para que você possa selecionar um ou mais ao configurar o log de diagnóstico.

+ [Criar um espaço de trabalho do log Analytics](../azure-monitor/logs/quick-create-workspace.md)

+ [Criar uma conta de armazenamento](../storage/common/storage-account-create.md)

+ [Criar um Hub de Eventos](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Habilitar coleta de dados

As configurações de diagnóstico especificam como os eventos registrados e as métricas são coletados.

1. Em **Monitoramento**, selecione **Configurações de diagnóstico**.

   ![Configurações de diagnóstico](./media/search-monitor-usage/diagnostic-settings.png "Configurações de Diagnóstico")

1. Selecione **+ Adicionar configuração de diagnóstico**

1. Marque **log Analytics**, selecione seu espaço de trabalho e selecione **OperationLogs** e **biométricas**.

   ![Configurar a coleta de dados](./media/search-monitor-usage/configure-storage.png "Configurar a coleta de dados")

1. Salve a configuração.

1. Depois que o registro em log tiver sido habilitado, use o serviço de pesquisa para começar a gerar logs e métricas. Levará tempo antes que os eventos e as métricas registrados fiquem disponíveis.

Por Log Analytics, serão vários minutos antes que os dados estejam disponíveis, após o qual você pode executar consultas Kusto para retornar dados. Para obter mais informações, consulte [monitorar solicitações de consulta](search-monitor-logs.md).

Para o armazenamento de BLOBs, ele leva uma hora antes que os contêineres apareçam no armazenamento de BLOBs. Haverá um blob por hora, por contêiner. Os contêineres são criados apenas quando há uma atividade para registrar ou medir. Quando os dados são copiados para uma conta de armazenamento, eles são formatados como JSON e são colocados em dois contêineres:

+ insights-logs-operationlogs: para logs de tráfego de pesquisa
+ insights-metrics-pt1m: para métrica

## <a name="query-log-information"></a>Informações do log de consulta

Duas tabelas contêm logs e métricas para o Azure Pesquisa Cognitiva: **AzureDiagnostics** e **AzureMetrics**.

1. Em **monitoramento**, selecione **logs**.

1. Insira **AzureMetrics** na janela de consulta. Execute esta consulta simples para familiarizar-se com os dados coletados nesta tabela. Role pela tabela para exibir as métricas e os valores. Observe a contagem de registros na parte superior e, se o serviço estiver coletando métricas por algum tempo, talvez você queira ajustar o intervalo de tempo para obter um conjunto de dados gerenciável.

   ![Tabela AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabela AzureMetrics")

1. Insira a consulta a seguir para retornar um conjunto de resultados tabulares.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Repita as etapas anteriores, começando com **AzureDiagnostics** para retornar todas as colunas para fins informativos, seguidos por uma consulta mais seletiva que extrai informações mais interessantes.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabela AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabela AzureDiagnostics")

## <a name="kusto-query-examples"></a>Exemplos de consulta do Kusto

Se você habilitou o log de diagnóstico, poderá consultar **AzureDiagnostics** para obter uma lista de operações que foram executadas em seu serviço e quando. Você também pode correlacionar a atividade para investigar alterações no desempenho.

#### <a name="example-list-operations"></a>Exemplo: listar operações 

Retornar uma lista de operações e uma contagem de cada uma delas.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exemplo: correlacionar operações

Correlacione a solicitação de consulta com operações de indexação e processe os pontos de dados em um gráfico de tempo para ver as operações coincidirem.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Operações registradas

Os eventos registrados pelo Azure Monitor incluem aqueles relacionados à indexação e consultas. A tabela **AzureDiagnostics** no log Analytics coleta dados operacionais relacionados a consultas e indexação.

| OperationName | Descrição |
|---------------|-------------|
| Perstats | Essa operação é uma chamada de rotina para [obter estatísticas de serviço](/rest/api/searchservice/get-service-statistics), chamadas direta ou implicitamente para preencher uma página de visão geral do portal quando ela é carregada ou atualizada. |
| Consulta. Search |  Solicitações de consulta em um índice consulte [monitorar consultas](search-monitor-queries.md) para obter informações sobre consultas registradas.|
| Indexação. index  | Esta operação é uma chamada para [Adicionar, atualizar ou excluir documentos](/rest/api/searchservice/addupdate-or-delete-documents). |
| índices. Funções | Este é um índice criado pelo assistente de importação de dados. |
| Indexadores. Create | Crie um indexador explicitamente ou implicitamente por meio do assistente de importação de dados. |
| Indexadores. Get | Retorna o nome de um indexador sempre que o indexador é executado. |
| Indexadores. status | Retorna o status de um indexador sempre que o indexador é executado. |
| Fontes de fonte. Get | Retorna o nome da fonte de dados sempre que um indexador é executado.|
| Indexes. Get | Retorna o nome de um índice sempre que um indexador é executado. |

## <a name="log-schema"></a>Esquema do log

Se você estiver criando relatórios personalizados, as estruturas de dados que contêm dados de log de Pesquisa Cognitiva do Azure estão em conformidade com o esquema abaixo. Para o armazenamento de BLOBs, cada blob tem um objeto raiz chamado **registros** que contém uma matriz de objetos de log. Cada blob contém registros de todas as operações que ocorreram durante a mesma hora.

A tabela a seguir é uma lista parcial de campos comuns ao log de recursos.

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| timeGenerated |DATETIME |"2018-12-07T00:00:43.6872559Z" |Carimbo de data/hora da operação |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Seu ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2020-06-30" |A api-version usada |
| category |string |"OperationLogs" |constante |
| resultType |string |"Success" |Valores possíveis: Success ou Failure |
| resultSignature |INT |200 |Código do resultado HTTP |
| durationMS |INT |50 |Duração da operação em milissegundos |
| properties |objeto |confira a seguinte tabela |Objeto que contém os dados específicos da operação |

### <a name="properties-schema"></a>Esquema de propriedades

As propriedades abaixo são específicas para Pesquisa Cognitiva do Azure.

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| Description_s |string |"GET /indexes('content')/docs" |Ponto de extremidade da operação |
| Documents_d |INT |42 |Número de documentos processados |
| IndexName_s |string |"Test-index" |Nome do índice associado à operação |
| Query_s |string |"? Search = AzureSearch&$count = true&API-Version = 2020-06-30" |Parâmetros da consulta |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para solicitações de consulta e medidas em intervalos de um minuto. Cada métrica expõe valores mínimo, máximo e médios por minuto. Para obter mais informações, consulte [monitorar solicitações de consulta](search-monitor-queries.md).

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |sua ID de recurso |
| metricName |string |"Latency" |o nome da métrica |
| time |DATETIME |"2018-12-07T00:00:43.6872559Z" |carimbo de data/hora da operação |
| média |INT |64 |O valor médio das amostras brutas no intervalo de tempo de métrica, unidades em segundos ou percentual, dependendo da métrica. |
| mínimo |INT |37 |O valor mínimo das amostras brutas no intervalo de tempo de métrica, unidades em segundos. |
| máximo |INT |78 |O valor máximo das amostras brutas no intervalo de tempo de métrica, unidades em segundos.  |
| total |INT |258 |O valor total das amostras brutas no intervalo de tempo de métrica, unidades em segundos.  |
| count |INT |4 |O número de métricas emitidas de um nó para o log dentro do intervalo de um minuto.  |
| intervalo de tempo |string |"PT1M" |O intervalo de tempo da métrica no ISO 8601. |

É comum que as consultas sejam executadas em milissegundos, portanto, somente as consultas que medem como segundos aparecerão em métrica como QPS.

Para a métrica de **consultas de pesquisa por segundo** , o mínimo é o valor mais baixo para consultas de pesquisa por segundo que foi registrado durante esse minuto. O mesmo se aplica ao valor máximo. O valor médio é a agregação durante todo o minuto. Por exemplo, em um minuto, você pode ter um padrão como este: um segundo de alta carga que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média e, finalmente, um segundo com apenas uma consulta, que é o mínimo.

Para **consultas de pesquisa limitadas percentual**, mínimo, máximo, média e total, todos têm o mesmo valor: a porcentagem de consultas de pesquisa que foram limitadas, do número total de consultas de pesquisa durante um minuto.

## <a name="view-raw-log-files"></a>Exibir arquivos de log brutos

O armazenamento de BLOBs é usado para arquivar arquivos de log. Você pode usar qualquer editor de JSON para exibir o arquivo de log. Se você não tem um, recomendamos usar o [Visual Studio Code](https://code.visualstudio.com/download).

1. No portal do Azure, abra sua conta de armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Você deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Esses contêineres são criados pelo Azure Pesquisa Cognitiva quando os dados de log são exportados para o armazenamento de BLOBs.

3. Clique para baixo na hierarquia de pastas até alcançar o arquivo .json.  Use o menu de contexto para baixar o arquivo.

Depois de baixar o arquivo, abra-o em um editor de JSON para exibir o conteúdo.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, examine os conceitos básicos do monitoramento do serviço de pesquisa para saber mais sobre a gama completa de recursos de supervisão.

> [!div class="nextstepaction"]
> [Monitorar operações e atividades no Azure Pesquisa Cognitiva](search-monitor-usage.md)