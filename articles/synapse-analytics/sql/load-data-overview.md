---
title: Criar uma estratégia de carregamento de dados do polybase para o pool SQL dedicado
description: Em vez de ETL, crie um processo ELT (extração, carregamento e transformação) para carregar dados com SQL dedicado.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a89fa72db3deaec12a9073233f861aa6835288a5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678348"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Criar uma estratégia de carregamento de dados do polybase para o pool SQL dedicado no Azure Synapse Analytics

Os data warehouses SMP tradicionais usam um processo ETL (extração, transformação e carregamento) para carregar dados. O pool SQL do Azure é uma arquitetura MPP (processamento paralelo maciço) que aproveita a escalabilidade e a flexibilidade dos recursos de computação e armazenamento. O uso de um processo ELT (extração, carregamento e transformação) pode aproveitar os recursos internos de processamento de consultas distribuídas e eliminar os recursos necessários para transformar os dados antes do carregamento.

Embora o pool do SQL dê suporte a muitos métodos de carregamento, incluindo opções não polybase, como BCP e API do SQL BulkCopy, a maneira mais rápida e escalonável de carregar a data é por meio do polybase.  O PolyBase é uma tecnologia que acessa dados externos armazenados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage por meio da linguagem T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extração, carregamento e transformação (ELT)

O ELT (extração, carregamento e transformação) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados em um data warehouse e, em seguida, transformados.

As etapas básicas para implementar um polybase para pool de SQL dedicado são:

1. Extrair os dados de origem em arquivos de texto.
2. Descarregar os dados no armazenamento de Blobs do Azure ou no Azure Data Lake Store.
3. Preparar os dados para o carregamento.
4. Carregue os dados em tabelas de preparo do pool SQL dedicado usando o polybase.
5. Transformar os dados.
6. Inserir os dados nas tabelas de produção.

Para obter um tutorial de carregamento, consulte [usar o polybase para carregar dados do armazenamento de BLOBs do Azure para o Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Para obter mais informações, consulte [Blog de padrão de carga](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em arquivos de texto

Obter dados de fora do seu sistema de origem depende da localização de armazenamento.  A meta é mover os dados para os arquivos de texto delimitados compatíveis com PolyBase.

### <a name="polybase-external-file-formats"></a>Formatos de arquivos externos do PolyBase

O PolyBase carrega dados de arquivos de texto delimitados e codificados de UTF-8 e UTF-16. Além dos arquivos de texto delimitados, ele carrega os formatos de arquivo do Hadoop, o arquivo RC, ORC e Parquet. O PolyBase também pode carregar dados de arquivos compactados Gzip e Snappy. Atualmente, o PolyBase não suporta ASCII estendido, formato de largura fixa, e formatos aninhados, como XML, JSON e WinZip.

Se você estiver exportando do SQL Server, poderá usar a [ferramenta de linha de comando bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para exportar os dados para arquivos de texto delimitados. O mapeamento de tipo de dados parquet para o Azure Synapse Analytics é o seguinte:

| **Tipo de dados parquet** |                      **Tipo de dados SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        TINYINT        |                           TINYINT                            |
|       SMALLINT        |                           SMALLINT                           |
|          INT          |                             INT                              |
|        BIGINT         |                            BIGINT                            |
|        booleano        |                             bit                              |
|        double         |                            FLOAT                             |
|         FLOAT         |                             real                             |
|        double         |                            money                             |
|        double         |                          SMALLMONEY                          |
|        string         |                            NCHAR                             |
|        string         |                           NVARCHAR                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|        timestamp       |                             date                             |
|        timestamp       |                        smalldatetime                         |
|        timestamp       |                          datetime2                           |
|        timestamp       |                           DATETIME                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Descarregar os dados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage

Para descarregar dados para o armazenamento do Azure, você pode movê-los para o [armazenamento de Blobs do Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou [Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Em qualquer localização, os dados devem ser armazenados em arquivos de texto. O PolyBase pode carregar dessas localizações.

Ferramentas e serviços que você pode usar para mover dados para o Armazenamento do Microsoft Azure:

- O serviço [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) melhora a taxa de transferência de rede, o desempenho e a previsibilidade. O ExpressRoute é um serviço que encaminha os dados por uma conexão privada dedicada para o Azure. As conexões do ExpressRoute não encaminham dados pela Internet pública. As conexões oferecem mais confiabilidade e velocidade, latências menores e maior segurança do que as conexões comuns pela Internet.
- O [Utilitário AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) move os dados para o Armazenamento do Microsoft Azure pela internet pública. Isso funciona se os tamanhos dos seus dados forem inferiores a 10 TB. Para executar cargas regularmente com AZCopy, teste a velocidade da rede para ver se ela é aceitável.
- O [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) tem um gateway que você pode instalar no seu servidor local. Em seguida, você pode criar um pipeline para mover os dados do seu servidor local para o Armazenamento do Microsoft Azure. Para usar Data Factory com o pool SQL dedicado, consulte [carregar dados no pool SQL dedicado](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para o carregamento

Talvez seja necessário preparar e limpar os dados em sua conta de armazenamento antes de carregá-los no pool SQL dedicado. A preparação de dados pode ser executada enquanto seus dados estiverem na origem, conforme você exporta os dados para arquivos de texto ou após os dados no Armazenamento do Microsoft Azure.  É mais fácil trabalhar com os dados o mais precocemente possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas

Antes que você possa carregar os seus dados, você precisa definir tabelas externas no seu data warehouse. O PolyBase usa tabelas externas para definir e acessar os dados no Armazenamento do Microsoft Azure. Uma tabela externa é semelhante a uma exibição de banco de dados. A tabela externa contém o esquema de tabela e aponta para os dados armazenados fora do data warehouse.

Definir tabelas externas envolve a especificação da fonte de dados, o formato dos arquivos de texto e as definições de tabela. O que vem a seguir são os tópicos de sintaxe T-SQL que você precisará:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Arquivos de texto formatados

Depois que os objetos externos são definidos, você precisa alinhar as linhas dos arquivos de texto com a tabela externa e a definição de formato de arquivo. Os dados em cada linha do arquivo de texto devem se alinhar com a definição da tabela.
Para formatar os arquivos de texto:

- Se os seus dados forem provenientes de uma fonte não relacional, você precisa transformá-los em linhas e colunas. Se os dados forem de uma fonte relacional ou não, os dados devem ser transformados para se alinharem com as definições de coluna para a tabela na qual você planeja carregar os dados.
- Formate os dados no arquivo de texto para alinhá-los com as colunas e os tipos de dados na tabela de destino do pool do SQL. O desalinhamento entre os tipos de dados nos arquivos de texto externos e a tabela do data warehouse faz com que as linhas a sejam rejeitadas durante o carregamento.
- Separar os campos no arquivo de texto com um terminador.  Certifique-se de usar um caractere ou uma sequência de caracteres que não sejam encontrados na fonte de dados. Use o terminador especificado com [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. carregar os dados em tabelas de preparo do pool do SQL dedicado usando o polybase

É uma melhor prática carregar dados em uma tabela de preparo. Tabelas de preparo permitem manipular erros sem interferir nas tabelas de produção. Uma tabela de preparo também oferece a oportunidade de usar recursos de processamento de consulta distribuída internos do pool do SQL para transformações de dados antes de inserir os dados em tabelas de produção.

### <a name="options-for-loading-with-polybase"></a>Opções de carregamento com PolyBase

Para carregar dados com o PolyBase, é possível usar qualquer uma destas opções de carregamento:

- O [PolyBase com o T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) funciona bem quando os seus dados estiverem no armazenamento de Blobs do Azure ou no Azure Data Lake Store. Ele oferece mais controle sobre o processo de carregamento, mas também exige que você defina objetos de dados externos. Os outros métodos definem esses objetos em segundo plano, como mapear as tabelas de origem para as tabelas de destino.  Para coordenar as cargas de T-SQL, você pode usar o Azure Data Factory, SSIS ou as funções do Azure.
- O [polybase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) funciona bem quando os dados de origem estão em SQL Server. O SSIS define a origem para mapeamentos de tabela de destino e também coordena a carga. Se você já tiver pacotes SSIS, você pode modificar os pacotes para trabalhar com o novo destino do data warehouse.
- O [PolyBase com o Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) é outra ferramenta de orquestração.  Ele define um pipeline e agenda de trabalhos.
- O [polybase com Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) transfere dados de uma tabela do Azure Synapse Analytics para um dataframe do databricks e/ou grava dados de um dataframe do databricks em uma tabela do Azure Synapse Analytics usando o polybase.

### <a name="non-polybase-loading-options"></a>Opções de carregamento que não sejam PolyBase

Se os seus dados não forem compatíveis com o PolyBase, você pode usar [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ou a [API do SQLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). o bcp é carregado diretamente no pool do SQL dedicado sem passar pelo armazenamento de BLOBs do Azure e destina-se apenas a pequenas cargas. Note que o desempenho do carregamento dessas opções é significativamente mais lento do que o PolyBase.

## <a name="5-transform-the-data"></a>5. Transformar os dados

Enquanto os dados estão na tabela de preparo, execute as transformações que a sua carga de trabalho exige. Em seguida, mova os dados para uma tabela de produção.

## <a name="6-insert-the-data-into-production-tables"></a>6. Inserir os dados nas tabelas de produção

O INSERT INTO... A Instrução SELECT move os dados da tabela de preparo para a tabela permanente.

Ao criar um processo de ETL, tente executar o processo em uma amostra de teste pequena. Tente extrair 1000 linhas da tabela para um arquivo, movê-lo para o Azure e, em seguida, tente carregá-lo em uma tabela de preparo.

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos de nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos nossos [parceiros de solução](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Próximas etapas

Para orientações sobre carregamento, consulte as [Diretrizes para carregar dados](data-loading-best-practices.md).