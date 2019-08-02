---
title: Em vez de ETL, projetar ELT para SQL Data Warehouse do Azure | Microsoft Docs
description: Em vez de ETL, projete um processo de ELT (Extração, Carregamento e Transformação) para carregamento de dados ou SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 05/10/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fa688f40f8eb968f2c388601b387e4f584951a91
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595600"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Criando um estratégia de carregamento de dados PolyBase para o SQL Data Warehouse do Azure

Os tradicionais data warehouses SMP usam um processo ETL (Extrair, Transformar e Carregar) para carregar dados. O SQL Data Warehouse do Azure é uma arquitetura MPP (processamento paralelo maciço) que aproveita a escalabilidade e flexibilidade de recursos de computação e de armazenamento. A utilização de um processo ELT (Extrair, Carregar e Transformar) pode aproveitar o MPP e eliminar recursos necessários para transformar os dados antes do carregamento. Embora o SQL Data Warehouse seja compatível com muitos métodos de carregamento, incluindo opções não PolyBase como a API BCP e SQL BulkCopy, a maneira mais rápida e escalonável de carregar dados é por meio do PolyBase.  O PolyBase é uma tecnologia que acessa dados externos armazenados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage por meio da linguagem T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>O que é ELT?

ELT (Extrair, Carregar e Transformar) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados para um data warehouse e transformados. 

As etapas básicas para implementar um ELT PolyBase para SQL Data Warehouse são:

1. Extrair os dados de origem em arquivos de texto.
2. Descarregar os dados no armazenamento de Blobs do Azure ou no Azure Data Lake Store.
3. Preparar os dados para o carregamento.
4. Carregar os dados nas tabelas de preparo do SQL Data Warehouse usando o PolyBase. 
5. Transformar os dados.
6. Inserir os dados nas tabelas de produção.


Para ver um tutorial de carregamento, consulte [Usar o PolyBase para carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse do Azure](load-data-from-azure-blob-storage-using-polybase.md).

Para obter mais informações, consulte [Blog de padrão de carga](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em arquivos de texto

Obter dados de fora do seu sistema de origem depende da localização de armazenamento.  A meta é mover os dados para os arquivos de texto delimitados compatíveis com PolyBase. 

### <a name="polybase-external-file-formats"></a>Formatos de arquivos externos do PolyBase

O PolyBase carrega dados de arquivos de texto delimitados e codificados de UTF-8 e UTF-16. Além dos arquivos de texto delimitados, ele carrega os formatos de arquivo do Hadoop, o arquivo RC, ORC e Parquet. O PolyBase também pode carregar dados de arquivos compactados Gzip e Snappy. Atualmente, o PolyBase não suporta ASCII estendido, formato de largura fixa, e formatos aninhados, como XML, JSON e WinZip. Se você estiver exportando do SQL Server, poderá usar a [ferramenta de linha de comando bcp](/sql/tools/bcp-utility) para exportar os dados para arquivos de texto delimitados. O Parquet para mapeamento de tipo de dados do SQL DW é o seguinte:

| **Tipo de dados parquet** |                      **Tipo de dados SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        cadeia de caracteres         |                            nchar                             |
|        cadeia de caracteres         |                           nvarchar                           |
|        cadeia de caracteres         |                             char                             |
|        cadeia de caracteres         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date        | 1) carga como int e convertido em data </br> 2) [usar o conector do SQL DW do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse) com </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**atualizar em breve**) |
|        decimal        | [Usar o conector do SQL DW do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse) com </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**atualizar em breve**) |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Descarregar os dados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage

Para descarregar dados para o armazenamento do Azure, você pode movê-los para o [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) ou [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md). Em qualquer localização, os dados devem ser armazenados em arquivos de texto. O PolyBase pode carregar dessas localizações.

Ferramentas e serviços que você pode usar para mover dados para o Armazenamento do Microsoft Azure:

- O serviço [Azure ExpressRoute](../expressroute/expressroute-introduction.md) melhora a taxa de transferência de rede, o desempenho e a previsibilidade. O ExpressRoute é um serviço que encaminha os dados por uma conexão privada dedicada para o Azure. As conexões do ExpressRoute não encaminham dados pela Internet pública. As conexões oferecem mais confiabilidade e velocidade, latências menores e maior segurança do que as conexões comuns pela Internet.
- O [Utilitário AZCopy](../storage/common/storage-moving-data.md) move os dados para o Armazenamento do Microsoft Azure pela internet pública. Isso funciona se os tamanhos dos seus dados forem inferiores a 10 TB. Para executar cargas regularmente com AZCopy, teste a velocidade da rede para ver se ela é aceitável. 
- O [Azure Data Factory (ADF)](../data-factory/introduction.md) tem um gateway que você pode instalar no seu servidor local. Em seguida, você pode criar um pipeline para mover os dados do seu servidor local para o Armazenamento do Microsoft Azure. Para usar o Data Factory com o SQL Data Warehouse, consulte [Carregar dados no SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para o carregamento

Você pode precisar preparar e limpar os dados na sua conta de armazenamento antes de carregá-los no SQL Data Warehouse. A preparação de dados pode ser executada enquanto seus dados estiverem na origem, conforme você exporta os dados para arquivos de texto ou após os dados no Armazenamento do Microsoft Azure.  É mais fácil trabalhar com os dados o mais precocemente possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas

Antes que você possa carregar os seus dados, você precisa definir tabelas externas no seu data warehouse. O PolyBase usa tabelas externas para definir e acessar os dados no Armazenamento do Microsoft Azure. Uma tabela externa é semelhante a uma exibição de banco de dados. A tabela externa contém o esquema de tabela e aponta para os dados armazenados fora do data warehouse. 

Definir tabelas externas envolve a especificação da fonte de dados, o formato dos arquivos de texto e as definições de tabela. Estes são os tópicos de sintaxe do T-SQL que você precisará:
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Para obter um exemplo de criação de objetos externos, consulte a etapa [Criar tabelas externas](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) no tutorial de carregamento.

### <a name="format-text-files"></a>Arquivos de texto formatados

Depois que os objetos externos são definidos, você precisa alinhar as linhas dos arquivos de texto com a tabela externa e a definição de formato de arquivo. Os dados em cada linha do arquivo de texto devem se alinhar com a definição da tabela.
Para formatar os arquivos de texto:

- Se os seus dados forem provenientes de uma fonte não relacional, você precisa transformá-los em linhas e colunas. Se os dados forem de uma fonte relacional ou não, os dados devem ser transformados para se alinharem com as definições de coluna para a tabela na qual você planeja carregar os dados. 
- Formatar dados no arquivo de texto para se alinharem aos tipos de dados e colunas na tabela de destino do SQL Data Warehouse. O desalinhamento entre os tipos de dados nos arquivos de texto externos e a tabela do data warehouse faz com que as linhas a sejam rejeitadas durante o carregamento.
- Separar os campos no arquivo de texto com um terminador.  Certifique-se de usar um caractere ou uma sequência de caracteres que não sejam encontrados na fonte de dados. Use o terminador especificado com [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Carregar os dados nas tabelas de preparo do SQL Data Warehouse usando o PolyBase

É uma melhor prática carregar dados em uma tabela de preparo. Tabelas de preparo permitem manipular erros sem interferir nas tabelas de produção. Uma tabela de preparo também oferece a oportunidade de usar o MPP do SQL Data Warehouse para transformações de dados antes da inserção deles em tabelas de produção.

### <a name="options-for-loading-with-polybase"></a>Opções de carregamento com PolyBase

Para carregar dados com o PolyBase, é possível usar qualquer uma destas opções de carregamento:

- O [PolyBase com o T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bem quando os seus dados estiverem no armazenamento de Blobs do Azure ou no Azure Data Lake Store. Ele oferece mais controle sobre o processo de carregamento, mas também exige que você defina objetos de dados externos. Os outros métodos definem esses objetos em segundo plano, como mapear as tabelas de origem para as tabelas de destino.  Para coordenar as cargas de T-SQL, você pode usar o Azure Data Factory, SSIS ou as funções do Azure. 
- O [PolyBase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funciona bem quando os seus dados de origem estiverem no SQL Server, no SQL Server local ou na nuvem. O SSIS define a origem para mapeamentos de tabela de destino e também coordena a carga. Se você já tiver pacotes SSIS, você pode modificar os pacotes para trabalhar com o novo destino do data warehouse. 
- O [PolyBase com o Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) é outra ferramenta de orquestração.  Ele define um pipeline e agenda de trabalhos. 
- [O PolyBase com o Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) transfere os dados de uma tabela do SQL Data Warehouse para um dataframe Databricks e/ou grava dados de um dataframe Databricks em uma tabela do SQL Data Warehouse usando PolyBase.

### <a name="non-polybase-loading-options"></a>Opções de carregamento que não sejam PolyBase

Se os seus dados não forem compatíveis com o PolyBase, você pode usar [bcp](/sql/tools/bcp-utility) ou a [API do SQLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). O bcp carrega diretamente para o SQL Data Warehouse sem passar pelo armazenamento de Blobs do Azure e é destinado somente para pequenas cargas. Note que o desempenho do carregamento dessas opções é significativamente mais lento do que o PolyBase. 


## <a name="5-transform-the-data"></a>5. Transformar os dados

Enquanto os dados estão na tabela de preparo, execute as transformações que a sua carga de trabalho exige. Em seguida, mova os dados para uma tabela de produção.


## <a name="6-insert-the-data-into-production-tables"></a>6. Inserir os dados nas tabelas de produção

O INSERT INTO... A Instrução SELECT move os dados da tabela de preparo para a tabela permanente. 

Ao criar um processo de ETL, tente executar o processo em uma amostra de teste pequena. Tente extrair 1000 linhas da tabela para um arquivo, movê-lo para o Azure e, em seguida, tente carregá-lo em uma tabela de preparo. 


## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos de nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos nossos [parceiros de solução](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Próximas etapas

Para orientações sobre carregamento, consulte as [Diretrizes para carregar dados](guidance-for-loading-data.md).


