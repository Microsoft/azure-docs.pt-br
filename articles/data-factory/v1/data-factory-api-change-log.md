---
title: Data Factory - Log de Alteração da API do .NET | Microsoft Docs
description: Descreve alterações significativas, novos recursos e correções de bugs etc... em uma versão específica de API do .NET para o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 863f3500c84eeab1c3dac19141cd334fc6961694
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567241"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - Log de alterações da API .NET
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

Este artigo fornece informações sobre alterações no SDK do Azure Data Factory em uma versão específica. Você pode encontrar o pacote NuGet mais recente do Azure Data Factory [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Versão 4.11.0
Adições de Recursos:

* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versão 4.10.0
* As propriedades opcionais a seguir foram adicionadas a TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adicionar a propriedade [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) a AzureMLBatchExecutionActivity
  * Habilitar passando várias entradas do serviço Web para uma experiência do Azure Machine Learning

## <a name="version-491"></a>Versão 4.9.1
### <a name="bug-fix"></a>Correção de bug
* Substitua a autenticação baseada na WebApi pelo [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versão 4.9.0
### <a name="feature-additions"></a>Adições de recursos
* Adicione as propriedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Consulte [Cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre o recurso.

### <a name="bug-fix"></a>Correção de bug
* Introduza uma sobrecarga do método [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx), que utiliza uma instância [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx).
* Marque [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcionais no CopySink.

## <a name="version-480"></a>Versão 4.8.0
### <a name="feature-additions"></a>Adições de recursos
* As propriedades opcionais a seguir foram adicionadas ao tipo Atividade de cópia para permitir o ajuste de desempenho de cópia:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versão 4.7.0
### <a name="feature-additions"></a>Adições de recursos
* Adição do novo tipo StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar os arquivos no formato ORC (colunas com linhas otimizadas).
* Adicione as propriedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings a SqlDWSink.
  * Habilita o uso do PolyBase para copiar dados para o SQL Data Warehouse.

## <a name="version-461"></a>Versão 4.6.1
### <a name="bug-fixes"></a>Correções de bug
* Corrige a solicitação HTTP para listar janelas de atividade.
  * Remove o nome do grupo de recursos e o nome do data factory da carga da solicitação.

## <a name="version-460"></a>Versão 4.6.0
### <a name="feature-additions"></a>Adições de recursos
* As propriedades a seguir foram adicionadas a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* As propriedades a seguir foram adicionadas a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Adição de um novo tipo [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) do tipo [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir os conjuntos de dados cujos dados estão no formato JSON.

## <a name="version-450"></a>Versão 4.5.0
### <a name="feature-additions"></a>Adições de recursos
* Adição da [lista de operações à janela de atividade](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Métodos adicionados para recuperar as janelas de atividade com filtros baseados nos tipos de entidade (ou seja, fábricas de dados, conjuntos de dados, pipelines e atividades).
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versão 4.4.0
### <a name="feature-additions"></a>Adições de recursos
* O seguinte tipo de serviço vinculado foi adicionado como fontes de dados e coletores para atividades de cópia:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte [Serviço Vinculado de SAS de Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações conceituais e exemplos.

## <a name="version-430"></a>Versão 4.3.0
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados como fontes de dados para atividades de cópia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consulte [Mover dados do HDFS usando o Data Factory](data-factory-hdfs-connector.md) para obter informações conceituais e exemplos.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consulte [Mover dados dos armazenamentos de dados ODBC usando o Azure Data Factory](data-factory-odbc-connector.md) para obter informações conceituais e exemplos.

## <a name="version-420"></a>Versão 4.2.0
### <a name="feature-additions"></a>Adições de recursos
* O novo tipo de atividade a seguir foi adicionado: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obter detalhes sobre a atividade, consulte [Atualização dos modelos do ML do Azure usando a Atividade do Recurso de Atualização](data-factory-azure-ml-batch-execution-activity.md).
* Uma nova propriedade opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) foi adicionada à [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* As propriedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) foram adicionadas à classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
* Permitir a configuração de tempos de limite para chamadas de cliente para o serviço de dados de fábrica.

## <a name="version-410"></a>Versão 4.1.0
### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Os seguintes tipos de atividades foram adicionados:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Os seguintes tipos de fonte e coletor de atividade para Atividade de Cópia foram adicionados:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versão 4.0.1
### <a name="breaking-changes"></a>Alterações de última hora
As seguintes classes foram renomeadas. Os novos nomes eram os nomes das classes originais antes da versão 4.0.0.

| Nome no 4.0.0 | Nome no 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versão 4.0.0
### <a name="breaking-changes"></a>Alterações de última hora
* As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Novo nome |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

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
* A classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) suporta duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para dar suporte à cópia idempotente para o Azure SQL Data Warehouse. Veja o artigo [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md) para obter detalhes sobre essas propriedades.
* Agora há suporte para executar o procedimento armazenado nas fontes do Banco de Dados SQL do Azure e Azure SQL Data Warehouse como parte da Atividade de Cópia. As classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Consulte os artigos [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#sqlsource) e [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) em Azure.com para obter detalhes sobre essas propriedades.  
