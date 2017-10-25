---
title: Copiar dados para/do SQL Data Warehouse do Azure usando o Data Factory.| Microsoft Docs
description: Saiba como copiar dados de armazenamentos de origem com suporte para o SQL Data Warehouse do Azure ou do SQL Data Warehouse para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: fb67c70d1e85307c38a185e2b47729880880d55b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar dados de e para o SQL Data Warehouse do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 – Versão prévia](connector-azure-sql-data-warehouse.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para um SQL Data Warehouse do Azure. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do SQL Data Warehouse do Azure na V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados de/para o SQL Data Warehouse do Azure para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o SQL Data Warehouse do Azure. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SQL Data Warehouse do Azure dá suporte a:

- Dá suporte à cópia de dados, usando a autenticação do SQL.
- Como fonte, dá suporte à recuperação de dados, usando a consulta SQL ou o procedimento armazenado.
- Como o coletor, dá suporte ao carregamento de dados usando **PolyBase** ou inserção em massa. A primeira opção é a **recomendada** para melhor desempenho de cópia.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do SQL Data Warehouse do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do SQL Data Warehouse do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureSqlDW** | Sim |
| connectionString |Especifique as informações necessárias para se conectar à instância do SQL Data Warehouse do Azure para a propriedade connectionString. Há suporte somente para autenticação básica. Marque esse campo como uma SecureString. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |


> [!IMPORTANT]
> Configure o [Firewall do SQL Data Warehouse do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e o servidor de banco de dados para [permitir que os Serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você estiver copiando os dados para o SQL Data Warehouse do Azure de fora do Azure, inclusive de fontes de dados locais, com o Integration Runtime auto-hospedado, configure o devido intervalo de endereços IP do computador que está enviando os dados para o SQL Data Warehouse do Azure.

**Exemplo:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SQL Data Warehouse do Azure.

Para copiar dados de/para o SQL Data Warehouse do Azure, defina a propriedade type do conjunto de dados como **AzureSqlDWTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureSqlDWTable** | Sim |
| tableName |Nome da tabela ou exibição na instância do SQL Data Warehouse do Azure à qual o serviço vinculado se refere. | Sim |

**Exemplo:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pelo coletor e pela fonte do SQL Data Warehouse do Azure.

### <a name="azure-sql-data-warehouse-as-source"></a>SQL Data Warehouse do Azure como fonte

Para copiar dados do SQL Data Warehouse do Azure, defina o tipo de fonte na atividade de cópia como **SqlDWSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **SqlDWSource** | Sim |
| SqlReaderQuery |Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são: pares nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

**Pontos a serem observados:**

- Se **sqlReaderQuery** for especificado para SqlSource, a Atividade de cópia executará essa consulta na fonte do SQL Data Warehouse do Azure para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).
- Se você não especificar "sqlReaderQuery" nem "sqlReaderStoredProcedureName", as colunas definidas na seção "estrutura" do JSON do conjunto de dados serão usadas para criar uma consulta (`select column1, column2 from mytable`) para ser executada no SQL Data Warehouse do Azure. Se a definição de conjunto de dados não tiver a seção "structure", todas as colunas serão selecionadas da tabela.
- Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar uma propriedade **tableName** fictícia no JSON do conjunto de dados.

**Exemplo: usando a consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo: usando o procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A definição do procedimento armazenado:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>SQL Data Warehouse do Azure como coletor

Para copiar dados para o SQL Data Warehouse do Azure, defina o tipo de coletor na atividade de cópia como **SqlDWSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **SqlDWSink** | Sim |
| allowPolyBase |Indica se o PolyBase (quando aplicável) deve ser utilizado em vez do mecanismo BULKINSERT. <br/><br/> **Usar o PolyBase é a maneira recomendada para carregar dados no SQL Data Warehouse.** Confira a seção [Usar o PolyBase para carregar dados no Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter os detalhes e as restrições.<br/><br/>Os valores permitidos são: **True** (padrão) e **False**.  |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** está definida como **true**. |Não |
| rejectValue |Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção **Argumentos** do tópico [CRIAR TABELA EXTERNA (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . <br/><br/>Os valores permitidos são: 0 (padrão), 1, 2, ... |Não |
| rejectType |Especifica se a opção rejectValue é especificada como um valor literal ou um percentual.<br/><br/>Os valores permitidos são: **Valor** (padrão) e **Percentual**. |Não |
| rejectSampleValue |Determina o número de linhas a serem recuperadas antes de o PolyBase recalcular o percentual de linhas rejeitadas.<br/><br/>Valores permitidos são: 1, 2, … |Sim, se **rejectType** for **percentual** |
| useTypeDefault |Especifica como tratar valores ausentes nos arquivos de texto delimitados quando PolyBase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são: **True**, **False** (padrão). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. Aplica-se somente quando o PolyBase não é usado.<br/><br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 10000) |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. Aplica-se somente quando o PolyBase não é usado.<br/><br/>Os valores permitidos são: período. Exemplo: "00:30:00" (30 minutos). |Não |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia, a ser executada antes de gravar dados no SQL Data Warehouse do Azure em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não |(#repeatability-during-copy). |Uma instrução de consulta. |Não |

**Exemplo:**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Saiba mais sobre como usar o PolyBase para carregar no SQL Data Warehouse com eficiência na próxima seção.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Use PolyBase to load data into Azure SQL Data Warehouse

Usar o **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** é uma maneira eficiente de carregar grandes quantidades de dados no SQL Data Warehouse do Azure com alta taxa de transferência. Você pode notar um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão. Veja [número de referência do desempenho de cópia](copy-activity-performance.md#performance-reference) com comparação detalhada. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](connector-azure-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).

* Se os dados de origem estiverem no **Blob do Azure ou no Azure Data Lake Store** e o formato for compatível com o PolyBase, você poderá copiar diretamente para o SQL Data Warehouse do Azure usando PolyBase. Veja **[Cópia direta usando o PolyBase](#direct-copy-using-polybase)** com detalhes.
* Se o armazenamento de dados de origem e seu formato não tiver suporte originalmente no PolyBase, você poderá usar o recurso **[Cópia em etapas usando o PolyBase](#staged-copy-using-polybase)**. Ele também fornece uma melhor produtividade convertendo automaticamente os dados em um formato compatível com o PolyBase e armazenando os dados no armazenamento de Blobs do Azure. Em seguida, ele carrega os dados no SQL Data Warehouse.

### <a name="direct-copy-using-polybase"></a>Cópia direta usando o PolyBase

O SQL Data Warehouse PolyBase dá suporte diretamente ao Blob do Azure e ao Azure Data Lake Store (usando a entidade de serviço) como origem e com os requisitos de formato de arquivo específico. Se os dados de origem atenderem aos critérios descritos nesta seção, você poderá copiar diretamente do armazenamento de dados de origem para o Azure SQL Data Warehouse usando o PolyBase. Caso contrário, poderá aproveitar a [Cópia de preparo usando o PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados do Data Lake Store para o SQL Data Warehouse com eficiência, saiba mais sobre como o [Azure Data Factory torna ainda mais fácil e conveniente revelar informações de dados usando o Data Lake Store com o SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, o Azure Data Factory verificará as configurações e automaticamente reverterá para o mecanismo BULKINSERT da movimentação de dados.

1. O **serviço vinculado de origem** é do tipo: **AzureStorage** ou **AzureDataLakeStore**.
2. O **conjunto de dados de entrada** é do tipo: **AzureBlob** ou **AzureDataLakeStoreFile**, e o tipo de formato nas propriedades `type` é **OrcFormat** ou **TextFormat** com as seguintes configurações:

   1. `rowDelimiter` deve ser **\n**.
   2. `nullValue` é definido como **cadeia de caracteres vazia** ("") ou `treatEmptyAsNull` é definido como **true**.
   3. `encodingName` é definido como **utf-8**, que é o valor **padrão**.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` e `skipLineCount` não foram especificados.
   5. `compression` pode ser **sem compactação**, **GZip** ou **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Não há uma configuração `skipHeaderLineCount` em **BlobSource** ou **AzureDataLakeStore** para a atividade de cópia no pipeline.
4. Não há nenhuma configuração `sliceIdentifierColumnName` em **SqlDWSink** para a atividade de Cópia no pipeline. (O PolyBase garante que todos os dados são atualizados ou que nada é atualizado em uma execução única. Para obter **repetibilidade**, você pode usar `sqlWriterCleanupScript`).
5. Não há nenhum `columnMapping` sendo usado na atividade de Cópia associada.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>Cópia de preparo usando o PolyBase

Quando os dados de origem não atenderem aos critérios introduzidos na seção anterior, você poderá habilitar a cópia dos dados via Armazenamento de Blobs do Azure de preparo provisório (não pode ser Armazenamento Premium). Neste caso, o Azure Data Factory executa automaticamente transformações nos dados para atender aos requisitos do formato de dados do PolyBase e, em seguida, usa o PolyBase para carregar os dados no SQL Data Warehouse e, então, limpa os dados temporários do Armazenamento de Blobs. Confira [Cópia de Preparo](copy-activity-performance.md#staged-copy) para obter detalhes sobre como copiar dados por meio de um trabalho de preparo do Blob do Azure em geral.

Para usar esse recurso, crie um [serviço vinculado de Armazenamento do Azure](connector-azure-blob-storage.md#linked-service-properties) que se refere à Conta de Armazenamento do Azure que tem o armazenamento de blobs provisório, então, especifique as propriedades `enableStaging` e `stagingSettings` para a Atividade de Cópia, como mostrado no código a seguir:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob"
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Práticas recomendadas ao usar o PolyBase

As seções a seguir fornecem práticas recomendadas adicionais àquelas mencionadas em [Práticas recomendadas para o SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de banco de dados obrigatória

Para usar o PolyBase, é preciso que o usuário que está sendo usado para carregar dados no SQL Data Warehouse tenha a [permissão "CONTROLE"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Para isso, adicione esse usuário como um membro da função "db_owner". Saiba como fazer isso seguindo [esta seção](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitação de tamanho da linha e tipo de dados

As cargas do Polybase estão limitadas a carregar linhas com menos de **1 MB** e que não podem ser carregadas para VARCHR(MAX), NVARCHAR(MAX) nem VARBINARY(MAX). Confira [aqui](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Caso você tenha dados de origem com linhas maiores que 1 MB, talvez você deseje dividir as tabelas de origem verticalmente em várias pequenas, em que o maior tamanho de linha de cada uma delas não excede o limite. As tabelas menores podem ser carregadas usando o PolyBase e mescladas no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse

Para obter a melhor produtividade possível, considere atribuir a maior classe de recursos ao usuário que está sendo usado para carregar dados no SQL Data Warehouse por meio do PolyBase. Saiba como fazer isso seguindo [Alterar um exemplo de classe de recurso de usuário](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName no Azure SQL Data Warehouse

A tabela a seguir fornece exemplos de como especificar a propriedade **tableName** no conjunto de dados JSON para várias combinações de nome de esquema e de tabela.

| Esquema do BD | Nome da tabela | Propriedade JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo.MyTable ou [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable ou [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] ou [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Se você vir o erro a seguir, pode ser um problema com o valor especificado para a propriedade tableName. Consulte a tabela para ver a maneira correta de especificar os valores para a propriedade JSON tableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores padrão

Atualmente, o recurso PolyBase no Data Factory só aceita o mesmo número de colunas da tabela de destino. Digamos que você tenha uma tabela com quatro colunas e uma delas esteja definida com um valor padrão. Os dados de entrada ainda devem conter quatro colunas. Fornecer um conjunto de dados de entrada com três colunas produziria um erro parecido com a mensagem a seguir:

```
All columns of the table must be specified in the INSERT BULK statement.
```

O valor NULL é uma forma especial do valor padrão. Se a coluna for anulável, os dados de entrada (no blob) para essa coluna poderão estar vazios (não poderão estar ausentes no conjunto de dados de entrada). O PolyBase insere NULL para eles no Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados do SQL Data Warehouse do Azure

Ao copiar dados de/para o SQL Data Warehouse do Azure, os seguintes mapeamentos são usados de tipos de dados do SQL Data Warehouse do Azure para tipos de dados intermediários do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do SQL Data Warehouse do Azure | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binário |Byte[] |
| bit |Booliano |
| char |String, Char[] |
| data |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Duplo |
| imagem |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérico |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objeto * |
| texto |String, Char[] |
| tempo real |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).