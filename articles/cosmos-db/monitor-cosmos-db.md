---
title: Monitorar Azure Cosmos DB | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do Azure Cosmos DB.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: f18d1850cb6ccf28ff70f826e3d4bfe74ae05c40
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178721"
---
# <a name="monitor-azure-cosmos-db"></a>Monitorar o Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelos bancos de dados do Azure Cosmos e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

Você pode monitorar seus dados com as métricas do lado do cliente e do servidor. Ao usar as métricas do lado do servidor, você pode monitorar os dados armazenados no Azure Cosmos DB com as seguintes opções:

* **Monitorar do portal de Azure Cosmos DB:** Você pode monitorar com as métricas disponíveis na guia **métricas** da conta do Azure Cosmos. As métricas nessa guia incluem taxa de transferência, armazenamento, disponibilidade, latência, consistência e métricas de nível de sistema. Por padrão, essas métricas têm um período de retenção de 7 dias. Para saber mais, consulte a seção [monitoramento de dados coletados de Azure Cosmos DB](#monitoring-from-azure-cosmos-db) deste artigo.

* **Monitorar com métricas no Azure monitor:** Você pode monitorar as métricas de sua conta do Azure Cosmos e criar painéis do Azure Monitor. Azure Monitor coleta as métricas de Azure Cosmos DB por padrão, você não configurou nada explicitamente. Essas métricas são coletadas com granularidade de um minuto, a granularidade pode variar com base na métrica que você escolher. Por padrão, essas métricas têm um período de retenção de 30 dias. A maioria das métricas que estão disponíveis nas opções anteriores também estão disponíveis nessas métricas. Os valores de dimensão para as métricas, como o nome do contêiner, não diferenciam maiúsculas de minúsculas. Portanto, você precisa usar a comparação de maiúsculas e minúsculas ao fazer comparações de cadeias de caracteres nesses valores de dimensão. Para saber mais, consulte a seção [analisar dados de métrica](#analyze-metric-data) deste artigo.

* **Monitorar com os logs de diagnóstico no Azure monitor:** Você pode monitorar os logs da sua conta do Azure Cosmos e criar painéis do Azure Monitor. A telemetria, como eventos e rastreamentos que ocorrem em uma granularidade de segundo, são armazenados como logs. Por exemplo, se a taxa de transferência de um contêiner for alterada, as propriedades de uma conta do cosmos serão alteradas, esses eventos serão capturados nos logs. Você pode analisar esses logs executando consultas nos dados coletados. Para saber mais, consulte a seção [analisar dados de log](#analyze-log-data) deste artigo.

* **Monitorar programaticamente com SDKs:** Você pode monitorar sua conta do Azure Cosmos por meio de programação usando .NET, Java, Python, Node.js SDKs e os cabeçalhos na API REST. Para saber mais, consulte a seção [monitoramento Azure Cosmos DB programaticamente](#monitor-cosmosdb-programmatically) deste artigo.

A imagem a seguir mostra opções diferentes disponíveis para monitorar Azure Cosmos DB conta por meio de portal do Azure:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Opções de monitoramento disponíveis no portal do Azure" border="false":::

Ao usar Azure Cosmos DB, no lado do cliente, você pode coletar os detalhes de cobrança de solicitação, ID da atividade, informações de rastreamento de exceção/pilha, código de status/substatus HTTP, Cadeia de caracteres de diagnóstico para depurar qualquer problema que possa ocorrer. Essas informações também serão necessárias se você precisar entrar na equipe de suporte do Azure Cosmos DB. 

## <a name="monitor-overview"></a>Visão geral do Monitor

A página de **visão geral** no portal do Azure para cada conta de Azure Cosmos DB inclui uma breve exibição do uso de recursos, como total de solicitações, solicitações que resultaram em um código de status HTTP específico e cobrança por hora. Essas informações são úteis, no entanto, apenas uma pequena quantidade de dados de monitoramento está disponível neste painel. Alguns desses dados são coletados automaticamente e estão disponíveis para análise assim que você criar o recurso. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure Cosmos DB cria dados de monitoramento usando o [Azure Monitor](../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e locais.

Se você ainda não estiver familiarizado com o monitoramento dos serviços do Azure, comece com o artigo [monitorando os recursos do Azure com Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) que descreve os seguintes conceitos:

* O que é o Azure Monitor?
* Custos associados ao monitoramento
* Monitoramento de dados coletados no Azure
* Configuração de coleta de dados
* Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados do Azure Cosmos DB e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor para o Azure Cosmos DB

Azure Monitor para Azure Cosmos DB baseia-se no [recurso de pastas de trabalho do Azure monitor](../azure-monitor/visualize/workbooks-overview.md) e usa os mesmos dados de monitoramento coletados para Azure Cosmos DB descritos nas seções abaixo. Use Azure Monitor para exibir o desempenho geral, as falhas, a capacidade e a integridade operacional de todos os seus recursos do Azure Cosmos DB em uma experiência interativa unificada e aproveitar os outros recursos do Azure Monitor para análise e alertas detalhados. Para saber mais, confira o artigo [Explorar o Azure Monitor para o Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md).

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> Dados de monitoramento 

O Azure Cosmos DB coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [Dados de monitoramento dos recursos do Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). Confira [Referência de dados de monitoramento do Azure Cosmos DB](monitor-cosmos-db-reference.md) para obter uma referência detalhada dos logs e métricas criados pelo Azure Cosmos DB.

A página **Visão Geral** no portal do Azure para cada banco de dados do Azure Cosmos inclui uma visão breve do uso do banco de dados, incluindo sua solicitação e o uso de cobrança por hora. Essas informações são úteis, mas apenas uma pequena quantidade de dados de monitoramento está disponível. Alguns desses dados são coletados automaticamente e ficam disponíveis para análise assim que você cria o banco de dados, embora você possa habilitar a coleta de dado adicional com algumas configurações.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Página de visão geral":::

## <a name="collection-and-routing"></a>Coleta e roteamento

As métricas de plataforma e o Log de atividades são coletados e armazenados automaticamente, mas podem ser roteados para outras localizações usando uma configuração de diagnóstico.  

Os Logs de Recursos não são coletados e armazenados até você criar uma configuração de diagnóstico e roteá-los para uma ou mais localizações.

Consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](cosmosdb-monitor-resource-logs.md) para o processo detalhado para criar uma configuração de diagnóstico usando o portal do Azure e alguns exemplos de consulta de diagnóstico. Ao criar uma configuração de diagnóstico, você especifica quais categorias de logs coletar.

As métricas e os logs que você pode coletar são discutidos nas seções a seguir.

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> Analisando métricas

O Azure Cosmos DB fornece uma experiência personalizada para trabalhar com métricas. Você pode analisar as métricas para o Azure Cosmos DB com métricas de outros serviços do Azure usando o Metrics Explorer abrindo **Métricas** no menu **Azure Monitor**. Consulte [Introdução ao Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta. Você também pode fazer check-out de como monitorar a [latência do lado do servidor](monitor-server-side-latency.md), o uso da unidade de [solicitação](monitor-request-unit-usage.md)e o uso da [unidade de solicitação normalizada](monitor-normalized-request-units.md) para seus recursos de Azure Cosmos DB.

Para obter uma lista das métricas de plataforma coletadas para Azure Cosmos DB, consulte o artigo [monitoramento Azure Cosmos DB métricas de referência de dados](monitor-cosmos-db-reference.md#metrics) .

Todas as métricas para o Azure Cosmos DB estão no namespace **Métricas padrão do Cosmos DB**. Você pode usar as seguintes dimensões com essas métricas ao adicionar um filtro a um gráfico:

* CollectionName
* DatabaseName
* OperationType
* Região
* StatusCode

Para referência, você pode ver uma lista de [todas as métricas de recurso com suporte no Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Exibir métricas de nível de operação para o Azure Cosmos DB

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de menus de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Painel Métricas no Azure Monitor":::

1. No painel **Métricas** > **Selecionar um recurso** > escolha a **assinatura** e o **grupo de recursos** necessários. Para o **Tipo de recurso**, selecione **Contas do Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **Aplicar**.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Escolha uma conta do Cosmos DB para exibir as métricas":::

1. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria](monitor-cosmos-db-reference.md). Neste exemplo, vamos selecionar **Unidades de solicitação** e **Média** como o valor de agregação.

   Além desses detalhes, você também pode selecionar o **Intervalo de tempo** e a **Granularidade de tempo** das métricas. Você pode exibir as métricas de, no máximo, os últimos 30 dias.  Depois que você aplicar o filtro, um gráfico será exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto pelo período selecionado.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Escolha uma métrica no portal do Azure":::

### <a name="add-filters-to-metrics"></a>Adicionar filtros a métricas

Você também pode filtrar as métricas e os gráficos exibidos por um **CollectionName**, **DatabaseName**, **OperationType**, **Region** e **StatusCode** específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **OperationType** e selecione um valor como **Consulta**. Em seguida, o grafo exibe as unidades de solicitação consumidas para a operação de consulta para o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas em log, de modo que não estão disponíveis na métrica OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Adicionar um filtro para selecionar a granularidade da métrica":::

Você pode agrupar as métricas usando a opção **Aplicar divisão**. Por exemplo, você pode agrupar as unidades de solicitação por tipo de operação e exibir o grafo para todas as operações de uma só vez, conforme mostrado na imagem a seguir:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Adicionar filtro de divisão de aplicação":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> Analisando logs

Os dados em Logs do Azure Monitor são armazenados em tabelas em que cada tabela tem o próprio conjunto de propriedades exclusivas.

Todos os logs de recursos no Azure Monitor têm os mesmos campos seguidos por campos específicos do serviço. O esquema comum é descrito em [Azure monitor esquema de log de recursos](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Para obter uma lista dos tipos de logs de recursos coletados para Azure Cosmos DB, consulte [monitoramento Azure Cosmos DB referência de dados](monitor-cosmos-db-reference.md#resource-logs).

O [Log de atividades](../azure-monitor/essentials/activity-log.md) é um logon de plataforma do Azure que fornece insights sobre eventos de nível de assinatura. Você pode exibi-lo de modo independente ou roteá-lo para os Logs do Azure Monitor, nos quais você pode fazer consultas muito mais complexas usando o Log Analytics.  

O Azure Cosmos DB armazena dados nas tabelas a seguir.

| Tabela | Descrição |
|:---|:---|
| AzureDiagnostics | Tabela comum usada por vários serviços para armazenar logs de recursos. Os logs de recursos do Azure Cosmos DB podem ser identificados com `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabela comum que armazena todos os registros do log de Atividades.

### <a name="sample-kusto-queries"></a>Consultas de exemplo do Kusto

> [!IMPORTANT]
> Quando você seleciona **logs** no menu Azure Cosmos DB, log Analytics é aberto com o escopo de consulta definido para a conta de Azure Cosmos DB atual. Isso significa que as consultas de log incluirão apenas os dados desse recurso. Se você quiser executar uma consulta que inclui dados de outras contas ou dados de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](../azure-monitor/logs/scope.md) para obter detalhes.

Aqui estão algumas consultas que você pode inserir na barra de pesquisa da **pesquisa de logs** para ajudá-lo a monitorar seus recursos de Cosmos do Azure. Essas consultas funcionam com a [nova linguagem](../azure-monitor/logs/log-query-overview.md).

* Para consultar todos os logs de diagnóstico do Azure Cosmos DB para um período especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Para consultar todas as operações, agrupadas por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consultar toda a atividade do usuário, agrupada por recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam você proativamente quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas no seu sistema antes que os clientes os percebam. Você pode definir alertas em [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [logs](../azure-monitor/alerts/alerts-unified-log.md) e [log de atividades](../azure-monitor/alerts/activity-log-alerts.md). Tipos diferentes de alertas têm benefícios e desvantagens

Por exemplo, a tabela a seguir lista algumas regras de alerta para seus recursos. Você pode encontrar uma lista detalhada das regras de alerta do portal do Azure. Para saber mais, consulte [o artigo como configurar alertas](create-alerts.md) .  

| Tipo de alerta | Condição | Descrição  |
|:---|:---|:---|
|Limitação de taxa em unidades de solicitação (alerta de métrica) |Nome da dimensão: StatusCode, operador: Equals, valores de dimensão: 429  | Alerta se o contêiner ou um banco de dados excedeu o limite de taxa de transferência provisionado. |
|Falha na região |Operador: maior que, tipo de agregação: contagem, valor do limite: 1 | Quando uma única região faz failover. Esse alerta será útil se você não tiver habilitado o failover automático. |
| Teclas de rotação (alerta do log de atividades)| Nível do evento: informativo, status: iniciado| Alerta quando as chaves de conta são giradas. Você pode atualizar seu aplicativo com as novas chaves. |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Monitorar Azure Cosmos DB programaticamente

As métricas no nível da conta disponíveis no portal, como o uso de armazenamento da conta e o total de solicitações, não estão disponíveis por meio das APIs do SQL. No entanto, você pode recuperar os dados de uso no nível da coleção usando as APIs do SQL. Para recuperar os dados do nível de coleção, faça o seguinte:

* Para usar a API REST, [execute um GET na coleção](/rest/api/cosmos-db/get-a-collection). As informações de cota e de uso da coleção são retornadas nos cabeçalhos x-ms-resource-quota e x-ms-resource-usage na resposta.

* Para usar o SDK do .NET, use o método [DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync), que retorna uma [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) que contém diversas propriedades de uso como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e muito mais.

Para acessar outras métricas, use o [SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). As definições de métricas disponíveis podem ser recuperadas chamando:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Para recuperar métricas individuais, use o seguinte formato:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Para saber mais, confira o artigo da [API REST de monitoramento do Azure](../azure-monitor/essentials/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Próximas etapas

* Confira [Referência de dados de monitoramento do Azure Cosmos DB](monitor-cosmos-db-reference.md) para obter uma referência dos logs e métricas criados pelo Azure Cosmos DB.
* Confira [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
