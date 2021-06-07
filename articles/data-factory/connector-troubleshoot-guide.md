---
title: Solucionar problemas de conectores Azure Data Factory
description: Saiba como solucionar problemas com conector no Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 9d8f940e3900c00b1c6f6623dfeff2d92ca85aa3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042421"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Solucionar problemas de conectores Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora maneiras comuns de solucionar problemas com conectores de Azure Data Factory.

## <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

### <a name="error-code-azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem**: "falha na operação de BLOB. Idcontêiner:% ContainerName;, caminho:% Path;. "

- **Causa**: um problema com a operação de armazenamento de BLOBs.

- **Recomendação**: para verificar os detalhes do erro, consulte [códigos de erro do armazenamento de BLOBs](/rest/api/storageservices/blob-service-error-codes). Para obter mais ajuda, entre em contato com a equipe de armazenamento de BLOBs.


### <a name="invalid-property-during-copy-activity"></a>Propriedade inválida durante a atividade de cópia

- **Mensagem**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Causa**: o tipo definido no conjunto de texto é inconsistente com o tipo de origem ou de coletor definido na atividade de cópia.

- **Resolução**: Edite a definição de JSON de conjunto de forma ou de pipeline para tornar os tipos consistentes e, em seguida, execute a implantação novamente.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Mensagem de erro: O tamanho da solicitação é muito grande

- **Sintomas**: quando você copia dados para Azure Cosmos DB com um tamanho de lote de gravação padrão, você recebe o seguinte erro: `Request size is too large.`

- **Causa**: Azure Cosmos DB limita o tamanho de uma única solicitação para 2 MB. A fórmula é o *tamanho da solicitação = tamanho do lote de \* gravação de um único documento*. Se o tamanho do documento for grande, o comportamento padrão resultará em um tamanho de solicitação muito grande. Você pode ajustar o tamanho do lote de gravação.

- **Resolução**: no coletor da atividade de cópia, reduza o valor de *tamanho do lote de gravação* (o valor padrão é 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Mensagem de erro: Violação de restrição de índice exclusivo

- **Sintomas**: quando você copia dados para o Azure Cosmos DB, você recebe o seguinte erro:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Causa**: Há duas causas possíveis:

    - Causa 1: se você usar **Insert** como comportamento de gravação, esse erro significa que os dados de origem têm linhas ou objetos com a mesma ID.
    - Causa 2: se você usar **Upsert** como o comportamento de gravação e definir outra chave exclusiva para o contêiner, esse erro significa que os dados de origem têm linhas ou objetos com IDs diferentes, mas o mesmo valor para a chave exclusiva definida.

- **Resolução**: 

    - Para a causa 1, defina **Upsert** como o comportamento de gravação.
    - Para a causa 2, certifique-se de que cada documento tem um valor diferente para a chave exclusiva definida.

### <a name="error-message-request-rate-is-large"></a>Mensagem de erro: A taxa de solicitação é alta

- **Sintomas**: quando você copia dados para o Azure Cosmos DB, você recebe o seguinte erro:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Causa**: o número de RUS (unidades de solicitação usadas) é maior que o RUS disponível configurado no Azure Cosmos DB. Para saber como Azure Cosmos DB calcula RUs, consulte [unidades de solicitação no Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Resolução**: Tente uma das duas soluções a seguir:

    - Aumente o número de *RUs do contêiner* para um valor maior em Azure Cosmos DB. Essa solução melhorará o desempenho da atividade de cópia, mas incorrerá em mais custo em Azure Cosmos DB. 
    - Diminua o *writeBatchSize* para um valor menor, como 1000, e diminua o *parallelCopies* para um valor menor, como 1. Essa solução reduzirá o desempenho da execução de cópia, mas não incorrerá em mais custo em Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Colunas ausentes no mapeamento de coluna

- **Sintomas**: quando você importa um esquema para Azure Cosmos DB para mapeamento de coluna, algumas colunas estão ausentes. 

- **Causa**: data Factory infere o esquema dos primeiros 10 documentos Azure Cosmos DB. Se algumas colunas de documento ou propriedades não contiverem valores, o esquema não será detectado pelo Data Factory e, consequentemente, não será exibido.

- **Resolução**: você pode ajustar a consulta, conforme mostrado no código a seguir, para forçar os valores de coluna a serem exibidos no conjunto de resultados com valores vazios. Suponha que a coluna *impossível* esteja ausente nos 10 primeiros documentos). Como alternativa, você pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Mensagem de erro: O GuidRepresentation para o leitor é CSharpLegacy

- **Sintomas**: quando você copia dados de Azure Cosmos DB MongoAPI ou MongoDB com o campo identificador universalmente exclusivo (UUID), você recebe o seguinte erro:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Causa**: há duas maneiras de representar o UUID em JSON binário (BSON): UuidStardard e UuidLegacy. Por padrão, UuidLegacy é usado para ler dados. Você receberá um erro se os dados do UUID no MongoDB forem UuidStandard.

- **Resolução**: na cadeia de conexão do MongoDB, adicione a opção *uuidRepresentation = Standard* . Para obter mais informações, consulte [cadeia de conexão do MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (API do SQL)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Código de erro: CosmosDbSqlApiOperationFailed

- **Mensagem**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Causa**: um problema com a operação CosmosDbSqlApi.

- **Recomendação**: para verificar os detalhes do erro, consulte [Azure Cosmos DB documento de ajuda](../cosmos-db/troubleshoot-dot-net-sdk.md). Para obter mais ajuda, contate a equipe de Azure Cosmos DB.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Mensagem de erro: a conexão subjacente foi fechada: não foi possível estabelecer uma relação de confiança para o canal seguro de SSL/TLS.

- **Sintomas**: a atividade de cópia falha com o seguinte erro: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Causa**: falha na validação do certificado durante o HANDSHAKE de TLS.

- **Resolução**: como solução alternativa, use a cópia preparada para ignorar a validação de TLS (Transport Layer Security) para Azure data Lake Storage Gen1. Você precisa reproduzir esse problema e reunir o rastreamento do monitor de rede (Netmon) e, em seguida, envolver sua equipe de rede para verificar a configuração de rede local.

    ![Diagrama de conexões Azure Data Lake Storage Gen1 para solucionar problemas.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Mensagem de erro: O servidor remoto retornou um erro: (403) Forbidden

- **Sintomas**: Falha na atividade de cópia com o seguinte erro: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Causa**: uma possível causa é que a entidade de serviço ou a identidade gerenciada que você usa não tem permissão para acessar determinadas pastas ou arquivos.

- **Resolução**: conceda as permissões apropriadas a todas as pastas e subpastas que você precisa copiar. Para obter mais informações, consulte [copiar dados de ou para Azure data Lake Storage Gen1 usando Azure data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Mensagem de erro: Falha ao obter o token de acesso usando a entidade de serviço. Erro de ADAL: service_unavailable

- **Sintomas**: a atividade de cópia falha com o seguinte erro:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Causa**: quando o servidor de token de serviço (STS) pertencente ao Azure Active Directory não está disponível, isso significa que está muito ocupado para lidar com as solicitações e retorna o erro HTTP 503. 

- **Resolução:** Execute a atividade de cópia novamente após alguns minutos.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Código de erro: ADLSGen2OperationFailed

- **Mensagem**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causas e recomendações**: causas diferentes podem levar a esse erro. Marque a lista abaixo para obter a possível análise de causa e a recomendação relacionada.

  | Análise de causa                                               | Recomendação                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Se Azure Data Lake Storage Gen2 gera um erro indicando que uma operação falhou.| Verifique a mensagem de erro detalhada lançada por Azure Data Lake Storage Gen2. Se o erro for uma falha transitória, repita a operação. Para obter mais ajuda, entre em contato com o suporte do armazenamento do Azure e forneça a ID da solicitação na mensagem de erro. |
  | Se a mensagem de erro contiver a cadeia de caracteres "proibido", a entidade de serviço ou a identidade gerenciada que você usar poderá não ter permissão suficiente para acessar Azure Data Lake Storage Gen2. | Para solucionar esse erro, consulte [copiar e transformar dados em Azure data Lake Storage Gen2 usando Azure data Factory](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Se a mensagem de erro contiver a cadeia de caracteres "InternalServerError", o erro será retornado por Azure Data Lake Storage Gen2. | O erro pode ser causado por uma falha transitória. Nesse caso, repita a operação. Se o problema persistir, entre em contato com o suporte do armazenamento do Azure e forneça a ID da solicitação da mensagem de erro. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>A solicitação para Azure Data Lake Storage Gen2 conta causou um erro de tempo limite

- **Mensagem**: 
  * Código de erro = `UserErrorFailedBlobFSOperation`
  * Mensagem de erro = `BlobFS operation failed for: A task was canceled.`

- **Causa**: o problema é causado pelo erro de tempo limite do coletor de Azure data Lake Storage Gen2, que geralmente ocorre na máquina de Integration Runtime (ir) de hospedagem interna.

- **Recomendação**: 

    - Coloque sua máquina IR auto-hospedada e direcione Azure Data Lake Storage Gen2 conta na mesma região, se possível. Isso pode ajudar a evitar um erro de tempo limite aleatório e produzir um melhor desempenho.

    - Verifique se há uma configuração de rede especial, como o ExpressRoute, e certifique-se de que a rede tenha largura de banda suficiente. Sugerimos que você reduza a configuração de trabalhos simultâneos de IR via hospedagem interna quando a largura de banda geral estiver baixa. Isso pode ajudar a evitar a competição de recursos de rede em vários trabalhos simultâneos.

    - Se o tamanho do arquivo for moderado ou pequeno, use um tamanho de bloco menor para cópia não binária para atenuar esse erro de tempo limite. Para obter mais informações, consulte [BLOB Storage Put Block](/rest/api/storageservices/put-block).

       Para especificar o tamanho do bloco personalizado, edite a propriedade no editor de arquivo JSON, conforme mostrado aqui:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Armazenamento de arquivos do Azure

### <a name="error-code-azurefileoperationfailed"></a>Código de erro: AzureFileOperationFailed

- **Mensagem**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Causa**: um problema com a operação de armazenamento de arquivos do Azure.

- **Recomendação**: para verificar os detalhes do erro, consulte a [ajuda dos arquivos do Azure](/rest/api/storageservices/file-service-error-codes). Para obter mais ajuda, contate a equipe de arquivos do Azure.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, banco de dados SQL do Azure e SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Causas e recomendações**: causas diferentes podem levar a esse erro. Marque a lista abaixo para obter a possível análise de causa e a recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Para o SQL do Azure, se a mensagem de erro contiver a cadeia de caracteres "SqlErrorNumber = 47073", significa que o acesso à rede pública é negado na configuração de conectividade. | No firewall do SQL do Azure, defina a opção **negar acesso à rede pública** como *não*. Para obter mais informações, consulte [configurações de conectividade do SQL do Azure](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | Para o SQL do Azure, se a mensagem de erro contiver um código de erro SQL como "SqlErrorNumber = [ErrorCode]", consulte o guia de solução de problemas do SQL do Azure. | Para obter uma recomendação, consulte [solucionar problemas de conectividade e outros erros com o banco de dados SQL do Azure e o Azure sql instância gerenciada](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Verifique se a porta 1433 está na lista de permissões do firewall. | Para obter mais informações, consulte [portas usadas pelo SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Se a mensagem de erro contiver a cadeia de caracteres "SqlException", o banco de dados SQL indicará que uma operação específica falhou. | Para obter mais informações, pesquise por código de erro do SQL em [erros do mecanismo de banco de dados](/sql/relational-databases/errors-events/database-engine-events-and-errors). Para obter mais ajuda, entre em contato com o suporte do SQL do Azure. |
    | Se esse for um problema transitório (por exemplo, uma conexão de rede inestável), adicione nova tentativa na política de atividade para mitigar. | Para saber mais, confira [Pipelines e atividades no Azure Data Factory](./concepts-pipelines-activities.md#activity-policy). |
    | Se a mensagem de erro contiver a cadeia de caracteres "cliente com endereço IP"... " Não tem permissão para acessar o servidor ", e você está tentando se conectar ao banco de dados SQL do Azure, o erro geralmente é causado por um problema de firewall do banco de dados SQL do Azure. | Na configuração do firewall do SQL Server do Azure, habilite a opção **permitir que os serviços e recursos do Azure acessem este servidor** . Para obter mais informações, consulte [Azure SQL Database e regras de firewall de IP do Azure Synapse](../azure-sql/database/firewall-configure.md). |
    
### <a name="error-code-sqloperationfailed"></a>Código de erro: SqlOperationFailed

- **Mensagem**: `A database operation failed. Please search error to get more details.`

- **Causas e recomendações**: causas diferentes podem levar a esse erro. Marque a lista abaixo para obter a possível análise de causa e a recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Se a mensagem de erro contiver a cadeia de caracteres "SqlException", o banco de dados SQL gera um erro indicando que uma operação específica falhou. | Se o erro de SQL não estiver claro, tente alterar o banco de dados para o nível de compatibilidade mais recente ' 150 '. Ele pode gerar os erros de versão mais recentes do SQL. Para obter mais informações, confira a [documentação](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Para obter mais informações sobre como solucionar problemas de SQL, pesquise por código de erro do SQL em [erros do mecanismo de banco de dados](/sql/relational-databases/errors-events/database-engine-events-and-errors). Para obter mais ajuda, entre em contato com o suporte do SQL do Azure. |
    | Se a mensagem de erro contiver a cadeia de caracteres "PdwManagedToNativeInteropException", ela será geralmente causada por uma incompatibilidade entre os tamanhos de coluna de origem e de coletor. | Verifique o tamanho das colunas de origem e do coletor. Para obter mais ajuda, entre em contato com o suporte do SQL do Azure. |
    | Se a mensagem de erro contiver a cadeia de caracteres "InvalidOperationException", ela será geralmente causada por dados de entrada inválidos. | Para identificar qual linha encontrou o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Para obter mais informações, consulte [tolerância a falhas da atividade de cópia no Azure data Factory](./copy-activity-fault-tolerance.md). |


### <a name="error-code-sqlunauthorizedaccess"></a>Código de erro: SqlUnauthorizedAccess

- **Mensagem**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: as credenciais estão incorretas ou a conta de logon não pode acessar o banco de dados SQL.

- **Recomendação**: Verifique se a conta de logon tem permissões suficientes para acessar o banco de dados SQL.


### <a name="error-code-sqlopenconnectiontimeout"></a>Código de erro: SqlOpenConnectionTimeout

- **Mensagem**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: o problema pode ser uma falha transitória do banco de dados SQL.

- **Recomendação**: repita a operação para atualizar a cadeia de conexão de serviço vinculado com um valor de tempo limite de conexão maior.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Código de erro: SqlAutoCreateTableTypeMapFailed

- **Mensagem**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: a tabela de criação não pode atender ao requisito de origem.

- **Recomendação**: Atualize o tipo de coluna em *mapeamentos* ou crie manualmente a tabela de coletor no servidor de destino.


### <a name="error-code-sqldatatypenotsupported"></a>Código de erro: SqlDataTypeNotSupported

- **Mensagem**: `A database operation failed. Check the SQL errors.`

- **Causa**: se o problema ocorrer na origem do SQL e o erro estiver relacionado ao estouro de SqlDateTime, o valor dos dados excederá o intervalo do tipo lógico (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Recomendação**: Converta o tipo para a cadeia de caracteres na consulta SQL de origem ou, no mapeamento de coluna da atividade de cópia, altere o tipo de coluna para *cadeia de caracteres*.

- **Causa**: se o problema ocorrer no coletor SQL e o erro estiver relacionado ao estouro de SqlDateTime, o valor dos dados excederá o intervalo permitido na tabela de coletor.

- **Recomendação**: Atualize o tipo de coluna correspondente para o tipo *datetime2* na tabela de coletor.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Código de erro: SqlInvalidDbStoredProcedure

- **Mensagem**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: o procedimento armazenado especificado é inválido. A causa pode ser que o procedimento armazenado não retorna nenhum dado.

- **Recomendação**: valide o procedimento armazenado usando as ferramentas do SQL. Verifique se o procedimento armazenado pode retornar dados.


### <a name="error-code-sqlinvaliddbquerystring"></a>Código de erro: SqlInvalidDbQueryString

- **Mensagem**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: a consulta SQL especificada é inválida. A causa pode ser que a consulta não retorna nenhum dado.

- **Recomendação**: valide a consulta SQL usando ferramentas SQL. Verifique se a consulta pode retornar dados.


### <a name="error-code-sqlinvalidcolumnname"></a>Código de erro: SqlInvalidColumnName

- **Mensagem**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa**: a coluna não pode ser encontrada porque a configuração pode estar incorreta.

- **Recomendação**: Verifique a coluna na consulta, *estrutura* no conjunto de dados e *mapeamentos* na atividade.


### <a name="error-code-sqlbatchwritetimeout"></a>Código de erro: SqlBatchWriteTimeout

- **Mensagem**: `Timeouts in SQL write operation.`

- **Causa**: o problema pode ser causado por uma falha transitória do banco de dados SQL.

- **Recomendação**: repita a operação. Se o problema persistir, entre em contato com o suporte do SQL do Azure.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Código de erro: SqlBatchWriteTransactionFailed

- **Mensagem**: `SQL transaction commits failed.`

- **Causa**: se os detalhes da exceção indicarem constantemente um tempo limite de transação, a latência de rede entre o tempo de execução de integração e o banco de dados será maior que o limite padrão de 30 segundos.

- **Recomendação**: Atualize a cadeia de conexão de serviço vinculada ao SQL com um valor de *tempo limite de conexão* igual ou maior que 120 e execute a atividade novamente.

- **Causa**: se os detalhes da exceção indicarem intermitentemente que a conexão SQL foi interrompida, pode ser uma falha de rede transitória ou um problema do lado do banco de dados SQL.

- **Recomendação**: repita a atividade e examine as métricas do lado do banco de dados SQL.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Código de erro: SqlBulkCopyInvalidColumnLength

- **Mensagem**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: falha na cópia em massa do SQL porque ela recebeu um comprimento de coluna inválido do cliente bcp (utilitário de cópia em massa).

- **Recomendação**: para identificar qual linha encontrou o problema, habilite o recurso de tolerância a falhas na atividade de cópia. Isso pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Para obter mais informações, consulte [tolerância a falhas da atividade de cópia no Azure data Factory](./copy-activity-fault-tolerance.md).


### <a name="error-code-sqlconnectionisclosed"></a>Código de erro: SqlConnectionIsClosed

- **Mensagem**: `The connection is closed by SQL Database.`

- **Causa**: a conexão SQL é fechada pelo banco de dados SQL quando uma execução simultânea alta e o servidor encerram a conexão.

- **Recomendação**: repita a conexão. Se o problema persistir, entre em contato com o suporte do SQL do Azure.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Mensagem de erro: Falha na conversão de uma cadeia de caracteres em um uniqueidentifier

- **Sintomas**: quando você copia dados de uma fonte de dados tabulares (como SQL Server) para o Azure Synapse Analytics usando cópia preparada e polybase, você recebe o seguinte erro:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Causa**: o polybase do Azure Synapse Analytics não pode converter uma cadeia de caracteres vazia em um GUID.

- **Resolução**: no coletor de atividade de cópia, em configurações do polybase, defina a opção **usar tipo padrão** como *false*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: Tipo de dados esperado: DECIMAL (x,x), valor incorreto

- **Sintomas**: quando você copia dados de uma fonte de dados tabulares (como SQL Server) para o Azure Synapse Analytics usando cópia preparada e polybase, você recebe o seguinte erro:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Causa**: o polybase do Azure Synapse Analytics não pode inserir uma cadeia de caracteres vazia (valor nulo) em uma coluna decimal.

- **Resolução**: no coletor de atividade de cópia, em configurações do polybase, defina a opção **usar tipo padrão** como false.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Mensagem de erro: mensagem de exceção Java: HdfsBridge:: CreateRecordReader

- **Sintomas**: você copia dados para o Azure Synapse Analytics usando o polybase e recebe o seguinte erro:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Causa**: a causa pode ser que o esquema (largura total da coluna) é muito grande (maior que 1 MB). Verifique o esquema da tabela de destino do Azure Synapse Analytics adicionando o tamanho de todas as colunas:

    - Int = 4 bytes
    - Bigint = 8 bytes
    - Varchar (n), Char (n), binary (n), varbinary (n) = n bytes
    - Nvarchar (n), nchar (n) = n * 2 bytes
    - Data = 6 bytes
    - DateTime/(2), smalldatetime = 16 bytes
    - DateTimeOffset = 20 bytes
    - Decimal = 19 bytes
    - Float = 8 bytes
    - Dinheiro = 8 bytes
    - Smallmoney = 4 bytes
    - Real = 4 bytes
    - Smallint = 2 bytes
    - Tempo = 12 bytes
    - Tinyint = 1 byte

- **Resolução**: 
    - Reduza a largura da coluna para menos de 1 MB.
    - Ou use uma abordagem de inserção em massa desabilitando o polybase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Mensagem de erro: A condição especificada usando cabeçalhos condicionais HTTP não foi atendida

- **Sintomas**: você usa a consulta SQL para efetuar pull de dados do Azure Synapse Analytics e receber o seguinte erro:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Causa**: o Azure Synapse Analytics encontrou um problema ao consultar a tabela externa no armazenamento do Azure.

- **Resolução**: execute a mesma consulta no SQL Server Management Studio (SSMS) e verifique se você obtém o mesmo resultado. Se você fizer isso, abra um tíquete de suporte para o Azure Synapse Analytics e forneça o nome do servidor e do banco de dados do Azure Synapse Analytics.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>O nível de desempenho é baixo e leva a uma falha de cópia

- **Sintomas**: você copia dados para o Azure SQL Database e recebe o seguinte erro: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Causa**: O banco de dados SQL do Azure S1 atingiu os limites de e/s (entrada/saída).

- **Resolução**: Atualize o nível de desempenho do banco de dados SQL do Azure para corrigir o problema. 


### <a name="sql-table-cant-be-found"></a>Não foi possível encontrar a tabela SQL 

- **Sintomas**: você copia dados de híbrido para uma tabela de SQL Server local e recebe o seguinte erro:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Causa**: a conta SQL atual não tem permissões suficientes para executar solicitações emitidas pelo .net SqlBulkCopy. WriteToServer.

- **Resolução**: mude para uma conta do SQL mais privilegiada.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Mensagem de erro: a cadeia de caracteres ou os dados binários estão truncados

- **Sintomas**: ocorre um erro quando você copia dados em uma tabela SQL Server do Azure local. 

- **Causa**: a definição do esquema de tabela do CX SQL tem uma ou mais colunas com menos comprimento do que o esperado.

- **Resolução**: para resolver o problema, tente o seguinte:

    1. Para solucionar quais linhas têm o problema, aplique a [tolerância a falhas](./copy-activity-fault-tolerance.md)do coletor SQL, especialmente "redirectIncompatibleRowSettings".

        > [!NOTE]
        > A tolerância a falhas pode exigir tempo de execução adicional, o que pode levar a custos mais altos.

    2. Verifique novamente os dados redirecionados em relação ao comprimento da coluna do esquema de tabela SQL para ver quais colunas precisam ser atualizadas.

    3. Atualize o esquema da tabela de acordo.


## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Código de erro: AzureTableDuplicateColumnsFromSource

- **Mensagem**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Causa**: as colunas de origem duplicadas podem ocorrer por um dos seguintes motivos:
   * Você está usando o banco de dados como uma fonte e junções de tabela aplicadas.
   * Você tem arquivos CSV não estruturados com nomes de coluna duplicados na linha de cabeçalho.

- **Recomendação**: Verifique e corrija as colunas de origem de forma dupla, conforme necessário.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Código de erro: DB2DriverRunFailed

- **Mensagem**: `Error thrown from driver. Sql code: '%code;'`

- **Causa**: se a mensagem de erro contiver a cadeia de caracteres "SQLSTATE = 51002 SQLCODE =-805", siga a "dica" em [copiar dados do DB2 usando Azure data Factory](./connector-db2.md#linked-service-properties).

- **Recomendação**: tente definir "nullid" na `packageCollection`  propriedade.


## <a name="delimited-text-format"></a>Formato de texto delimitado

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Código de erro: DelimitedTextColumnNameNotAllowNull

- **Mensagem**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: quando ' firstRowAsHeader ' é definido na atividade, a primeira linha é usada como o nome da coluna. Esse erro significa que a primeira linha contém um valor vazio (por exemplo, ' ColumnA, coluna b ').

- **Recomendação**: Verifique a primeira linha e corrija o valor se ele estiver vazio.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Código de erro: DelimitedTextMoreColumnsThanDefined

- **Mensagem**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Causas e recomendações**: causas diferentes podem levar a esse erro. Marque a lista abaixo para obter a possível análise de causa e a recomendação relacionada.

  | Análise de causa                                               | Recomendação                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | A contagem de colunas da linha problemática é maior que a contagem de colunas da primeira linha. Pode ser causado por um problema de dados ou configurações de caractere delimitador de coluna ou aspas incorretas. | Obtenha a contagem de linhas da mensagem de erro, verifique a coluna da linha e corrija os dados. |
  | Se a contagem de colunas esperada for "1" em uma mensagem de erro, você poderá ter especificado configurações incorretas de formato ou compactação, o que fez com que Data Factory analisar os arquivos incorretamente. | Verifique as configurações de formato para verificar se elas correspondem aos seus arquivos de origem. |
  | Se sua origem for uma pasta, os arquivos na pasta especificada poderão ter um esquema diferente. | Verifique se os arquivos na pasta especificada têm um esquema idêntico. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service e Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Código de erro: DynamicsCreateServiceClientError

- **Mensagem**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Causa**: o problema é um problema transitório no lado do servidor do Dynamics.

- **Recomendação**:  Executar novamente o pipeline. Se ele falhar novamente, tente reduzir o paralelismo. Se o problema persistir, contate o suporte do Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Colunas ausentes ao importar um esquema ou Visualizar dados

- **Sintomas**: algumas colunas estão ausentes quando você importa um esquema ou dados de visualização. Mensagem de erro: `The valid structure information (column name and type) are required for Dynamics source.`

- **Causa**: esse problema é por design, porque data Factory não pode mostrar colunas que não contêm valores nos primeiros 10 registros. Verifique se as colunas que você adicionou estão no formato correto. 

- **Recomendação**: adicione manualmente as colunas na guia mapeamento.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Código de erro: DynamicsMissingTargetForMultiTargetLookupField

- **Mensagem**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Causa**: a coluna de destino não existe na origem ou no mapeamento de coluna.

- **Recomendação**:  
  1. Verifique se a origem contém a coluna de destino. 
  2. Adicione a coluna de destino no mapeamento de coluna. Verifique se a coluna do coletor está no formato *{FieldName} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Código de erro: DynamicsInvalidTargetForMultiTargetLookupField

- **Mensagem**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Causa**: um nome de entidade incorreto é fornecido como uma entidade de destino de um campo de pesquisa de vários destinos.

- **Recomendação**: forneça um nome de entidade válido para o campo de pesquisa de vários destinos.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Código de erro: DynamicsInvalidTypeForMultiTargetLookupField

- **Mensagem**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Causa**: o valor na coluna de destino não é uma cadeia de caracteres.

- **Recomendação**: forneça uma cadeia de caracteres válida na coluna de destino de pesquisa de vários destinos.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Código de erro: DynamicsFailedToRequetServer

- **Mensagem**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Causa**: o servidor do Dynamics está inestável ou inacessível ou a rede está enfrentando problemas.

- **Recomendação**: para obter mais detalhes, verifique a conectividade de rede ou verifique o log do servidor do Dynamics. Para obter mais ajuda, entre em contato com o suporte do Dynamics.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Código de erro: FtpFailedToConnectToFtpServer

- **Mensagem**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Causa**: um tipo de serviço vinculado incorreto pode ser usado para o servidor FTP, como usar o serviço vinculado de FTP seguro (SFTP) para se conectar a um servidor FTP.

- **Recomendação**: Verifique a porta do servidor de destino. O FTP usa a porta 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Código de erro: HttpFileFailedToRead

- **Mensagem**: `Failed to read data from http server. Check the error from http server：%message;`

- **Causa**: esse erro ocorre quando Azure data Factory se comunica com o servidor http, mas a operação de solicitação HTTP falha.

- **Recomendação**: Verifique o código de status HTTP na mensagem de erro e corrija o problema do servidor remoto.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Código de erro: ArgumentOutOfRangeException

- **Mensagem**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Causa**: em data Factory, os valores de DateTime têm suporte no intervalo de 0001-01-01 00:00:00 a 9999-12-31 23:59:59. No entanto, o Oracle dá suporte a um intervalo mais amplo de valores de data e hora, como o século de BC ou mín/s>59, o que leva a uma falha no Data Factory.

- **Recomendação**: 

    Para ver se o valor no Oracle está no intervalo de Data Factory, execute `select dump(<column name>)` . 

    Para saber a seqüência de bytes no resultado, consulte [como as datas são armazenadas no Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>Formato ORC

### <a name="error-code-orcjavainvocationexception"></a>Código de erro: OrcJavaInvocationException

- **Mensagem**: `An error occurred when invoking Java, message: %javaException;.`
- **Causas e recomendações**: causas diferentes podem levar a esse erro. Marque a lista abaixo para obter a possível análise de causa e a recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando a mensagem de erro contém as cadeias de caracteres "Java. lang. OutOfMemory", "espaço de heap Java" e "doubleCapacity", geralmente é um problema de gerenciamento de memória em uma versão antiga do Integration Runtime. | Se você estiver usando Integration Runtime de hospedagem interna, recomendamos que atualize para a versão mais recente. |
    | Quando a mensagem de erro contém a cadeia de caracteres "Java. lang. OutOfMemory", o Integration Runtime não tem recursos suficientes para processar os arquivos. | Limite as execuções simultâneas no Integration Runtime. Para IR por hospedagem interna, escale verticalmente para um computador avançado com memória igual ou maior que 8 GB. |
    |Quando a mensagem de erro contém a cadeia de caracteres "NullPointerReference", a causa pode ser um erro transitório. | Repita a operação. Se o problema persistir, contate o Suporte. |
    | Quando a mensagem de erro contém a cadeia de caracteres "BufferOverflowException", a causa pode ser um erro transitório. | Repita a operação. Se o problema persistir, contate o Suporte. |
    | Quando a mensagem de erro contém a cadeia de caracteres "Java. lang. ClassCastException:org. Apache. Hadoop. Hive. serde2. IO. HiveCharWritable não pode ser convertida em org. Apache. Hadoop. IO. Text", a causa pode ser um problema de conversão de tipo no tempo de execução do Java. Normalmente, isso significa que os dados de origem não podem ser tratados bem no tempo de execução do Java. | Esse é um problema de dados. Tente usar uma cadeia de caracteres em vez de char ou varchar em dados de formato ORC. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Código de erro: OrcDateTimeExceedLimit

- **Mensagem**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Causa**: se o valor de DateTime for ' 0001-01-01 00:00:00 ', ele poderá ser causado pelas diferenças entre o [calendário juliano e o calendário gregoriano](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Recomendação**: Verifique o valor de tiques e evite usar o valor de datetime ' 0001-01-01 00:00:00 '.


## <a name="parquet-format"></a>Formato Parquet

### <a name="error-code-parquetjavainvocationexception"></a>Código de erro: ParquetJavaInvocationException

- **Mensagem**: `An error occurred when invoking java, message: %javaException;.`

- **Causas e recomendações**: causas diferentes podem levar a esse erro. Marque a lista abaixo para obter a possível análise de causa e a recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando a mensagem de erro contém as cadeias de caracteres "Java. lang. OutOfMemory", "espaço de heap Java" e "doubleCapacity", geralmente é um problema de gerenciamento de memória em uma versão antiga do Integration Runtime. | Se você estiver usando o IR auto-hospedado e a versão for anterior à 3.20.7159.1, recomendamos que você atualize para a versão mais recente. |
    | Quando a mensagem de erro contém a cadeia de caracteres "Java. lang. OutOfMemory", o Integration Runtime não tem recursos suficientes para processar os arquivos. | Limite as execuções simultâneas no Integration Runtime. Para IR por hospedagem interna, escale verticalmente para um computador avançado com memória igual ou maior que 8 GB. |
    | Quando a mensagem de erro contém a cadeia de caracteres "NullPointerReference", pode ser um erro transitório. | Repita a operação. Se o problema persistir, contate o Suporte. |

### <a name="error-code-parquetinvalidfile"></a>Código de erro: ParquetInvalidFile

- **Mensagem**: `File is not a valid Parquet file.`

- **Causa**: esse é um problema de arquivo parquet.

- **Recomendação**: Verifique se a entrada é um arquivo parquet válido.


### <a name="error-code-parquetnotsupportedtype"></a>Código de erro: ParquetNotSupportedType

- **Mensagem**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: o formato parquet não tem suporte no Azure data Factory.

- **Recomendação**: Verifique novamente os dados de origem acessando os [formatos de arquivo com suporte e os codecs de compactação por atividade de cópia no Azure data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Código de erro: ParquetMissedDecimalPrecisionScale

- **Mensagem**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: a precisão e a escala do número foram analisadas, mas nenhuma informação foi fornecida.

- **Recomendação**: a fonte não retorna as informações corretas de precisão e escala. Verifique a coluna do problema para obter as informações.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Código de erro: ParquetInvalidDecimalPrecisionScale

- **Mensagem**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: O esquema é inválido.

- **Recomendação**: Verifique a precisão e a escala da coluna de problemas.


### <a name="error-code-parquetcolumnnotfound"></a>Código de erro: ParquetColumnNotFound

- **Mensagem**: `Column %column; does not exist in Parquet file.`

- **Causa**: o esquema de origem é uma incompatibilidade com o esquema de coletor.

- **Recomendação**: Verifique os mapeamentos na atividade. Verifique se a coluna de origem pode ser mapeada para a coluna de coletor correta.


### <a name="error-code-parquetinvaliddataformat"></a>Código de erro: ParquetInvalidDataFormat

- **Mensagem**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: os dados não podem ser convertidos no tipo especificado em mapeamentos. origem.

- **Recomendação**: Verifique os dados de origem ou especifique o tipo de dados correto para essa coluna no mapeamento de coluna da atividade de cópia. Para obter mais informações, consulte [formatos de arquivo com suporte e codecs de compactação por atividade de cópia no Azure data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Código de erro: ParquetDataCountNotMatchColumnCount

- **Mensagem**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: uma incompatibilidade entre a contagem de colunas de origem e a contagem de colunas do coletor.

- **Recomendação**: Verifique se a contagem de colunas de origem é a mesma que a contagem de colunas do coletor em ' mapeamento '.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Código de erro: ParquetDataTypeNotMatchColumnType

- **Mensagem**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Causa**: os dados da origem não podem ser convertidos para o tipo que é definido no coletor.

- **Recomendação**: especifique um tipo correto no Mapping. Sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Código de erro: ParquetBridgeInvalidData

- **Mensagem**: `%message;`

- **Causa**: o valor dos dados excedeu o limite.

- **Recomendação**: repita a operação. Se o problema persistir, entre em contato conosco.


### <a name="error-code-parquetunsupportedinterpretation"></a>Código de erro: ParquetUnsupportedInterpretation

- **Mensagem**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Causa**: não há suporte para esse cenário.

- **Recomendação**:  “ParquetInterpretFor” não deve ser “sparkSql”.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Código de erro: ParquetUnsupportFileLevelCompressionOption

- **Mensagem**: `File level compression is not supported for Parquet.`

- **Causa**: não há suporte para esse cenário.

- **Recomendação**: Remova ' CompressionType ' na carga.


### <a name="error-code-usererrorjniexception"></a>Código de erro: UserErrorJniException

- **Mensagem**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Causa**: uma JVM (máquina virtual Java) não pode ser criada porque alguns argumentos (globais) ilegais estão definidos.

- **Recomendação**: faça logon no computador que hospeda *cada nó* do seu ir hospedado automaticamente. Verifique se a variável do sistema está definida corretamente, da seguinte maneira: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Reinicie todos os nós IR e, em seguida, execute novamente o pipeline.


### <a name="arithmetic-overflow"></a>Estouro aritmético

- **Sintomas**: a mensagem de erro ocorreu quando você copia arquivos parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Causa**: atualmente, somente o decimal da precisão <= 38 e o comprimento da parte inteira <= 20 têm suporte quando você copia arquivos do Oracle para o parquet. 

- **Resolução**: como solução alternativa, você pode converter todas as colunas com esse problema em varchar2.


### <a name="no-enum-constant"></a>Nenhuma constante de enumeração

- **Sintomas**: a mensagem de erro ocorreu quando você copia dados para o formato parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` ou: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Causa**: 

    O problema pode ser causado por espaços em branco ou caracteres especiais sem suporte (como,; {} () \n\t =) no nome da coluna, porque parquet não oferece suporte a esse formato. 

    Por exemplo, um nome de coluna como *contoso (Test)* analisará o tipo entre colchetes do [código](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . O erro é gerado porque não há tal tipo de "teste".

    Para verificar os tipos com suporte, vá para o [site do Apache/parquet-Mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)do github.

- **Resolução**: 

    Verifique se há duas verificações para ver se:
    - Há espaços em branco no nome da coluna do coletor.
    - A primeira linha com espaços em branco é usada como o nome da coluna.
    - Há suporte para o tipo Originaltype. Tente evitar o uso destes caracteres especiais: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Código de erro: RestSinkCallFailed

- **Mensagem**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Causa**: esse erro ocorre quando Azure data Factory se comunica com o ponto de extremidade REST sobre o protocolo http e a operação de solicitação falha.

- **Recomendação**: Verifique o código de status HTTP ou a mensagem na mensagem de erro e corrija o problema do servidor remoto.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Resposta de rede inesperada do conector REST

- **Sintomas**: o ponto de extremidade às vezes recebe uma resposta inesperada (400, 401, 403, 500) do conector REST.

- **Causa**: o conector de origem REST usa a URL e o método HTTP/cabeçalho/corpo do serviço vinculado/conjunto de conteúdo/fonte de cópia como parâmetros ao construir uma solicitação HTTP. O problema é provavelmente causado por alguns erros em um ou mais parâmetros especificados.

- **Resolução**: 
    - Use ' ondulação ' em uma janela de prompt de comando para ver se o parâmetro é a causa (os cabeçalhos **Accept** e **User-Agent** devem ser sempre incluídos):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Se o comando retornar a mesma resposta inesperada, corrija os parâmetros anteriores com ' ondulação ' até que ele retorne a resposta esperada. 

      Você também pode usar ' enrolamento--ajuda ' para uso mais avançado do comando.

    - Se apenas o conector REST Data Factory retornar uma resposta inesperada, entre em contato com o suporte da Microsoft para obter mais soluções de problemas.
    
    - Observe que "Ondulação" pode não ser adequado para reproduzir um problema de validação de certificado SSL. Em alguns cenários, o comando "Ondulação" foi executado com êxito sem encontrar nenhum problema de validação de certificado SSL. Mas quando a mesma URL é executada em um navegador, nenhum certificado SSL é retornado para o cliente estabelecer confiança com o servidor.

      Ferramentas como o **postmaster** e o **Fiddler** são recomendadas para o caso anterior.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Código de erro: SftpOperationFail

- **Mensagem**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Causa**: um problema com a operação SFTP.

- **Recomendação**: Verifique os detalhes do erro do SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Código de erro: SftpRenameOperationFail

- **Mensagem**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Causa**: seu servidor SFTP não dá suporte à renomeação do arquivo temporário.

- **Recomendação**: Defina "useTempFileRename" como falso no coletor de cópia para desabilitar o carregamento para o arquivo temporário.


### <a name="error-code-sftpinvalidsftpcredential"></a>Código de erro: SftpInvalidSftpCredential

- **Mensagem**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Causa**: o conteúdo da chave privada é buscado do cofre de chaves do Azure ou do SDK, mas não está codificado corretamente.

- **Recomendação**:  

    Se o conteúdo da chave privada for do cofre de chaves, o arquivo de chave original poderá funcionar se você carregá-lo diretamente no serviço vinculado do SFTP.

    Para obter mais informações, consulte [copiar dados de e para o servidor SFTP usando Azure data Factory](./connector-sftp.md#use-ssh-public-key-authentication). O conteúdo da chave privada é o conteúdo da chave privada SSH codificada em base64.

    Codifique *todo* o arquivo de chave privada original com codificação Base64 e armazene a cadeia de caracteres codificada em seu cofre de chaves. O arquivo de chave privada original é aquele que pode funcionar no serviço vinculado do SFTP se você selecionar **carregar** do arquivo.

    Aqui estão alguns exemplos que você pode usar para gerar a cadeia de caracteres:

    - Usar código C#:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Use o código Python:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Use uma ferramenta de conversão de Base64 de terceiros. Recomendamos a ferramenta [codificar para formato Base64](https://www.base64encode.org/) .

- **Causa**: o formato de conteúdo de chave incorreto foi escolhido.

- **Recomendação**:  

    PKCS # 8 Format chave privada SSH (começar com "-----começar a chave privada CRIPTOGRAFAda-----") não tem suporte no momento para acessar o servidor SFTP no Data Factory. 

    Para converter a chave no formato de chave SSH tradicional, começando com "-----iniciar a chave privada RSA-----", execute os seguintes comandos:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Causa**: credenciais inválidas ou conteúdo de chave privada.

- **Recomendação**: para ver se o arquivo de chave ou a senha está correta, verifique novamente com ferramentas como WinSCP.

### <a name="sftp-copy-activity-failed"></a>Falha na atividade de cópia de SFTP

- **Sintomas**: 
  * Código de erro: UserErrorInvalidColumnMappingColumnNotFound 
  * Mensagem de erro: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Causa**: a origem não inclui uma coluna chamada "AccMngr".

- **Resolução**: para determinar se a coluna "AccMngr" existe, verifique novamente a configuração do conjunto de configurações mapeando a coluna do conjunto de banco de de destino.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Código de erro: SftpFailedToConnectToSftpServer

- **Mensagem**: `Failed to connect to SFTP server '%server;'.`

- **Causa**: se a mensagem de erro contiver a cadeia de caracteres "a operação de leitura de soquete esgotou o tempo limite após 30.000 milissegundos", uma possível causa é que um tipo de serviço vinculado incorreto é usado para o servidor SFTP. Por exemplo, você pode estar usando o serviço vinculado de FTP para se conectar ao servidor SFTP.

- **Recomendação**: Verifique a porta do servidor de destino. Por padrão, o SFTP usa a porta 22.

- **Causa**: se a mensagem de erro contiver a cadeia de caracteres "a resposta do servidor não contém a identificação do protocolo SSH", uma possível causa é que o servidor SFTP limitou a conexão. Data Factory criará várias conexões para baixar do servidor SFTP em paralelo e, às vezes, encontrará a limitação do servidor SFTP. Normalmente, servidores diferentes retornam erros diferentes quando encontram limitação.

- **Recomendação**:  

    Especifique o número máximo de conexões simultâneas do conjunto de acordo de SFTP como 1 e execute novamente a atividade de cópia. Se a atividade for realizada com sucesso, você pode ter certeza de que a limitação é a causa.

    Se você quiser promover a baixa taxa de transferência, entre em contato com o administrador do SFTP para aumentar o limite de contagem de conexões simultâneas ou faça um dos seguintes:

    * Se você estiver usando o IR auto-hospedado, adicione o IP da máquina IR via hospedagem interna à lista de permissões.
    * Se você estiver usando Azure IR, adicione [Azure Integration Runtime endereços IP](./azure-integration-runtime-ip-addresses.md). Se você não quiser adicionar um intervalo de IPs à lista de permissões do servidor SFTP, use o IR via hospedagem interna em vez disso.

## <a name="sharepoint-online-list"></a>Lista do SharePoint Online

### <a name="error-code-sharepointonlineauthfailed"></a>Código de erro: SharePointOnlineAuthFailed

- **Mensagem**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Causa**: a ID e a chave da entidade de serviço podem não estar definidas corretamente.

- **Recomendação**: Verifique o aplicativo registrado (ID da entidade de serviço) e a chave para ver se eles estão definidos corretamente.


## <a name="xml-format"></a>Formato XML

### <a name="error-code-xmlsinknotsupported"></a>Código de erro: XmlSinkNotSupported

- **Mensagem**: `Write data in XML format is not supported yet, choose a different format!`

- **Causa**: um conjunto de um banco de um XML foi usado como um conjunto de coleta de banco de uma atividade de cópia.

- **Recomendação**: Use um conjunto de um DataSet em um formato diferente daquele do conjunto de coleta.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Código de erro: XmlAttributeColumnNameConflict

- **Mensagem**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Causa**: um prefixo de atributo foi usado, o que causou o conflito.

- **Recomendação**: defina um valor diferente para a propriedade "attributePrefix".


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Código de erro: XmlValueColumnNameConflict

- **Mensagem**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Causa**: um dos nomes de elemento filho foi usado como o nome da coluna para o valor do elemento.

- **Recomendação**: defina um valor diferente para a propriedade "valueColumn".


### <a name="error-code-xmlinvalid"></a>Código de erro: xmlinvalid

- **Mensagem**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Causa**: o arquivo XML de entrada não está bem formado.

- **Recomendação**: corrija o arquivo XML para torná-lo bem formado.


## <a name="general-copy-activity-error"></a>Erro geral da atividade de cópia

### <a name="error-code-jrenotfound"></a>Código de erro: JreNotFound

- **Mensagem**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa**: o ir auto-hospedado não pode localizar o tempo de execução Java. O tempo de execução do Java é necessário para ler fontes específicas.

- **Recomendação**: Verifique seu ambiente de tempo de execução de integração, confira [usar Integration Runtime de hospedagem interna](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Código de erro: WildcardPathSinkNotSupported

- **Mensagem**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa**: o conjunto de coleta não dá suporte a valores curinga.

- **Recomendação**: Verifique o conjunto de valores do coletor e reescreva o caminho sem usar um valor curinga.


### <a name="fips-issue"></a>Problema de FIPS

- **Sintomas**: a atividade de cópia falha em uma máquina ir autohospedada habilitada para FIPS com a seguinte mensagem de erro: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Causa**: esse erro pode ocorrer quando você copia dados com conectores como blob do Azure, SFTP e assim por diante. O FIPS (Federal Information Processing Standards) define um determinado conjunto de algoritmos de criptografia que podem ser usados. Quando o modo FIPS está habilitado no computador, algumas classes criptográficas das quais a atividade de cópia depende são bloqueadas em alguns cenários.

- **Resolução**: saiba [por que não estamos mais recomendando o "modo FIPS"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)e avalie se você pode desabilitar o FIPS em sua máquina ir hospedada internamente.

    Como alternativa, se você quiser permitir que Azure Data Factory ignore o FIPS e faça com que a atividade seja executada com sucesso, faça o seguinte:

    1. Abra a pasta na qual o IR auto-hospedado está instalado. O caminho geralmente é *C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Abra o arquivo *diawp.exe.config* e, no final da `<runtime>` seção, adicione `<enforceFIPSPolicy enabled="false"/>` , conforme mostrado aqui:

        ![Captura de tela de uma seção do arquivo diawp.exe.config mostrando o FIPS desabilitado.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Salve o arquivo e reinicie o computador IR auto-hospedado.


## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&uma página](/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)