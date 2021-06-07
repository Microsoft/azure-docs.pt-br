---
title: Criar um trabalho de streaming do T-SQL no Azure SQL Edge
description: Saiba mais sobre como criar Stream Analytics trabalhos no Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 97189fd7a232c2467981b23dc20da51ebef08252
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656335"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Criar um trabalho de streaming de dados no Azure SQL Edge 

Este artigo explica como criar um trabalho de streaming do T-SQL no Azure SQL Edge. Você cria os objetos de entrada e saída de fluxo externo e, em seguida, define a consulta do trabalho de streaming como parte da criação do trabalho de streaming.

## <a name="configure-the-external-stream-input-and-output-objects"></a>Configurar os objetos de entrada e saída de fluxo externo

O streaming T-SQL usa a funcionalidade de fonte de dados externa do SQL Server para definir as fontes de dados associadas às entradas e saídas do fluxo externo do trabalho de streaming. Use os seguintes comandos T-SQL para criar um objeto de entrada ou saída de fluxo externo:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Além disso, se o Azure SQL Edge, o SQL Server ou o banco de dados SQL do Azure for usado como um fluxo de saída, você precisará da [credencial criar escopo do banco de dados (Transact-SQL)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Esse comando T-SQL define as credenciais para acessar o banco de dados.

### <a name="supported-input-and-output-stream-data-sources"></a>Fontes de dados de fluxo de entrada e saída com suporte

O SQL do Azure no Edge atualmente oferece suporte apenas às fontes de dados a seguir como entradas e saídas de fluxo.

| Tipo de fonte de dados | Entrada | Saída | Descrição |
|------------------|-------|--------|------------------|
| Hub de Azure IoT Edge | Y | Y | Fonte de dados para ler e gravar dados de streaming em um hub de Azure IoT Edge. Para obter mais informações, consulte [IOT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| Banco de Dados SQL | N | S | Conexão de fonte de dados para gravar dados de streaming no banco de dado SQL. O banco de dados pode ser um banco de dados local no Azure SQL Edge ou um banco de dados remoto no SQL Server ou no banco de dados SQL do Azure.|
| Kafka | S | N | Fonte de dados para ler dados de streaming de um tópico Kafka. Atualmente, esse adaptador só está disponível para as versões Intel ou AMD do Azure SQL Edge. Ele não está disponível para a versão ARM64 do Azure SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exemplo: criar um objeto de entrada/saída de fluxo externo para Azure IoT Edge Hub

O exemplo a seguir cria um objeto de fluxo externo para Azure IoT Edge Hub. Para criar uma fonte de dados de entrada/saída de fluxo externo para Azure IoT Edge Hub, primeiro você precisa criar um formato de arquivo externo para o layout dos dados que estão sendo lidos ou gravados também.

1. Crie um formato de arquivo externo do tipo JSON.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Crie uma fonte de dados externa para Azure IoT Edge Hub. O script T-SQL a seguir cria uma conexão de fonte de dados com um hub IoT Edge que é executado no mesmo host do Docker que o Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Crie o objeto de fluxo externo para Azure IoT Edge Hub. O script T-SQL a seguir cria um objeto de fluxo para o Hub de IoT Edge. No caso de um objeto de fluxo de IoT Edge Hub, o parâmetro LOCATION é o nome do tópico do hub de IoT Edge ou do canal que está sendo lido ou gravado.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Exemplo: criar um objeto de fluxo externo para o banco de dados SQL do Azure

O exemplo a seguir cria um objeto de fluxo externo para o banco de dados local no Azure SQL Edge. 

1. Crie uma chave mestra no banco de dados. Isso é necessário para criptografar o segredo da credencial.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Crie uma credencial no escopo do banco de dados para acessar a fonte de SQL Server. O exemplo a seguir cria uma credencial para a fonte de dados externa, com IDENTITY = username e segredo = password.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Crie uma fonte de dados externa, usando CREATE EXTERNAL DATA SOURCE. O exemplo a seguir:

    * Cria uma fonte de dados externa chamada *LocalSQLOutput*.
    * Identifica a fonte de dados externa (LOCATION = '<vendor>://<server>[:<port>]'). No exemplo, ele aponta para uma instância local do Azure SQL Edge.
    * Usa a credencial criada anteriormente.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Crie o objeto de fluxo externo. O exemplo a seguir cria um objeto de fluxo externo apontando para uma tabela *dbo. TemperatureMeasurements*, no banco de dados *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Exemplo: criar um objeto de fluxo externo para Kafka

O exemplo a seguir cria um objeto de fluxo externo para o banco de dados local no Azure SQL Edge. Este exemplo pressupõe que o servidor Kafka está configurado para acesso anônimo. 

1. Crie uma fonte de dados externa, usando CREATE EXTERNAL DATA SOURCE. O exemplo a seguir:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Crie um formato de arquivo externo para a entrada Kafka. O exemplo a seguir criou um formato de arquivo JSON com compactação Gzip. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. Crie o objeto de fluxo externo. O exemplo a seguir cria um objeto de fluxo externo apontando para o tópico Kafka `*TemperatureMeasurement*` .

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Criar o trabalho de streaming e as consultas de streaming

Use o `sys.sp_create_streaming_job` procedimento armazenado do sistema para definir as consultas de streaming e criar o trabalho de streaming. O `sp_create_streaming_job` procedimento armazenado usa os seguintes parâmetros:

- `job_name`: O nome do trabalho de streaming. Os nomes de trabalhos de streaming são exclusivos em toda a instância.
- `statement`: [Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)instruções de consulta de streaming baseadas em linguagem de consulta.

O exemplo a seguir cria um trabalho de streaming simples com uma consulta de streaming. Essa consulta lê as entradas do hub de IoT Edge e grava `dbo.TemperatureMeasurements` no banco de dados.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

O exemplo a seguir cria um trabalho de streaming mais complexo com várias consultas diferentes. Essas consultas incluem uma que usa a `AnomalyDetection_ChangePoint` função interna para identificar anomalias nos dados de temperatura.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Iniciar, parar, remover e monitorar trabalhos de streaming

Para iniciar um trabalho de streaming no Azure SQL Edge, execute o `sys.sp_start_streaming_job` procedimento armazenado. O procedimento armazenado requer que o nome do trabalho de streaming seja iniciado, como entrada.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Para interromper um trabalho de streaming, execute o `sys.sp_stop_streaming_job` procedimento armazenado. O procedimento armazenado requer que o nome do trabalho de streaming seja interrompido, como entrada.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Para descartar (ou excluir) um trabalho de streaming, execute o `sys.sp_drop_streaming_job` procedimento armazenado. O procedimento armazenado exige que o nome do trabalho de streaming seja descartado, como entrada.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Para obter o status atual de um trabalho de streaming, execute o `sys.sp_get_streaming_job` procedimento armazenado. O procedimento armazenado exige que o nome do trabalho de streaming seja descartado, como entrada. Ele gera o nome e o status atual do trabalho de streaming.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

O trabalho de streaming pode ter um dos seguintes status:

| Status | Descrição |
|--------| ------------|
| Criado | O trabalho de streaming foi criado, mas ainda não foi iniciado. |
| Iniciando | O trabalho de fluxo está na fase inicial. |
| Ocioso | O trabalho de streaming está em execução, mas não há nenhuma entrada para processar. |
| Processando | O trabalho de streaming está em execução e processando as entradas. Indica um estado de integridade para o trabalho de fluxo. |
| Degradado | O trabalho de streaming está em execução, mas houve alguns erros não fatais durante o processamento de entrada. O trabalho de entrada continuará em execução, mas removerá as entradas que encontrar erros. |
| Parado | O trabalho de fluxo foi interrompido. |
| Com falha | Falha no trabalho de streaming. Em geral indica um erro fatal durante o processamento. |

## <a name="next-steps"></a>Próximas etapas

- [Exibir metadados associados a trabalhos de streaming no Azure SQL Edge](streaming-catalog-views.md) 
- [Criar um fluxo externo](create-external-stream-transact-sql.md)