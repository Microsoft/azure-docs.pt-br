---
title: 'Tutorial: realizar operações de ETL (extração, transformação e carregamento) usando o Apache Hive no Azure HDInsight'
description: Nesse tutorial você aprende como extrair dados de um conjunto de dados CSV bruto, transformá-los usando o Apache Hive no Azure HDInsight e, em seguida, carregar os dados transformados no Banco de Dados SQL do Azure usando o Sqoop.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f28cae7ed56b694f4194adf78c288ffa87eb71d8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447739"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando o Apache Hive no Azure HDInsight

Neste tutorial, você executa uma operação de ETL: extrair, transformar e carregar dados. Você obtém um arquivo de dados CSV bruto, importa-o em um cluster do Azure HDInsight, transforma-o com o Apache Hive e carrega-o em um Banco de Dados SQL do Azure com o Apache Sqoop.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * extrair e carregar os dados em um cluster do HDInsight.
> * transformar os dados usando o Apache Hive.
> * Carregar os dados em um Banco de Dados SQL do Azure usando o Sqoop.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta de armazenamento do Azure Data Lake Storage Gen2 configurada para HDInsight**

    Consulte [Usar o Azure Data Lake Storage Gen2 com clusters de HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Um cluster Hadoop baseado em Linux no HDInsight**

    Confira [Início Rápido: Introdução ao Apache Hadoop e ao Apache Hive no Azure HDInsight usando o portal do Azure](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Banco de Dados SQL do Azure**: Você usa um banco de dados SQL do Azure como um repositório de dados de destino. Se você não tiver um Banco de Dados SQL, consulte [Criar um Banco de Dados SQL do Azure no Portal do Azure](../../sql-database/sql-database-get-started.md).

* **CLI do Azure**: Se você ainda não instalou a CLI do Azure, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Um cliente do Secure Shell (SSH)** : Para obter mais informações, confira [Conectar-se ao HDInsight (Hadoop) usando SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Baixar os dados de voos

1. Navegue até [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na página, selecione os valores a seguir:

   | NOME | Valor |
   | --- | --- |
   | Filtrar por ano |2013 |
   | Filtrar por período |Janeiro |
   | Campos |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Limpe todos os outros campos.

3. Selecione **Baixar**. Você obtém um arquivo .zip com os campos de dados selecionados.

## <a name="extract-and-upload-the-data"></a>Extrair e carregar os dados

Nesta seção, você carregará dados no cluster HDInsight e, em seguida, vai copiá-los em sua conta do Data Lake Storage Gen2.

1. Abra um prompt de comando e use o seguinte comando Secure Copy (Scp) para carregar o arquivo .zip para o nó de cabeçalho do cluster HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Substitua o espaço reservado `<file-name>` pelo nome do arquivo .zip.
   * Substitua o espaço reservado `<ssh-user-name>` pelo logon SSH do cluster HDInsight.
   * Substitua o espaço reservado `<cluster-name>` pelo nome do cluster HDInsight.

   Se você usar uma senha para autenticar seu logon SSH, a senha será solicitada.

   Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` e especificar a chave privada correspondente. Por exemplo, `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Após o upload ser concluído, conecte-se ao cluster usando SSH. Insira o seguinte comando no prompt de comando:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Use o comando a seguir para descompactar o arquivo .zip:

   ```bash
   unzip <file-name>.zip
   ```

   O comando extrai um arquivo **.csv**.

4. Use o seguinte comando para criar o sistema de arquivos do Data Lake Storage Gen2.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Substitua o espaço reservado `<file-system-name>` pelo nome que você deseja dar ao sistema de arquivos.

   Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

5. Use o seguinte comando para criar um diretório.

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Use o seguinte comando para copiar o arquivo *.csv* para o diretório:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Use aspas em torno do nome do arquivo se ele contiver espaços ou caracteres especiais.

## <a name="transform-the-data"></a>Transformar os dados

Nesta seção, você usa o Beeline para executar um trabalho do Apache Hive.

Como parte do trabalho do Apache Hive, importe os dados do arquivo .csv para uma tabela do Apache Hive chamada **delays**.

1. No prompt de SSH já existente para o cluster HDInsight, use o seguinte comando para criar e editar um novo arquivo denominado **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. Modifique o seguinte texto substituindo os espaços reservados `<file-system-name>` e `<storage-account-name>` pelo nome do sistema de arquivos e da conta de armazenamento. Em seguida, copie e cole o texto no console nano pressionando a tecla SHIFT clicando junto com o botão direito do mouse.

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
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
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

3. Salve o arquivo usando CTRL + X e, em seguida, digite `Y` quando solicitado.

4. Use o seguinte comando para iniciar o Hive e executar o arquivo **flightdelays.hql**:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Após o término da execução do script __flightdelays.hql__, use o comando a seguir para abrir uma sessão interativa de Beeline:

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

   Essa consulta recupera uma lista de cidades em que houve atrasos causados pelo clima, além do tempo médio de atrasos e a salva em `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Posteriormente, o Sqoop lê os dados desse local e os exporta para o Banco de Dados SQL do Azure.

7. Para sair do Beeline, digite `!quit` no prompt.

## <a name="create-a-sql-database-table"></a>Criar uma tabela do Banco de Dados SQL

É necessário o nome do servidor do banco de dados SQL para essa operação. Conclua estas etapas para localizar o nome do servidor.

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Selecione **Bancos de dados SQL**.

3. Filtre pelo nome do banco de dados que você escolher usar. O nome do servidor está listado na coluna **Nome do servidor**.

4. Filtre pelo nome do banco de dados que você deseja usar. O nome do servidor está listado na coluna **Nome do servidor**.

    ![Obter detalhes do Azure SQL Server](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obter detalhes do Azure SQL Server")

    Há várias maneiras de se conectar ao Banco de Dados SQL e criar uma tabela. As seguintes etapas usam [FreeTDS](https://www.freetds.org/) do cluster HDInsight.

5. Para instalar FreeTDS, utilize o seguinte comando de uma conexão SSH para o cluster:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Depois de concluir a instalação, use o seguinte comando para conectar-se ao servidor do banco de dados SQL.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Substitua o espaço reservado `<server-name>` pelo nome do servidor do Banco de Dados SQL.

   * Substitua o espaço reservado `<admin-login>` pelo logon do administrador do Banco de Dados SQL.

   * Substitua o espaço reservado `<database-name>` pelo nome do banco de dados

   Quando solicitado, insira a senha para o logon de administrador do Banco de Dados SQL.

   Você receberá saídas semelhantes ao seguinte texto:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. No prompt `1>`, insira as seguintes instruções:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas.

   A consulta cria uma tabela chamada **delays**, que tem um índice clusterizado.

9. Use a consulta a seguir para verificar se a tabela foi criada:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   A saída é semelhante ao texto a seguir:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Enter `exit` at the `1>`.

## <a name="export-and-load-the-data"></a>Exportar e carregar os dados

Nas seções anteriores, você copiou os dados transformados no local `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Nesta seção, você usa o Sqoop para exportar os dados de `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` para a tabela criada no Banco de Dados SQL do Azure.

1. Use o comando a seguir para verificar se o Sqoop pode ver seu Banco de Dados SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   O comando retorna uma lista de bancos de dados, incluindo o banco de dados em que você criou a tabela **delays**.

2. Use o seguinte comando para exportar os dados da tabela **hivesampletable** para a tabela **delays**:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   O Sqoop conecta-se ao banco de dados que contém a tabela **delays** e exporta os dados do diretório `/tutorials/flightdelays/output` para a tabela **delays**.

3. Depois que o comando `sqoop` for concluído, use o utilitário tsql para conectar-se ao banco de dados:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Use as instruções a seguir para verificar se os dados foram exportados para a tabela **delays**:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Você deve ver uma listagem dos dados na tabela. A tabela inclui o nome da cidade e o tempo de atraso de voo médio dessa cidade.

5. Insira `exit` para sair do utilitário tsql.

## <a name="clean-up-resources"></a>Limpar recursos

Todos os recursos usados neste tutorial são preexistentes. Nenhuma limpeza é necessária.

## <a name="next-steps"></a>Próximas etapas

Para saber mais maneiras de trabalhar usando dados no HDInsight, confira o artigo a seguir:

> [!div class="nextstepaction"]
> [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
