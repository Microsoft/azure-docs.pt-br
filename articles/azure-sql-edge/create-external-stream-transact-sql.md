---
title: CRIAR fluxo externo (Transact-SQL)-Azure SQL Edge
description: Saiba mais sobre a instrução CREATE EXTERNAL STREAM no Azure SQL Edge
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659215"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

O objeto de fluxo externo tem uma finalidade dupla de um fluxo de entrada e de saída. Ele pode ser usado como uma entrada para consultar dados de streaming de serviços de ingestão de eventos, como o Hub de eventos do Azure, o Hub IoT do Azure (ou Hub do Edge) ou Kafka ou pode ser usado como uma saída para especificar onde e como armazenar os resultados de uma consulta de streaming.

Um EXTERNAL STREAM também pode ser especificado e criado como uma saída e uma entrada para serviços como o hub de eventos ou o armazenamento de blobs. Isso facilita os cenários de encadeamento em que uma consulta de streaming está persistindo os resultados para o fluxo externo como saída e outra consulta de streaming com a leitura do mesmo fluxo externo como entrada.

O SQL do Azure no Edge atualmente oferece suporte apenas às fontes de dados a seguir como entradas e saídas de fluxo.

| Tipo de fonte de dados | Entrada | Saída | Descrição |
|------------------|-------|--------|------------------|
| Hub de Azure IoT Edge | Y | Y | Fonte de dados para ler e gravar dados de streaming em um hub de Azure IoT Edge. Para obter mais informações, consulte [IOT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| Banco de Dados SQL | N | S | Conexão de fonte de dados para gravar dados de streaming no banco de dado SQL. O banco de dados pode ser um banco de dados local no Azure SQL Edge ou um banco de dados remoto no SQL Server ou no banco de dados SQL do Azure.|
| Kafka | S | N | Fonte de dados para ler dados de streaming de um tópico Kafka. O suporte a Kafka não está disponível para a versão ARM64 do Azure SQL Edge.|



## <a name="syntax"></a>Sintaxe

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argumentos

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**: Especifica o nome para os dados reais ou o local na fonte de dados. 
   - Para objetos do Hub do Edge ou do Kafka Stream, Location especifica o nome do Hub do Edge ou do tópico Kafka para leitura ou gravação.
   - Para objetos do SQL Stream (SQL Server, banco de dados SQL do Azure ou Azure SQL Edge), o local especifica o nome da tabela. Se o fluxo for criado no mesmo banco de dados e esquema que a tabela de destino, apenas o nome da tabela será suficiente. Caso contrário, você precisa qualificar totalmente (<database_name. schema_name. table_name) o nome da tabela.
   - Para local do objeto de fluxo do armazenamento de BLOBs do Azure refere-se ao padrão de caminho a ser usado dentro do contêiner de BLOB. Para obter mais informações sobre esse recurso, consulte (/articles/Stream-Analytics/Stream-Analytics-define-Outputs.MD # BLOB-Storage-and-Azure-data-Lake-Gen2)

- **INPUT_OPTIONS**: especificar opções como pares de chave-valor para serviços como Kafka, IOT Edge Hub que são entradas para streaming de consultas
    - PARTIÇÕES: número de partições definidas para um tópico. O número máximo de partições que podem ser usadas é limitado a 32.
      - Aplica-se a fluxos de entrada Kafka
    - CONSUMER_GROUP: Os hubs IoT e de eventos limitam a 5 o número de leitores em um grupo de consumidores. Deixar esse campo vazio usará o grupo de consumidores '$Default'.
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.  
    - TIME_POLICY: Descreve se deve-se descartar eventos ou ajustar a hora do evento quando eventos atrasados ou fora de ordem passam o valor de tolerância.
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: O atraso de tempo máximo aceitável. O atraso representa a diferença entre o carimbo de data/hora do evento e o relógio do sistema.
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: Os eventos podem chegar fora de ordem depois de terem feito a viagem da entrada para a consulta de streaming. Esses eventos podem ser aceitos como estão, ou você pode optar por pausar por um período definido para reordená-los.
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
        
- **OUTPUT_OPTIONS**: Especificar opções como pares chave-valor para serviços com suporte que são saídas para consultas de streaming 
  - REJECT_POLICY:  DROP | RETRY Especifica as políticas de tratamento de erro de dados quando ocorrerem erros de conversão de dados. 
    - Aplica-se a todas as saídas com suporte 
  - MINIMUM_ROWS:  
    Mínimo de linhas necessárias por lote gravadas em uma saída. Para o Parquet, cada lote criará um novo arquivo. 
    - Aplica-se a todas as saídas com suporte 
  - MAXIMUM_TIME:  
    Tempo de espera máximo em minutos por lote. Após esse período, o lote será gravado na saída mesmo que o requisito de linhas mínimas não seja atendido. 
    - Aplica-se a todas as saídas com suporte 
  - PARTITION_KEY_COLUMN:  
    Coluna usada para a chave de partição. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    Uma lista de nomes de colunas de saída separados por vírgula que serão anexados a mensagens como propriedades personalizadas, se fornecidos.  
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Uma coleção de pares de nome/valor de nomes de Propriedades do Sistema e colunas de saída formatadas como JSON a serem preenchidas nas mensagens de Barramento de Serviço. Por exemplo, { "MessageId":   "column1", "PartitionKey": "column2"} 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge. 
  - PARTITION_KEY:  
    O nome da coluna de saída que contém a chave da partição. A chave de partição é um identificador exclusivo para a partição em uma determinada tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
  - ROW_KEY:  
    O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade em uma determinada partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
  - BATCH_SIZE:  
    Isso representa o número de transações para o armazenamento de tabelas em que o máximo pode ir até 100 registros. Para o Azure Functions, isso representa o tamanho do lote em bytes enviados para a função por chamada. O padrão é 256 KB. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    Número máximo de eventos enviados para a função por chamada para o Azure Function. O padrão é 100. Para o Banco de Dados SQL do Azure, isso representa o número máximo de registros enviados com cada transação de inserção em massa. O padrão é 10.000. 
    - Aplica-se a todas as saídas baseadas em SQL 
  - STAGING_AREA: objeto de fonte de dados externa ao armazenamento de BLOBs na área de preparação para a ingestão de dados de alta taxa de transferência no Azure Synapse Analytics 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.


## <a name="examples"></a>Exemplos

### <a name="example-1---edgehub"></a>Exemplo 1 – Hub do Edge

Tipo: Entrada ou Saída<br>

Sintaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exemplo 2 – Banco de Dados SQL do Azure, SQL do Azure no Edge, SQL Server

Tipo: Saída<br>

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Exemplo 3 – Kafka

Tipo: Entrada<br>

Sintaxe:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Confira também

- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md)
