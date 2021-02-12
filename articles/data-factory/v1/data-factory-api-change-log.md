---
title: Data Factory-log de alterações da API .NET
description: Descreve as alterações significativas, adições de recursos, correções de bugs e assim por diante, em uma versão específica da API do .NET para o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 70df35409d1c84efb996bb40f4e39bde6ad7d5a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496493"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - Log de alterações da API .NET
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

Este artigo fornece informações sobre alterações no SDK do Azure Data Factory em uma versão específica. Você pode encontrar o pacote NuGet mais recente do Azure Data Factory [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Versão 4.11.0
Adições de Recursos:

* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Versão 4.10.0
* As propriedades opcionais a seguir foram adicionadas a TextFormat:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Adicionar a propriedade [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) a AzureMLBatchExecutionActivity
  * Habilitar passando várias entradas do serviço Web para uma experiência do Azure Machine Learning

## <a name="version-491"></a>Versão 4.9.1
### <a name="bug-fix"></a>Correção de bug
* Substitua a autenticação baseada na WebApi pelo [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice).

## <a name="version-490"></a>Versão 4.9.0
### <a name="feature-additions"></a>Adições de recursos
* Adicione as propriedades [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) e [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) a CopyActivity. Consulte [Cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre o recurso.

### <a name="bug-fix"></a>Correção de bug
* Introduza uma sobrecarga do método [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions), que utiliza uma instância [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters).
* Marque [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) e [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) como opcionais no CopySink.

## <a name="version-480"></a>Versão 4.8.0
### <a name="feature-additions"></a>Adições de recursos
* As propriedades opcionais a seguir foram adicionadas ao tipo Atividade de cópia para permitir o ajuste de desempenho de cópia:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Versão 4.7.0
### <a name="feature-additions"></a>Adições de recursos
* Adição do novo tipo StorageFormat [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) para copiar os arquivos no formato ORC (colunas com linhas otimizadas).
* Adicione as propriedades [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) e PolyBaseSettings a SqlDWSink.
  * Permite o uso do polybase para copiar dados para o Azure Synapse Analytics.

## <a name="version-461"></a>Versão 4.6.1
### <a name="bug-fixes"></a>Correções de bugs
* Corrige a solicitação HTTP para listar janelas de atividade.
  * Remove o nome do grupo de recursos e o nome do data factory da carga da solicitação.

## <a name="version-460"></a>Versão 4.6.0
### <a name="feature-additions"></a>Adições de recursos
* As propriedades a seguir foram adicionadas a [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties):
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Conjunto de dados](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* As propriedades a seguir foram adicionadas a [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo):
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Adição de um novo tipo [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) do tipo [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) para definir os conjuntos de dados cujos dados estão no formato JSON.

## <a name="version-450"></a>Versão 4.5.0
### <a name="feature-additions"></a>Adições de recursos
* Adição da [lista de operações à janela de atividade](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Métodos adicionados para recuperar as janelas de atividade com filtros baseados nos tipos de entidade (ou seja, fábricas de dados, conjuntos de dados, pipelines e atividades).
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Versão 4.4.0
### <a name="feature-additions"></a>Adições de recursos
* O seguinte tipo de serviço vinculado foi adicionado como fontes de dados e coletores para atividades de cópia:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Consulte [Serviço Vinculado de SAS de Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações conceituais e exemplos.

## <a name="version-430"></a>Versão 4.3.0
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados como fontes de dados para atividades de cópia:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Consulte [Mover dados do HDFS usando o Data Factory](data-factory-hdfs-connector.md) para obter informações conceituais e exemplos.
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Consulte [Mover dados dos armazenamentos de dados ODBC usando o Azure Data Factory](data-factory-odbc-connector.md) para obter informações conceituais e exemplos.

## <a name="version-420"></a>Versão 4.2.0
### <a name="feature-additions"></a>Adições de recursos
* O novo tipo de atividade a seguir foi adicionado: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Para obter detalhes sobre a atividade, consulte [Atualização dos modelos do ML do Azure usando a Atividade do Recurso de Atualização](data-factory-azure-ml-batch-execution-activity.md).
* Uma nova propriedade opcional [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) foi adicionada à [classe AzureMLLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice).
* As propriedades [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) e [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) foram adicionadas à classe [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient).
* Permitir a configuração de tempos de limite para chamadas de cliente para o serviço de dados de fábrica.

## <a name="version-410"></a>Versão 4.1.0
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Os seguintes tipos de atividades foram adicionados:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Os seguintes tipos de fonte e coletor de atividade para Atividade de Cópia foram adicionados:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Versão 4.0.1
### <a name="breaking-changes"></a>Alterações de quebra
As seguintes classes foram renomeadas. Os novos nomes eram os nomes das classes originais antes da versão 4.0.0.

| Nome no 4.0.0 | Nome no 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Versão 4.0.0
### <a name="breaking-changes"></a>Alterações de quebra
* As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Novo nome |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabela |[Conjunto de dados](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* Os métodos **List** agora retornam resultados paginados. Se a resposta contiver uma propriedade não vazia **NextLink** , o aplicativo cliente precisa continuar buscando a próxima página até que todas as páginas sejam retornadas.  Veja um exemplo:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** retorna apenas o resumo de um pipeline, em vez de todos os detalhes. Por exemplo, as atividades em um resumo de pipeline contém apenas nome e tipo.

### <a name="feature-additions"></a>Adições de recursos
* A classe [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) dá suporte a duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para dar suporte à cópia idempotente para a análise de Synapse do Azure Azure. Consulte o artigo [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) para obter detalhes sobre essas propriedades.
* Agora, damos suporte à execução de procedimento armazenado em relação ao banco de dados SQL do Azure e às fontes do Azure Synapse Analytics como parte da atividade de cópia. As classes [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) e [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Consulte os artigos do [banco de dados SQL do Azure](data-factory-azure-sql-connector.md#sqlsource) e [do Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) em Azure.com para obter detalhes sobre essas propriedades.