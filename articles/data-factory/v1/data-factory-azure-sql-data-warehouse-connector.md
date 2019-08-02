---
title: Copiar dados bidirecionalmente no SQL Data Warehouse do Azure | Microsoft Docs
description: Saiba como copiar dados bidirecionalmente no SQL Data Warehouse do Azure usando o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7570cfc8a9804f753a9de140a71436bcc0cebb43
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836646"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Copiar dados bidirecionalmente no SQL Data Warehouse do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do SQL Data Warehouse do Azure na V2 ](../connector-azure-sql-data-warehouse.md).

Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados bidirecionalmente no SQL Data Warehouse do Azure. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

> [!TIP]
> Para obter melhor desempenho, use o PolyBase para carregar dados no SQL Data Warehouse. A seção [Use PolyBase to load data into Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) (Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse) apresenta os detalhes. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode copiar dados **de um SQL Data Warehouse do Azure** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos armazenamentos de dados a seguir **para um SQL Data Warehouse do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Ao copiar dados do SQL Server ou do Banco de Dados SQL do Azure para o SQL Data Warehouse do Azure, se a tabela não existir no repositório de destino, o Data Factory poderá criar a tabela automaticamente no SQL Data Warehouse usando o esquema da tabela no armazenamento de dados de origem. Consulte [Criação automática de tabela](#auto-table-creation) para obter detalhes.

## <a name="supported-authentication-type"></a>Tipos de autenticação com suporte
O conector do SQL Data Warehouse do Azure dá suporte à autenticação básica.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de um banco de dados bidirecionalmente em um SQL Data Warehouse do Azure usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline que copia dados de/para o Azure SQL Data Warehouse é usar o Assistente de cópia de dados. Consulte [Tutorial: Carregar dados no SQL Data Warehouse com o Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) para ver uma rápida explicação passo a passo sobre como criar um pipeline usando o Assistente para cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines. 
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você está copiando dados de um Armazenamento de Blobs do Azure para um SQL Data Warehouse do Azure, crie dois serviços vinculados para vincular seu SQL Data Warehouse do Azure e sua conta de Armazenamento do Azure ao data factory. Para propriedades do serviço vinculado que são específicas do SQL Data Warehouse do Azure, consulte a seção [propriedades do serviço vinculado](#linked-service-properties). 
3. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de blob e a pasta que contém os dados de entrada. Em seguida, você cria outro conjunto de dados para especificar a tabela no SQL Data Warehouse do Azure que contém os dados copiados do Armazenamento de Blobs. Para propriedades do conjunto de dados que são específicas do SQL Data Warehouse do Azure, consulte a seção [propriedades do conjunto de dados](#dataset-properties).
4. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa BlobSource como fonte e SqlDWSink como coletor para a atividade de cópia. De modo similar, se você estiver copiando do SQL Data Warehouse do Azure para o Armazenamento de Blobs do Azure, você usará SqlDWSource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas do SQL Data Warehouse do Azure, consulte a seção [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para o armazenamento de dados.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um SQL Data Warehouse do Azure, confira a seção [Exemplos de JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao SQL Data Warehouse do Azure:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço vinculado do SQL Data Warehouse do Azure.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **AzureSqlDW** |Sim |
| connectionString |Especifique as informações necessárias para se conectar à instância do SQL Data Warehouse do Azure para a propriedade connectionString. Há suporte somente para autenticação básica. |Sim |

> [!IMPORTANT]
> Configure o [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e o servidor do banco de dados para [permitir que os Serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você estiver copiando os dados para o Azure SQL Data Warehouse de fora do Azure, inclusive a partir das fontes de dados locais com o gateway do data factory, configure o devido intervalo de endereços IP do computador que está enviando os dados para o Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** do conjunto de dados do tipo **AzureSqlDWTable** tem as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição no banco de dados SQL Data Warehouse do Azure ao qual o serviço vinculado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

### <a name="sqldwsource"></a>SqlDWSource
Quando a fonte é do tipo **SqlDWSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se **sqlReaderQuery** for especificado para SqlDWSource, a Atividade de Cópia executará essa consulta na fonte do SQL Data Warehouse do Azure para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção da estrutura do JSON do conjunto de dados serão usadas para compilar uma consulta para executar no Azure SQL Data Warehouse. Exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

#### <a name="sqldwsource-example"></a>Exemplo de SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**A definição do procedimento armazenado:**

```SQL
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** dá suporte às seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. Para obter detalhes, consulte a [seção de repetição](#repeatability-during-copy). |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se o PolyBase (quando aplicável) deve ser utilizado em vez do mecanismo BULKINSERT. <br/><br/> **Usar o PolyBase é a maneira recomendada para carregar dados no SQL Data Warehouse.** Confira a seção [Usar o PolyBase para carregar dados no Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter os detalhes e as restrições. |verdadeiro <br/>False (padrão) |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** está definida como **true**. |&nbsp; |Não |
| rejectValue |Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção **Argumentos** do tópico [CRIAR TABELA EXTERNA (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . |0 (padrão), 1, 2, … |Não |
| rejectType |Especifica se a opção rejectValue é especificada como um valor literal ou um percentual. |Valor (padrão), Percentual |Não |
| rejectSampleValue |Determina o número de linhas a serem recuperadas antes de o PolyBase recalcular o percentual de linhas rejeitadas. |1, 2, … |Sim, se **rejectType** for **percentual** |
| useTypeDefault |Especifica como tratar valores ausentes nos arquivos de texto delimitados quando PolyBase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, False (padrão) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: “00:30:00” (30 minutos). |Não |

#### <a name="sqldwsink-example"></a>Exemplo de SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Use PolyBase to load data into Azure SQL Data Warehouse
Usar o **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** é uma maneira eficiente de carregar grandes quantidades de dados no SQL Data Warehouse do Azure com alta taxa de transferência. Você pode notar um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão. Veja [número de referência do desempenho de cópia](data-factory-copy-activity-performance.md#performance-reference) com comparação detalhada. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).

* Se os dados de origem estiverem no **Blob do Azure ou no Azure Data Lake Store** e o formato for compatível com o PolyBase, você poderá copiar diretamente para o SQL Data Warehouse do Azure usando PolyBase. Veja **[Cópia direta usando o PolyBase](#direct-copy-using-polybase)** com detalhes.
* Se o armazenamento de dados de origem e seu formato não tiver suporte originalmente no PolyBase, você poderá usar o recurso **[Cópia em etapas usando o PolyBase](#staged-copy-using-polybase)** . Ele também fornece uma melhor produtividade convertendo automaticamente os dados em um formato compatível com o PolyBase e armazenando os dados no armazenamento de Blobs do Azure. Em seguida, ele carrega os dados no SQL Data Warehouse.

Defina a propriedade `allowPolyBase` como **true**, conforme mostrado no exemplo a seguir para o Azure Data Factory usar o PolyBase para copiar dados para o Azure SQL Data Warehouse. Quando você definir allowPolyBase para true, poderá especificar determinadas propriedades do PolyBase usando o grupo de propriedades `polyBaseSettings`. Consulte a seção [SqlDWSink](#sqldwsink) para obter detalhes sobre as propriedades que você pode usar com polyBaseSettings.

```JSON
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

### <a name="direct-copy-using-polybase"></a>Cópia direta usando o PolyBase
O SQL Data Warehouse PolyBase dá suporte diretamente ao Blob do Azure e ao Azure Data Lake Store (usando a entidade de serviço) como origem e com os requisitos de formato de arquivo específico. Se os dados de origem atenderem aos critérios descritos nesta seção, você poderá copiar diretamente do armazenamento de dados de origem para o Azure SQL Data Warehouse usando o PolyBase. Caso contrário, poderá aproveitar a [Cópia de preparo usando o PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados do Data Lake Store para o SQL Data Warehouse com eficiência, saiba mais sobre como o [Azure Data Factory torna ainda mais fácil e conveniente revelar informações de dados usando o Data Lake Store com o SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, o Azure Data Factory verificará as configurações e automaticamente reverterá para o mecanismo BULKINSERT da movimentação de dados.

1. O **serviço vinculado de origem** é do tipo: **AzureStorage** ou **AzureDataLakeStore** com autenticação da entidade de serviço.
2. O **conjunto de dados de entrada** é do tipo: **AzureBlob** ou **AzureDataLakeStore**, e o tipo de formato nas propriedades `type` é **OrcFormat**, **ParquetFormat** ou **TextFormat** com as configurações abaixo:

   1. `rowDelimiter` deve ser **\n**.
   2. `nullValue` é definido como **cadeia de caracteres vazia** ("") ou `treatEmptyAsNull` é definido como **true**.
   3. `encodingName` é definido como **utf-8**, que é o valor **padrão**.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` e `skipLineCount` não foram especificados.
   5. `compression` pode ser **sem compactação**, **GZip** ou **Deflate**.

      ```JSON
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

### <a name="staged-copy-using-polybase"></a>Cópia de preparo usando o PolyBase
Quando os dados de origem não atenderem aos critérios introduzidos na seção anterior, você poderá habilitar a cópia dos dados via Armazenamento de Blobs do Azure de preparo provisório (não pode ser Armazenamento Premium). Neste caso, o Azure Data Factory executa automaticamente transformações nos dados para atender aos requisitos do formato de dados do PolyBase, em seguida, use o PolyBase para carregar os dados no SQL Data Warehouse e, por fim, limpe os dados temporários do Armazenamento de Blobs. Confira [Cópia de Preparo](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre como copiar dados por meio de um trabalho de preparo do Blob do Azure em geral.

> [!NOTE]
> Quando copiar dados de um local armazenamento de dados no Azure SQL Data Warehouse usando PolyBase e preparo, se sua versão do Gateway de gerenciamento de dados for inferior a 2.4, o JRE (Java Runtime Environment) é necessária no computador do gateway que é usada para transformar sua fonte dados em formato adequado. Sugerimos que você faça upgrade do gateway para a versão mais recente para evitar essa dependência.
>

Para usar esse recurso, crie um [serviço vinculado de Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) que se refere à Conta de Armazenamento do Azure que tem o armazenamento de blobs provisório, então, especifique as propriedades `enableStaging` e `stagingSettings` para a Atividade de Cópia, como mostrado no código a seguir:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
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
As seções a seguir fornecem práticas recomendadas adicionais àquelas mencionadas em [Práticas recomendadas para o SQL Data Warehouse do Azure](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de banco de dados obrigatória
Para usar o PolyBase, é preciso que o usuário que está sendo usado para carregar dados no SQL Data Warehouse tenha a [permissão "CONTROLE"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Para isso, adicione esse usuário como um membro da função "db_owner". Saiba como fazer isso seguindo [esta seção](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitação de tamanho da linha e tipo de dados
As cargas do Polybase estão limitadas a carregar linhas com menos de **1 MB** e que não podem ser carregadas para VARCHR(MAX), NVARCHAR(MAX) nem VARBINARY(MAX). Confira [aqui](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Caso você tenha dados de origem com linhas maiores que 1 MB, talvez você deseje dividir as tabelas de origem verticalmente em várias pequenas, em que o maior tamanho de linha de cada uma delas não excede o limite. As tabelas menores podem ser carregadas usando o PolyBase e mescladas no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse
Para obter a melhor produtividade possível, considere atribuir a maior classe de recursos ao usuário que está sendo usado para carregar dados no SQL Data Warehouse por meio do PolyBase. Saiba como fazer isso seguindo [Alterar um exemplo de classe de recurso de usuário](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName no Azure SQL Data Warehouse
A tabela a seguir fornece exemplos de como especificar a propriedade **tableName** no conjunto de dados JSON para várias combinações de nome de esquema e de tabela.

| Esquema do BD | Nome da tabela | Propriedade JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo.MyTable ou [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable ou [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] ou [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

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

## <a name="auto-table-creation"></a>Criação automática de tabela
Se você estiver usando o Assistente de Cópia para copiar dados do SQL Server ou do Banco de Dados SQL do Azure para o SQL Data Warehouse do Azure e a tabela que corresponde à tabela de origem não existir no repositório de destino, o Data Factory poderá criar automaticamente a tabela no data warehouse usando o esquema da tabela de origem.

O Data Factory cria a tabela no repositório de destino com o mesmo nome de tabela no armazenamento de dados de origem. Os tipos de dados das colunas são escolhidos de acordo com o mapeamento de tipo a seguir. Se necessário, ele executa conversões de tipo para corrigir todas as incompatibilidades entre os repositórios de origem e de destino. Ele também usa a distribuição de tabelas em um Round Robin.

| Tipo de coluna do Banco de Dados SQL da origem | Tipo de coluna do SQL DW de destino (limitação de tamanho) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Numeric | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binary | Binary |
| Varbinary | Varbinary (até 8.000) |
| Date | Date |
| Datetime | Datetime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (até 8.000) |
| NText | NVarChar (até 4.000) |
| Image | VarBinary (até 8.000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (até 8.000) |
| NVarChar | NVarChar (até 4.000) |
| Xml | Varchar (até 8.000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo do SQL Data Warehouse do Azure
Como mencionado no artigo sobre as [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) , a atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados bidirecionalmente no SQL Data Warehouse do Azure, os mapeamentos a seguir são usados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o [Mapeamento de tipo de dados do SQL Server para o ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de mecanismo do Banco de Dados do SQL Server | Tipo .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Duplo |
| imagem |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Simples |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

Você também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de coletor na definição da atividade de cópia. Para obter detalhes, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapear colunas de conjunto de dados no Azure Data Factory).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Exemplos JSON para cópia de dados de e para o SQL Data Warehouse
Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados entre o Azure SQL Data Warehouse e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exemplo: Copiar dados do SQL Data Warehouse do Azure para o Blob do Azure
O exemplo define as entidades do Data Factory a seguir:

1. Um serviço vinculado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlDWTable](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia os dados da série temporal (por hora, dia etc.) de uma tabela no banco de dados do Azure SQL Data Warehouse para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Data Warehouse do Azure:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço vinculado do armazenamento de Blob do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do SQL Data Warehouse do Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Data Warehouse do Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Atividade de cópia em um pipeline com SqlDWSource e BlobSink:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **SqlDWSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> No exemplo, **sqlReaderQuery** é especificada para o SqlDWSource. A Atividade de Cópia executa essa consulta em relação à fonte de SQL Data Warehouse do Azure para obter os dados.
>
> Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).
>
> Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura do conjunto de dados JSON são usadas para criar uma consulta (selecione column1, column2 de mytable) para executar o SQL Data Warehouse do Azure. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exemplo: Copiar dados do Blob do Azure para o SQL Data Warehouse do Azure
O exemplo define as entidades do Data Factory a seguir:

1. Um serviço vinculado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlDWTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

O exemplo copia os dados da série temporal (por hora, dia etc.) de um blob do Azure para uma tabela no banco de dados do Azure SQL Data Warehouse a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Data Warehouse do Azure:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço vinculado do armazenamento de Blob do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte do ano, mês e dia da hora de início e o nome de arquivo usa a parte da hora de início. A configuração “external”: ”true” informa ao serviço Data Factory que essa é uma tabela externa do data factory e não é produzida por uma atividade no data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados de saída do SQL Data Warehouse do Azure:**

O exemplo copia os dados em uma tabela chamada "MyTable" no SQL Data Warehouse do Azure. Crie a tabela no Azure SQL Data Warehouse com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Atividade de cópia em um pipeline com BlobSource e SqlDWSink:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Para obter instruções, consulte a [carregar 1 TB no depósito de dados do SQL Azure em 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md) e [carregar dados com o Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artigo na documentação do Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
