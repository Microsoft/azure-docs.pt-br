---
title: Em vez de ETL, ELT de design
description: Implemente estratégias flexíveis de carregamento de dados para pools SQL dedicados no Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b59e256f137bc8e1b13cb92d477e32bc01629ac5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679889"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Estratégias de carregamento de dados para o pool de SQL dedicado no Azure Synapse Analytics

Os pools de SQL dedicados SMP tradicionais usam um processo ETL (extração, transformação e carregamento) para carregar dados. O Synapse SQL, na análise de Synapse do Azure, usa a arquitetura de processamento de consulta distribuída que aproveita a escalabilidade e a flexibilidade dos recursos de computação e armazenamento.

Usar um processo de extração, carregamento e transformação (ELT) aproveita os recursos internos de processamento de consultas distribuídas e elimina os recursos necessários para a transformação de dados antes do carregamento.

Embora os pools de SQL dedicados ofereçam suporte a muitos métodos de carregamento, incluindo opções populares de SQL Server como [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e a [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), a maneira mais rápida e escalonável de carregar dados é por meio de tabelas externas do polybase e a [instrução de cópia](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Com o PolyBase e a instrução COPY, você pode acessar dados externos armazenados no Armazenamento de Blobs do Azure ou Azure Data Lake Storage por meio da linguagem T-SQL. Para obter mais flexibilidade ao carregar, é recomendável usar a instrução COPY.


## <a name="what-is-elt"></a>O que é ELT?

O ELT (extração, carregamento e transformação) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados em um pool SQL dedicado e, em seguida, transformados.

As etapas básicas para implementar ELT são:

1. Extrair os dados de origem em arquivos de texto.
2. Descarregar os dados no armazenamento de Blobs do Azure ou no Azure Data Lake Store.
3. Preparar os dados para o carregamento.
4. Carregar os dados em tabelas de preparo com o PolyBase ou o comando COPY.
5. Transformar os dados.
6. Inserir os dados nas tabelas de produção.

Para obter um tutorial de carregamento, consulte [carregando dados do armazenamento de BLOBs do Azure](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em arquivos de texto

Obter dados de fora do seu sistema de origem depende da localização de armazenamento. O objetivo é mover os dados para arquivos CSV ou de texto delimitados com suporte.

### <a name="supported-file-formats"></a>Formatos de arquivo com suporte

Com o PolyBase e a instrução COPY, você pode carregar dados de texto delimitado codificado em UTF-8 e UTF-16 ou arquivos CSV. Além de texto delimitado ou arquivos CSV, ele é carregado de formatos de arquivo do Hadoop, como ORC e Parquet. O PolyBase e a instrução COPY também podem carregar dados de arquivos compactados Gzip e Snappy.

Não há suporte para ASCII estendido, formato de largura fixa nem formatos aninhados, como WinZip ou XML. Se você está exportando do SQL Server, pode usar a [ferramenta de linha de comando bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para exportar os dados para arquivos de texto delimitados.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Descarregar os dados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage

Para descarregar dados para o armazenamento do Azure, você pode movê-los para o [Armazenamento de Blobs do Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou [Azure Data Lake Storage Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Em qualquer localização, os dados devem ser armazenados em arquivos de texto. O PolyBase e a instrução COPY podem ser carregados de qualquer uma das localizações.

Ferramentas e serviços que você pode usar para mover dados para o Armazenamento do Microsoft Azure:

- O serviço [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) melhora a taxa de transferência de rede, o desempenho e a previsibilidade. O ExpressRoute é um serviço que encaminha os dados por uma conexão privada dedicada para o Azure. As conexões do ExpressRoute não encaminham dados pela Internet pública. As conexões oferecem mais confiabilidade e velocidade, latências menores e maior segurança do que as conexões comuns pela Internet.
- O [Utilitário AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) move os dados para o Armazenamento do Microsoft Azure pela internet pública. Isso funciona se os tamanhos dos seus dados forem inferiores a 10 TB. Para executar cargas regularmente com AZCopy, teste a velocidade da rede para ver se ela é aceitável.
- O [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tem um gateway que você pode instalar no seu servidor local. Em seguida, você pode criar um pipeline para mover os dados do seu servidor local para o Armazenamento do Microsoft Azure. Para usar Data Factory com pools SQL dedicados, consulte [carregando dados para pools de SQL dedicados](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para o carregamento

Você pode precisar preparar e limpar os dados na sua conta de armazenamento antes de carregá-los. A preparação de dados pode ser executada enquanto seus dados estiverem na origem, conforme você exporta os dados para arquivos de texto ou após os dados no Armazenamento do Microsoft Azure.  É mais fácil trabalhar com os dados o mais precocemente possível no processo.  

### <a name="define-the-tables"></a>Definir as tabelas

Você deve primeiro definir as tabelas que está carregando em seu pool SQL dedicado ao usar a instrução de cópia.

Se você estiver usando o polybase, precisará definir tabelas externas em seu pool SQL dedicado antes do carregamento. O PolyBase usa tabelas externas para definir e acessar os dados no Armazenamento do Microsoft Azure. Uma tabela externa é semelhante a uma exibição de banco de dados. A tabela externa contém o esquema de tabela e aponta para os dados que são armazenados fora do pool SQL dedicado.

Definir tabelas externas envolve a especificação da fonte de dados, o formato dos arquivos de texto e as definições de tabela. Os artigos de referência de sintaxe do T-SQL que serão necessários são:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Use o seguinte mapeamento de tipo de dados SQL ao carregar arquivos parquet:

|                         Tipo do Parquet                         |   Tipo lógico do Parquet (anotação)   |  Tipo de dados SQL   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      FLOAT       |
|                            FLOAT                             |                                       |       real       |
|                            INT32                             |                                       |       INT        |
|                            INT64                             |                                       |      BIGINT      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINARY                            |                 UTF8                  |     NVARCHAR     |
|                            BINARY                            |                STRING                 |     NVARCHAR     |
|                            BINARY                            |                 ENUM                  |     NVARCHAR     |
|                            BINARY                            |                 UUID                  | UNIQUEIDENTIFIER |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     SMALLINT     |
|                            INT32                             |            INT(16, true)            |     SMALLINT     |
|                            INT32                             |             INT(32, true)             |       INT        |
|                            INT32                             |            INT(8, false)            |     TINYINT      |
|                            INT32                             |            INT(16, false)             |       INT        |
|                            INT32                             |           INT(32, false)            |      BIGINT      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME (MILLIS)             |       time       |
|                            INT64                             |            INT(64, true)            |      BIGINT      |
|                            INT64                             |           INT(64, false )            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME (MILLIS)                 |       time       |
|                            INT64                             | TIMESTAMP (MILIS)                  |    datetime2     |
| [Tipo complexo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 LISTA                  |   varchar(max)   |
| [Tipo complexo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |

>[!IMPORTANT] 
>- Atualmente, os pools do SQL dedicados não dão suporte a tipos de dados parquet com a precisão MICROs e NANOs. 
>- Você poderá enfrentar o seguinte erro se os tipos não corresponderem entre parquet e SQL ou se você tiver tipos de dados parquet sem suporte: **"HdfsBridge:: recordReaderFillBuffer-erro inesperado encontrado ao preencher buffer de leitor de registro: ClassCastException:..."**
>- Não há suporte para o carregamento de um valor fora do intervalo de 0-127 em uma coluna tinyint para o formato de arquivo parquet e ORC.

Para obter um exemplo de criação de objetos externos, consulte [criar tabelas externas](../sql/develop-tables-external-tables.md?tabs=sql-pool).

### <a name="format-text-files"></a>Arquivos de texto formatados

Se você está usando o PolyBase, os objetos externos definidos precisam alinhar as linhas dos arquivos de texto com a tabela externa e a definição de formato de arquivo. Os dados em cada linha do arquivo de texto devem se alinhar com a definição da tabela.
Para formatar os arquivos de texto:

- Se os seus dados forem provenientes de uma fonte não relacional, você precisa transformá-los em linhas e colunas. Se os dados forem de uma fonte relacional ou não, os dados devem ser transformados para se alinharem com as definições de coluna para a tabela na qual você planeja carregar os dados.
- Formatar dados no arquivo de texto para se alinharem aos tipos de dados e colunas na tabela de destino. O desalinhamento entre os tipos de dados nos arquivos de texto externos e a tabela de pools SQL dedicada faz com que as linhas sejam rejeitadas durante a carga.
- Separar os campos no arquivo de texto com um terminador.  Certifique-se de usar um caractere ou uma sequência de caracteres que não sejam encontrados nos seus dados de origem. Use o terminador especificado com [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Carregar os dados usando o PolyBase ou a instrução COPY

É uma melhor prática carregar dados em uma tabela de preparo. Tabelas de preparo permitem manipular erros sem interferir nas tabelas de produção. Uma tabela de preparo também oferece a oportunidade de usar a arquitetura de processamento paralelo do pool do SQL dedicado para transformações de dados antes de inserir os dados em tabelas de produção.

### <a name="options-for-loading"></a>Opções de carregamento

Para carregar dados, você pode usar qualquer uma dessas opções de carregamento:

- A [instrução de cópia](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) é o utilitário de carregamento recomendado, pois permite que você carregue dados de forma direta e flexível. A instrução tem muitos recursos de carregamento adicionais que o polybase não fornece. 
- O [polybase com o T-SQL](./load-data-from-azure-blob-storage-using-copy.md) exige que você defina objetos de dados externos.
- [O PolyBase e a instrução COPY com o ADF (Azure Data Factory)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) é outra ferramenta de orquestração.  Ele define um pipeline e agenda de trabalhos.
- O [polybase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) funciona bem quando os dados de origem estão em SQL Server. O SSIS define a origem para mapeamentos de tabela de destino e também coordena a carga. Se você já tiver pacotes SSIS, você pode modificar os pacotes para trabalhar com o novo destino do data warehouse.
- O [PolyBase com Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) transfere dados de uma tabela para um dataframe do Databricks e/ou grava os dados de um dataframe do Databricks em uma tabela usando o PolyBase.

### <a name="other-loading-options"></a>Outras opções de carregamento

Além do PolyBase e da instrução COPY, você pode usar [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ou a [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). O bcp é carregado diretamente para o banco de dados sem passar pelo Armazenamento de Blobs do Azure e é destinado somente para pequenas cargas.

> [!NOTE]
> O desempenho de carga dessas opções é mais lento do que o PolyBase e a instrução COPY.

## <a name="5-transform-the-data"></a>5. Transformar os dados

Enquanto os dados estão na tabela de preparo, execute as transformações que a sua carga de trabalho exige. Em seguida, mova os dados para uma tabela de produção.

## <a name="6-insert-the-data-into-production-tables"></a>6. Inserir os dados nas tabelas de produção

O INSERT INTO... A Instrução SELECT move os dados da tabela de preparo para a tabela permanente.

Ao criar um processo de ETL, tente executar o processo em uma amostra de teste pequena. Tente extrair 1000 linhas da tabela para um arquivo, movê-lo para o Azure e, em seguida, tente carregá-lo em uma tabela de preparo.

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos de nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos nossos [parceiros de solução](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Próximas etapas

Para orientações sobre carregamento, consulte [Diretrizes para carregar dados](guidance-for-loading-data.md).