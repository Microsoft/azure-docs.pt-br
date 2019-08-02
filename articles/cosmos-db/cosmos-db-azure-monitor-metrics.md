---
title: Obter métricas do Azure Cosmos DB no Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276789"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorar e depurar as métricas do Azure Cosmos DB do Azure Monitor

Você pode exibir as métricas do Azure Cosmos DB da API do Azure Monitor. O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo o portal do Azure, acessando-os por meio da API REST ou consultá-las usando o PowerShell ou CLI. Métricas do Azure Cosmos DB são valores numéricos de baixa latência, coletados na frequência de um minuto, por padrão, você também pode agregar essas métricas. Essas métricas são capazes de dar suporte a cenários em tempo real.  

Este artigo descreve diferentes métricas do Azure Cosmos DB que você pode exibir do Monitor do Azure usando o portal do Azure. Se você estiver interessado em comum casos de uso e como as métricas do Azure Cosmos DB re é usado para analisar e depurar esses problemas ver [monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md) artigo. Você usará uma das suas contas do Cosmos do Azure existentes e exibir as métricas diferentes no banco de dados, contêiner, região, solicitação ou níveis de operação. Portanto, verifique se você tiver uma conta do Azure Cosmos com dados de exemplo e executar operações CRUD em que os dados.

## <a name="view-metrics-from-azure-portal"></a>Exibir métricas do portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda da barra e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Dos **métricas** painel > **selecionar um recurso** > escolha necessários **assinatura**, e **grupo de recursos**. Para o **tipo de recurso**, selecione **contas do Azure Cosmos DB**, escolha uma das suas contas do Cosmos do Azure existentes e selecione **aplicar**. 

   ![Escolha uma conta do Cosmos DB para exibir as métricas](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Em seguida, você pode selecionar uma métrica da lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outras pessoas. Para saber mais detalhes sobre todas as métricas disponíveis nessa lista, consulte a [métricas por categoria](#metrics-by-category) seção deste artigo. Neste exemplo, selecionaremos **unidades de solicitação** e **Avg** como o valor de agregação. 

   Além desses detalhes, você também pode selecionar o **intervalo de tempo** e **granularidade de tempo** das métricas. No máximo, você pode exibir as métricas dos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base em seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto para o período selecionado.  

   ![Escolha uma métrica do portal do Azure](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Adicionar filtros às métricas

Você também pode filtrar as métricas e o gráfico exibido por um determinado **CollectionName**, **DatabaseName**, **OperationType**, **região**, e **StatusCode**. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **OperationType** e selecione um valor como **consulta**. O gráfico, em seguida, exibe as unidades de solicitação consumidas para a operação de consulta para o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas para que eles não estão disponíveis sob a métrica de OperationType.

![Adicionar um filtro para selecionar a granularidade de métrica](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Você pode agrupar as métricas usando o **aplicam-se a divisão** opção. Por exemplo, você pode agrupar as unidades de solicitação por tipo de operação e exibir o gráfico de todas as operações de uma vez conforme mostrado na imagem a seguir: 

![Adicionar Aplicar filtro de divisão](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Métricas por categoria

### <a name="request-metrics"></a>Métricas de solicitação
            
|Métrica (nome de exibição da métrica)|Unidade (tipo de agregação) |DESCRIÇÃO|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Total de solicitações) | Contagem (contagem) | Número de solicitações feitas| DatabaseName, CollectionName, Region, StatusCode| Todos | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erro interno do servidor, Serviço Não Disponível, Solicitações Limitadas, Média de Solicitações por Segundo | Usada para monitorar solicitações por código de status, a coleção em uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60. |
| MetadataRequests (solicitações de metadados) |Contagem (contagem) | Contagem de solicitações de metadados. O Azure Cosmos DB mantém a coleção de metadados do sistema para cada conta, que permite que você enumere coleções, bancos de dados, etc., e suas configurações, gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Todos| |Usada para monitorar os limitadores devido a solicitações de metadados.|
| MongoRequests (solicitações do Mongo) | Contagem (contagem) | Número de Solicitações do Mongo Feitas | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todos |Taxa de Solicitação de Consulta do Mongo, Taxa de Solicitação de Atualização do Mongo, Taxa de Solicitação de Exclusão do Mongo, Taxa de Solicitação de Inserção do Mongo, Taxa de Solicitação de Contagem do Mongo| Usada para monitorar os erros de solicitação do Mongo, usos por tipo de comando. |

### <a name="request-unit-metrics"></a>Métricas de unidade de solicitação

|Métrica (nome de exibição da métrica)|Unidade (tipo de agregação)|DESCRIÇÃO|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo encargo de solicitação) | Contagem (Total) |Unidades Solicitadas do Mongo Consumidas| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todos |Encargo de Solicitação de Consulta do Mongo, Encargo de Solicitação de Atualização do Mongo, Encargo de Solicitação de Exclusão do Mongo, Encargo de Solicitação de Inserção do Mongo, Encargo de Solicitação de Contagem do Mongo| Usada para monitorar RUs de recurso do Mongo em um minuto.|
| TotalRequestUnits (unidades de solicitação Total)| Contagem (Total) | Unidades Solicitadas Consumidas| DatabaseName, CollectionName, Region, StatusCode |Todos| TotalRequestUnits| Usada para monitorar o uso de RU Total a uma granularidade de minuto. Para obter a média de RU consumida por segundo, use a agregação total no minuto e divida por 60.|
| ProvisionedThroughput (taxa de transferência provisionada)| Contagem (máximo) |Taxa de transferência provisionada na granularidade de coleção| DatabaseName, CollectionName| 5 M| | Usada para monitorar a taxa de transferência provisionada por coleção.|

### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (nome de exibição da métrica)|Unidade (tipo de agregação)|DESCRIÇÃO|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (armazenamento disponível) |Bytes (Total) | Total de armazenamento disponível relatada na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Armazenamento disponível| Usada para monitorar a capacidade de armazenamento disponível (aplicável apenas para coleções de armazenamento fixas). A granularidade mínima deve ser de 5 minutos.| 
| DataUsage (uso de dados) |Bytes (Total) |Uso de dados total relatado na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Tamanho dos dados | Usada para monitorar o uso total de dados na coleção e na região, a granularidade mínima deve ser 5 minutos.|
| IndexUsage (uso do índice) | Bytes (Total) |Uso total do índice relatado na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Tamanho do Índice| Usada para monitorar o uso total de dados na coleção e na região, a granularidade mínima deve ser 5 minutos. |
| DocumentQuota (cota do documento) | Bytes (Total) | Cota de armazenamento total relatado na granularidade de 5 minutos por região.| DatabaseName, CollectionName, Region| 5 M |Capacidade de Armazenamento| Usada para monitorar a cota total na coleção e na região, a granularidade mínima deve ser 5 minutos.|
| DocumentCount (contagem de documentos) | Contagem (Total) |Contagem total do documento são relatados na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Contagem de documentos|Usada para monitorar a contagem de documentos e na região, a granularidade mínima deve ser 5 minutos.|

### <a name="latency-metrics"></a>Métricas de latência

|Métrica (nome de exibição da métrica)|Unidade (tipo de agregação)|DESCRIÇÃO|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| ReplicationLatency (latência de replicação)| Milissegundos (mínimo, máximo, média) | Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente| SourceRegion, TargetRegion| Todos | Usada para monitorar a latência de replicação P99 entre quaisquer duas regiões para uma conta com replicação geográfica. |


### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (nome de exibição da métrica) |Unidade (tipo de agregação)|DESCRIÇÃO| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---|
| ServiceAvailability (disponibilidade de serviço)| % (Mínimo, máximo) | Disponibilidade de solicitações de conta na granularidade de uma hora| 1H | Disponibilidade do serviço | Representa a porcentagem do total passado solicitações. Uma solicitação será considerada com falha devido a erro de sistema se o código de status for 410, 500 ou 503. Usada para monitorar a disponibilidade da conta na granularidade de hora. |


### <a name="cassandra-api-metrics"></a>Métricas da API do Cassandra

|Métrica (nome de exibição da métrica)|Unidade (tipo de agregação)|DESCRIÇÃO|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra solicitações) | Contagem (contagem) | Número de solicitações da API do Cassandra feitas| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Todos| Usada para monitorar solicitações do Cassandra em uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60.|
| CassandraRequestCharges (encargos de solicitação de Cassandra) | Contagem (Sum, Min, Max, Avg) | Unidades de solicitação consumidas pelas solicitações de API do Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Todos| Usada para monitorar as RUs usadas por minuto por uma conta de API do Cassandra.|
| CassandraConnectionClosures (Cassandra fechamentos de Conexão) |Contagem (contagem) |Número de conexões do Cassandra fechadas| ClosureReason, Region| Todos | Usada para monitorar a conectividade entre os clientes e a API do Cassandra do Azure Cosmos DB.|

## <a name="next-steps"></a>Próximas etapas

* [Exibir e monitorar métricas do painel de métricas de conta do Azure Cosmos DB](use-metrics.md)

* [Log de diagnóstico no Azure Cosmos DB](logging.md)
