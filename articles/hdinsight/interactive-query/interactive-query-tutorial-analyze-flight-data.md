---
title: 'Tutorial: Realizar operações de ETL (extração, transformação e carregamento) usando a Consulta Interativa no Azure HDInsight'
description: Tutorial - Saiba como extrair dados de um conjunto de dados CSV brutos, transformá-los usando a Consulta Interativa no HDInsight e, em seguida, carregar os dados transformados no banco de dados SQL do Azure usando o Apache Sqoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 07/02/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: fbab8502c088c2ae7a4b8e87285d7e4cac1de4c0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807395"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando a Consulta Interativa no Azure HDInsight

Neste tutorial, você pega um arquivo de dados CSV brutos de dados de voo publicamente disponíveis, importa-o em um armazenamento de cluster do HDInsight e, em seguida, transforma os dados usando a Consulta Interativa no Azure HDInsight. Depois que os dados são transformados, você carrega esses dados em um banco de dados SQL do Azure usando o [Apache Sqoop](https://sqoop.apache.org/).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Fazer download dos dados de voo de exemplo
> * Fazer upload de dados para um cluster do HDInsight
> * Transformar os dados usando a consulta interativa
> * Criar uma tabela em um Banco de Dados SQL do Azure
> * Usar Sqoop para exportar dados para um Banco de Dados SQL do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de Consulta interativa no HDInsight. Veja [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Consulta Interativa** como **tipo de Cluster**.

* Um banco de dados SQL do Azure. Você usa um banco de dados SQL do Azure como um repositório de dados de destino. Se você não tiver um Banco de Dados SQL, consulte [Criar um Banco de Dados SQL do Azure no Portal do Azure](/azure/sql-database/sql-database-single-database-get-started).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Baixar os dados de voos

1. Navegue até [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na página, limpe todos os campos e, em seguida, selecione os seguintes valores:

   | NOME | Valor |
   | --- | --- |
   | Filtrar por ano |2019 |
   | Filtrar por período |Janeiro |
   | Campos |Ano, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

3. Selecione **Baixar**. Você obtém um arquivo .zip com os campos de dados selecionados.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Fazer upload de dados para um cluster do HDInsight

Há muitas maneiras de carregar dados para o armazenamento associado a um cluster do HDInsight. Nesta seção, você deve usar o `scp` para carregar dados. Para saber mais sobre outras maneiras de carregar dados, consulte [Carregar dados para o HDInsight](../hdinsight-upload-data.md).

1. Carregue o arquivo zip para o nó principal do cluster do HDInsight. Edite o comando abaixo, substituindo `FILENAME` pelo nome do arquivo zip e `CLUSTERNAME` pelo nome do cluster do HDInsight. Em seguida, abra um prompt de comando, defina seu diretório de trabalho para a localização do arquivo e, em seguida, digite o comando.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Se for solicitado que você insira sim ou não para continuar, digite yes no prompt de comando e pressione Enter. O texto não fica visível na janela conforme você digita.

2. Após o upload ser concluído, conecte-se ao cluster usando SSH. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do cluster do HDInsight. Em seguida, digite o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Defina a variável de ambiente depois que uma conexão SSH tiver sido estabelecida. Substitua `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER` e `SQL_PASWORD` pelos valores apropriados. Em seguida, digite o comando:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Descompacte o arquivo zip, digitando o comando abaixo:

    ```bash
    unzip $FILENAME.zip
    ```

5. Use o comando abaixo para criar um diretório no armazenamento do HDInsight e, em seguida, copie o arquivo .csv para o diretório:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformar dados usando uma consulta do Hive

Há muitas maneiras de executar um trabalho do Hive em um cluster do HDInsight. Nesta seção, você usa o [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) para executar um trabalho do Hive. Para obter informações sobre outros métodos de execução de um trabalho do Hive, confira [Usar o Apache Hive no HDInsight](../hadoop/hdinsight-use-hive.md).

Como parte do trabalho do Hive, importe os dados do arquivo. csv em uma tabela do Hive nomeada **Delays**.

1. No prompt de SSH já existente para o cluster HDInsight, use o seguinte comando para criar e editar um novo arquivo denominado **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Use o seguinte texto como o conteúdo deste arquivo:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Pressione **Ctrl + X** e pressione **Y** para salvar o arquivo.

4. Use o seguinte comando para iniciar o Hive e executar o arquivo **flightdelays.hql**:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Após o término da execução do script **flightdelays.hql**, use o comando a seguir para abrir uma sessão interativa de Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Quando você receber o prompt do `jdbc:hive2://localhost:10001/>`, use a consulta a seguir para recuperar dados usando os dados importados de voos atrasados:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Essa consulta recupera uma lista de cidades em que houve atrasos causados pelo clima, além do tempo médio de atrasos e a salva em `/tutorials/flightdelays/output`. Posteriormente, o Sqoop lê os dados desse local e os exporta para o Banco de Dados SQL do Azure.

7. Para sair do Beeline, digite `!quit` no prompt.

## <a name="create-a-sql-database-table"></a>Criar uma tabela do Banco de Dados SQL

Há várias maneiras de se conectar ao Banco de Dados SQL e criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.

1. Para instalar o FreeTDS, use o seguinte comando de uma conexão Open SSH para o cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Após a conclusão da instalação, use o comando a seguir para conectar-se ao servidor do Banco de Dados SQL.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Você receberá saídas semelhantes ao seguinte texto:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Ao prompt `1>`, insira o seguinte:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas. Essa instrução cria uma tabela chamada **atrasos**, com um índice clusterizado.

    Use a seguinte consulta para verificar se a tabela foi criada:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    A saída é semelhante ao texto a seguir:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Enter `exit` at the `1>`.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Exportar dados para o banco de dados SQL usando Apache Sqoop

Nas seções anteriores, você copiou os dados transformados em `/tutorials/flightdelays/output`. Nesta seção, você pode usar o Sqoop para exportar os dados de `/tutorials/flightdelays/output` para a tabela criada no banco de dados SQL do Azure.

1. Use o comando abaixo para verificar se o Sqoop pode ver seu Banco de Dados SQL:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Esse comando retorna uma lista de bancos de dados, incluindo o banco de dados no qual você criou a tabela `delays` anteriormente.

2. Exportar os dados do `/tutorials/flightdelays/output` para a tabela `delays` digitando o comando a seguir:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    O Sqoop conecta-se ao banco de dados que contém a tabela `delays` e exporta os dados do diretório `/tutorials/flightdelays/output` para a tabela `delays`.

3. Depois que o comando sqoop for concluído, inserindo o comando abaixo, use o utilitário tsql para se conectar ao banco de dados:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Use as instruções a seguir para verificar se os dados foram exportados para a tabela de atrasos:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Você deve ver uma listagem dos dados na tabela. A tabela inclui o nome da cidade e o tempo de atraso de voo médio dessa cidade. 

    Digite `exit` para sair do utilitário tsql.

## <a name="clean-up-resources"></a>Limpar recursos

Após concluir o tutorial, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Para excluir um cluster, confira [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você pegou um arquivo de dados CSV brutos, importou-o em um armazenamento de cluster do HDInsight e, em seguida, transformou os dados usando a Consulta Interativa no Azure HDInsight.  Avance para o próximo tutorial para saber mais sobre o Apache Hive Warehouse Connector.

> [!div class="nextstepaction"]
>[Integrar o Apache Spark e Apache Hive ao Hive Warehouse Connector](./apache-hive-warehouse-connector.md)
