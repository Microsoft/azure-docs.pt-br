---
title: "Analisar dados de atraso de voo com o Hive no HDInsight – Azure | Microsoft Docs"
description: "Saiba como usar o Hive para analisar dados de voos usando no HDInsight baseado em Linux e então exporte os dados para um Banco de Dados SQL do Azure usando o Sqoop."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f333354311b16c00a0d43a691f139f5f80383d1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analisar dados de atraso de voo usando o Hive no HDInsight baseado em Linux

Saiba como analisar dados de atraso de voos usando o Hive no HDInsight baseado em Linux e como exportar os dados para um Banco de Dados SQL do Azure usando o Sqoop.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no Azure HDInsight versão 3.4 ou posterior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster HDInsight**. Confira [Introdução ao uso do Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) para obter as etapas da criação de um novo cluster HDInsight baseado em Linux.

* **Banco de dados SQL do Azure**. Você usa um banco de dados SQL do Azure como um repositório de dados de destino. Se você não tiver um Banco de Dados SQL, consulte [Criar um Banco de Dados SQL do Azure no Portal do Azure](../sql-database/sql-database-get-started.md).

* **CLI do Azure**. Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure 1.0](../cli-install-nodejs.md) para obter mais etapas.

## <a name="download-the-flight-data"></a>Baixar os dados de voos

1. Navegue para [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. Na página, selecione os valores a seguir:

   | Nome | Valor |
   | --- | --- |
   | Filtrar por ano |2013 |
   | Filtrar por período |Janeiro |
   | Campos |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Limpe todos os outros campos. 

3. Selecione **Baixar**.

## <a name="upload-the-data"></a>Carregar os dados

1. Use o comando a seguir para carregar o arquivo zip no nó de cabeçalho do cluster HDInsight:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Substitua *NOME DO ARQUIVO* pelo nome do arquivo .zip. Substitua *NOMEDEUSUÁRIO* pelo logon SSH para o cluster HDInsight. Substitua *NOMEDOCLUSTER* pelo nome do cluster HDInsight.

   > [!NOTE]
   > Se você usar uma senha para autenticar seu logon SSH, a senha será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` e especificar a chave privada correspondente. Por exemplo: `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Após o upload ser concluído, conecte-se ao cluster usando SSH:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Para obter mais informações, consulte [Conectar ao HDInsight (Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Use o comando a seguir para descompactar o arquivo .zip:

    ```
    unzip FILENAME.zip
    ```

    Este comando extrai um arquivo .csv com aproximadamente 60 MB.

4. Use o seguinte comando para criar um diretório no armazenamento do HDInsight e, em seguida, copie o arquivo para o diretório:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Criar e executar o HiveQL

Use as etapas a seguir para importar dados do arquivo .csv para uma tabela do Hive chamada **Delays**.

1. Use o comando a seguir para criar e editar um novo arquivo chamado **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Use o seguinte texto como o conteúdo deste arquivo:

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

2. Use Ctrl + X seguido de Y para salvar o arquivo.

3. Use o seguinte comando para iniciar o Hive e executar o arquivo **flightdelays.hql**:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

   > [!NOTE]
   > Neste exemplo, `localhost` é usado, pois você está conectado ao nó de cabeçalho do cluster HDInsight, no qual o HiveServer2 está em execução.

4. Após o término da execução do script __flightdelays.hql__, use o comando a seguir para abrir uma sessão interativa de Beeline:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Quando você receber o prompt do `jdbc:hive2://localhost:10001/>`, use a consulta a seguir para recuperar dados usando os dados importados de voos atrasados:

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

6. Para sair do Beeline, digite `!quit` no prompt.

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Se você já tem um Banco de Dados SQL, deverá obter o nome do servidor. Para localizar o nome do servidor no [Portal do Azure](https://portal.azure.com), selecione **Bancos de Dados SQL** e, em seguida, filtre o nome do banco de dados que você deseja usar. O nome do servidor está listado na coluna **SERVIDOR** .

Se você ainda não tem um Banco de Dados SQL, use as informações em [Criar um banco de dados SQL no Portal do Azure](../sql-database/sql-database-get-started.md) para criar um. Salve o nome do servidor usado para o banco de dados.

## <a name="create-a-sql-database-table"></a>Criar uma tabela do Banco de Dados SQL

> [!NOTE]
> Há várias maneiras de se conectar ao Banco de Dados SQL e criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.


1. Use o SSH para conectar-se ao cluster HDInsight baseado em Linux e execute as etapas a seguir na sessão SSH.

2. Use o seguinte comando para instalar o FreeTDS:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Após a conclusão da instalação, use o comando a seguir para conectar-se ao servidor de Banco de Dados SQL. Substitua **serverName** pelo nome do servidor do Banco de Dados SQL. Substitua **adminLogin** e **adminPassword** pelo logon do Banco de Dados SQL. Substitua **databaseName** pelo nome do banco de dados.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Você receberá saídas semelhantes ao seguinte texto:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Ao prompt `1>` , insira o seguinte:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas. Essa consulta cria uma tabela chamada **atrasos**, com um índice clusterizado.

    Use a seguinte consulta para verificar se a tabela foi criada:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    A saída é semelhante ao texto a seguir:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Para sair do utilitário tsql, insira `exit` at the `1>` .

## <a name="export-data-with-sqoop"></a>Exportar dados com o Sqoop

1. Use o comando a seguir para verificar se o Sqoop pode ver seu Banco de Dados SQL:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Esse comando retorna uma lista de bancos de dados, incluindo o banco de dados no qual você criou a tabela de atrasos anteriormente.

2. Use o comando a seguir para exportar dados de hivesampletable para a tabela de atrasos:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    O Sqoop se conecta ao banco de dados que contém a tabela de atrasos e exporta dados do diretório `/tutorials/flightdelays/output` para a tabela de atrasos.

3. Depois que o comando sqoop for concluído, use o utilitário tsql para se conectar ao banco de dados:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Use as instruções a seguir para verificar se os dados foram exportados para a tabela de atrasos:

    ```
    SELECT * FROM delays
    GO
    ```

    Você deve ver uma listagem dos dados na tabela. Digite `exit` para sair do utilitário tsql.

## <a name="next-steps"></a>Próximas etapas

Para aprender mais formas de trabalhar usando dados no HDInsight, consulte os seguintes artigos:

* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
* [Use o Sqoop com o HDInsight][hdinsight-use-sqoop]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para Hadoop no HDInsight][hdinsight-develop-mapreduce]
* [Desenvolver programas MapReduce de streaming do Python para o HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
