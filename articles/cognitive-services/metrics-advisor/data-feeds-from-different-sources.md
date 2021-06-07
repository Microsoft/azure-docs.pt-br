---
title: Como adicionar feeds de dados de fontes diferentes ao orientador de métricas
titleSuffix: Azure Cognitive Services
description: Adicionar feeds de dados diferentes ao orientador de métricas
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: 4fd01256d94fbcb18fe8437be00c84e49d98f7d0
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606140"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Adicionar feeds de dados de diferentes fontes de dados ao orientador de métricas

Use este artigo para encontrar as configurações e os requisitos para conectar diferentes tipos de fontes de dados ao orientador de métricas. Certifique-se de ler como integrar [seus dados](how-tos/onboard-your-data.md) para saber mais sobre os principais conceitos para usar seus dados com o assistente de métricas. \

## <a name="supported-authentication-types"></a>Tipos de autenticação com suporte

| Tipos de autenticação | Descrição |
| ---------------------|-------------|
|**Basic** | Você precisará ser capaz de fornecer parâmetros básicos para acessar fontes de dados. Por exemplo, uma cadeia de conexão ou chave. Os administradores de feed de dados podem exibir essas credenciais. |
| **AzureManagedIdentity** | [Identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md) para recursos do Azure é um recurso do Azure Active Directory. Ele fornece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar a identidade para autenticar em qualquer serviço que dê suporte à autenticação do Azure AD.|
| **AzureSQLConnectionString**| Armazene sua cadeia de conexão do AzureSQL como uma **entidade de credencial** no Orientador de métricas e use-a diretamente sempre que estiver integrando dados de métricas. Somente os administradores da entidade de credenciais são capazes de exibir essas credenciais, mas permitem que os visualizadores autorizados criem feeds de dados sem precisar saber detalhes das credenciais. |
| **DataLakeGen2SharedKey**| Armazene sua chave de conta do data Lake como uma **entidade de credencial** no Orientador de métricas e use-a diretamente sempre que estiver integrando dados de métricas. Somente os administradores da entidade de credenciais são capazes de exibir essas credenciais, mas permitem que os visualizadores autorizados criem feed de dados sem precisar saber os detalhes da credencial.|
| **Entidade de serviço**| Armazene sua entidade de serviço como uma **entidade de credencial** no Orientador de métricas e use-a diretamente sempre que estiver integrando dados de métricas. Somente administradores da entidade de credenciais são capazes de exibir as credenciais, mas permitem que os visualizadores autorizados criem feed de dados sem precisar saber os detalhes da credencial.|
| **Entidade de serviço do Key Vault**|Armazene sua entidade de serviço em um cofre de chaves como uma **entidade de credencial** no assistente de métricas e use-a diretamente a cada vez ao integrar dados de métricas. Somente os administradores de uma **entidade de credenciais** são capazes de exibir as credenciais, mas também deixam os visualizadores capazes de criar feeds de dados sem precisar saber as credenciais detalhadas. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Fontes de dados com suporte e tipos de autenticação correspondentes


| Fontes de dados | Tipos de autenticação |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Basic |
|[**Armazenamento de BLOBs do Azure (JSON)**](#blob) | Basic<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer (Kusto)**](#kusto) | Basic<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>Entidade de serviço<br>Entidade de serviço do Key Vault<br> |
|[**Banco de dados SQL/SQL Server do Azure**](#sql) | Basic<br>ManagedIdentity<br>Entidade de serviço<br>Entidade de serviço do Key Vault<br>AzureSQLConnectionString
|[**Armazenamento de tabelas do Azure**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**Solicitação http**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

Crie uma entidade de credencial * * e use-a para autenticação em suas fontes de dados. As seções a seguir especificam os parâmetros necessários para a autenticação *básica* . 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **ID do aplicativo**: é usado para identificar esse aplicativo ao usar a API Application insights. Para obter o ID do Aplicativo, faça o seguinte:

    1. No recurso do Application Insights, clique em Acesso à API.

    2. Copie a ID do aplicativo gerada no campo **ID do aplicativo** no assistente de métricas. 
    
    Consulte a [documentação do serviço de bot do Azure](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) para obter mais informações.

* **Chave de API**: as chaves de API são usadas por aplicativos fora do navegador para acessar esse recurso. Para obter a chave da API, faça o seguinte:

    1. No recurso do Application Insights, clique em Acesso à API.

    2. Clique em Criar Chave de API.

    3. Insira uma breve descrição, marque a opção ler telemetria e clique no botão gerar chave.

    4. Copie a chave de API para o campo de **chave de API** no assistente de métricas.

* **Consulta**: os logs do insights do aplicativo Azure são criados no Azure Data Explorer e Azure monitor consultas de log usam uma versão da mesma linguagem de consulta Kusto. A [documentação da linguagem de consulta do Kusto](/azure/data-explorer/kusto/query/) tem todos os detalhes para o idioma e deve ser o recurso principal para escrever uma consulta em relação à Application insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Armazenamento de BLOBs do Azure (JSON)</span>

* **Cadeia de conexão**: consulte o artigo [cadeia de conexão](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) do armazenamento de BLOBs do Azure para obter informações sobre como recuperar essa cadeia de caracteres.

* **Contêiner**: o Orientador de métricas espera dados de série temporal armazenados como arquivos de BLOB (um blob por carimbo de data/hora) em um único contêiner. Este é o campo nome do contêiner.

* **Modelo de blob**: esse é o modelo dos nomes de arquivo de BLOB. Por exemplo: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. Os seguintes parâmetros são compatíveis:
  * `%Y` é o ano formatado como `yyyy`
  * `%m` é o mês formatado como `MM`
  * `%d` é o dia formatado como `dd`
  * `%h` é a hora formatada como `HH`
  * `%M` é o minuto formatado como `mm`

* **Versão de formato JSON**: define o esquema de dados nos arquivos JSON. Atualmente o Orientador de métricas dá suporte a duas versões:
  
  * V1 (valor padrão)

      Somente o *nome* e o *valor* das métricas são aceitos. Por exemplo:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      As *dimensões* e o *carimbo de data/hora* das métricas também são aceitos. Por exemplo:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

Somente um carimbo de data/hora é permitido por arquivo JSON. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Cadeia de conexão**: a cadeia de conexão para acessar seu Azure Cosmos DB. Isso pode ser encontrado no recurso Cosmos DB, em **chaves**. 
* **Banco de dados**: o banco de dados para consulta. Isso pode ser encontrado na página **procurar** na seção **contêineres** .
* **ID da coleção**: a ID da coleção a ser consultada. Isso pode ser encontrado na página **procurar** na seção **contêineres** .
* **Consulta SQL**: uma consulta SQL para obter e formular dados em dados de série temporal multidimensionais. Você pode usar as `@StartTime` `@EndTime` variáveis e em sua consulta. Eles devem ser formatados: `yyyy-MM-dd HH:mm:ss` .

    Consulta de exemplo:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Exemplo de consulta para uma fatia de dados de 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Cadeia de conexão**: o assistente de métricas dá suporte ao acesso ao Azure data Explorer (Kusto) usando a autenticação de aplicativo do Azure AD. Você precisará criar e registrar um aplicativo do Azure AD e, em seguida, autorizá-lo a acessar um banco de dados Data Explorer do Azure. Para obter a cadeia de conexão, consulte a documentação de [Data Explorer do Azure](/azure/data-explorer/provision-azure-ad-app) .

* **Consulta**: consulte [linguagem de consulta do Kusto](/azure/data-explorer/kusto/query) para obter e formular dados em dados de série temporal multidimensionais. Você pode usar as `@StartTime` `@EndTime` variáveis e em sua consulta. Eles devem ser formatados: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Nome da conta**: o nome da conta do seu Azure data Lake Storage Gen2. Isso pode ser encontrado em seu recurso de conta de armazenamento do Azure (Azure Data Lake Storage Gen2) em **chaves de acesso**.

* **Chave de conta**: especifique o nome da conta para acessar seu Azure data Lake Storage Gen2. Isso pode ser encontrado no recurso de conta de armazenamento do Azure (Azure Data Lake Storage Gen2) na configuração de **chaves de acesso** .

* **Nome do sistema de arquivos (contêiner)**: o assistente de métricas esperará que os dados de série temporal sejam armazenados como arquivos de BLOB (um blob por carimbo de data/hora) em um único contêiner. Este é o campo nome do contêiner. Isso pode ser encontrado em sua instância de conta de armazenamento do Azure (Azure Data Lake Storage Gen2) e clicar em ' contêineres ' na seção ' serviço BLOB '.

* **Modelo de diretório**: esse é o modelo de diretório do arquivo de BLOB. Por exemplo: */%Y/%m/%d*. Os seguintes parâmetros são compatíveis:
  * `%Y` é o ano formatado como `yyyy`
  * `%m` é o mês formatado como `MM`
  * `%d` é o dia formatado como `dd`
  * `%h` é a hora formatada como `HH`
  * `%M` é o minuto formatado como `mm`

* **Modelo de arquivo**: esse é o modelo de arquivo do arquivo de BLOB. Por exemplo: *X_% Y-% m-% d-% h-% M.jsem*. Os seguintes parâmetros são compatíveis:
  * `%Y` é o ano formatado como `yyyy`
  * `%m` é o mês formatado como `MM`
  * `%d` é o dia formatado como `dd`
  * `%h` é a hora formatada como `HH`
  * `%M` é o minuto formatado como `mm`

Atualmente, o assistente de métricas dá suporte ao esquema de dados nos arquivos JSON da seguinte maneira. Por exemplo:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Banco de dados SQL do Azure | SQL Server</span>

* **Cadeia de conexão**: o assistente de métricas aceita uma [cadeia de conexão de estilo ADO.net](/dotnet/framework/data/adonet/connection-string-syntax) para a fonte de dados do SQL Server.

    Exemplo de cadeia de conexão:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Consulta**: uma consulta SQL para obter e formular dados em dados de série temporal multidimensionais. Você pode usar uma `@StartTime` variável em sua consulta para ajudar a obter o valor de métricas esperado.

  * `@StartTime`: um DateTime no formato de `yyyy-MM-dd HH:mm:ss`

    Consulta de exemplo:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Consulta real executada para a fatia de dados de 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Armazenamento de Tabelas do Azure</span>

* **Cadeia de conexão**: Crie uma URL de SAS (assinatura de acesso compartilhado) e preencha aqui. A maneira mais direta de gerar uma URL SAS é usando o portal do Azure. Usando o portal do Azure, você pode navegar graficamente. Para criar uma URL SAS por meio do portal do Azure, primeiro, navegue até a conta de armazenamento que você deseja acessar na seção Configurações e clique em assinatura de acesso compartilhado. Marque pelo menos as caixas de seleção "tabela" e "objeto" e clique no botão gerar SAS e cadeia de conexão. A URL da SAS do serviço tabela é o que você precisa para copiar e preencher a caixa de texto no espaço de trabalho do assistente de métricas.

* **Nome da tabela**: especifique uma tabela a ser consultada. Isso pode ser encontrado em sua instância de conta de armazenamento do Azure. Clique em **tabelas** na seção **serviço tabela** .

* **Consulta** do Você pode usar o `@StartTime` em sua consulta. `@StartTime` é substituído por uma cadeia de caracteres de formato AAAA-MM-ddTHH: mm: SS no script. Dica: Use o Gerenciador de armazenamento do Azure para criar uma consulta com um intervalo de tempo específico e verifique se ele é executado corretamente e, em seguida, faça a substituição.

    ``` mssql
    date ge datetime'@StartTime' and date lt datetime'@EndTime'
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Host**: especifique o host mestre do cluster Elasticsearch.
* **Porta**: Especifique a porta mestra do cluster Elasticsearch.
* **Cabeçalho de autorização**: especifique o valor do cabeçalho de autorização do cluster Elasticsearch.
* **Consulta**: Especifique a consulta para obter dados. Há suporte para espaço reservado @StartTime . ( por exemplo, quando os dados de 2020-06-21T00:00:00Z são ingeridos, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">Solicitação HTTP</span>

* **URL da solicitação**: uma URL http que pode retornar um JSON. Há suporte para os espaços reservados% Y,% m,% d,% h,% M:% Y = ano no formato aaaa,% m = mês no formato MM,% d = dia no formato DD,% h = hora no formato HH,% M = minuto no formato mm. Por exemplo: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Solicitar método http**: use Get ou post.
* **Cabeçalho da solicitação**: é possível adicionar a autenticação básica. 
* **Carga da solicitação**: somente a carga JSON tem suporte. Há suporte para espaço reservado @StartTime na carga. A resposta deve estar no seguinte formato JSON: [{"timestamp": "2018-01-01T00:00:00Z", "mercado": "en-US", "Count": 11, "receita": 1,23}, {"timestamp": "2018-01-01T00:00:00Z", "mercado": "zh-CN", "Count": 22, "receita": 4,56}]. (por exemplo, quando os dados de 2020-06-21T00:00:00Z são ingeridos, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Cadeia de conexão**: a cadeia de conexão para acessar o InfluxDB.
* **Banco de dados**: o banco de dados para consulta.
* **Consulta**: uma consulta para obter e formular dados em dados de série temporal multidimensionais para ingestão.
* **Nome de usuário**: isso é opcional para autenticação. 
* **Senha**: isso é opcional para autenticação. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Cadeia de conexão**: a cadeia de conexão para acessar o MongoDB.
* **Banco de dados**: o banco de dados para consulta.
* **Comando**: um comando para obter e formular dados em dados de série temporal multidimensionais para ingestão.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Cadeia de conexão**: a cadeia de conexão para acessar seu BD MySQL.
* **Consulta**: uma consulta para obter e formular dados em dados de série temporal multidimensionais para ingestão.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Cadeia de conexão**: a cadeia de conexão para acessar seu BD PostgreSQL.
* **Consulta**: uma consulta para obter e formular dados em dados de série temporal multidimensionais para ingestão.

## <a name="next-steps"></a>Próximas etapas

* Ao aguardar a ingestão dos dados de métrica no sistema, leia sobre [como gerenciar as configurações de feed de dados](how-tos/manage-data-feeds.md).
* Quando os dados de métrica são ingeridos, você pode [configurar as métricas e ajustar a configuração de detecção](how-tos/configure-metrics.md).
