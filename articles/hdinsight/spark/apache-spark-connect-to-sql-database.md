---
title: Usar Apache Spark para ler e gravar dados no banco de dado SQL do Azure
description: Saiba como configurar uma conexão entre o cluster HDInsight Spark e o banco de dados SQL do Azure. Para ler dados, gravar dados e transmitir dados em um banco de dado SQL
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 4d42cedbc5dc20c929703be106e732b4806f3902
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940612"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Usar o cluster HDInsight Spark para ler e gravar dados no banco de dado SQL do Azure

Saiba como conectar um cluster Apache Spark no Azure HDInsight com o banco de dados SQL do Azure. Em seguida, leia, grave e transmita dados para o SQL Database. As instruções neste artigo usam um Jupyter Notebook para executar os trechos de código escalares. No entanto, você pode criar um aplicativo autônomo em escala ou Python e fazer as mesmas tarefas.

## <a name="prerequisites"></a>Pré-requisitos

* Cluster do Azure HDInsight Spark.  Siga as instruções em [Criar um cluster do Apache Spark no HDInsight ](apache-spark-jupyter-spark-sql.md).

* Banco de Dados SQL do Azure. Siga as instruções em [criar um banco de dados no banco de dados SQL do Azure](../../azure-sql/database/single-database-create-quickstart.md). Certifique-se de criar um banco de dados com o esquema e dados **AdventureWorksLT** de exemplo. Além disso, certifique-se de criar uma regra de firewall no nível de servidor para permitir que o endereço IP do cliente acesse o banco de dados SQL. As instruções para adicionar a regra de firewall estão disponíveis no mesmo artigo. Depois de criar o banco de dados SQL, certifique-se de manter os valores a seguir à mão. Esses valores serão necessários para se conectar ao banco de dados de um cluster Spark.

    * Nome de servidor.
    * nome do banco de dados.
    * Nome de usuário/senha do administrador do banco de dados SQL do Azure.

* SQL Server Management Studio (SSMS). Siga as instruções em [Usar SSMS para conectar e consultar dados](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Criará um Jupyter Notebook

Comece criando um Jupyter Notebook associado ao cluster Spark. Você usa esse notebook para executar os snippets de código usados neste artigo.

1. Do [portal do Azure](https://portal.azure.com/), abra o seu cluster.
1. Selecione **Jupyter Notebook** abaixo de **painéis de cluster** no lado direito.  Se você não vir os **painéis de cluster**, selecione **visão geral** no menu à esquerda. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

    ![Jupyter Notebook em Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter Notebook no Spark")

   > [!NOTE]  
   > Você também pode acessar o Jupyter Notebook no cluster Spark abrindo a seguinte URL em seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Na Jupyter Notebook, no canto superior direito, clique em **novo** e em **Spark** para criar um notebook escala. Os notebooks Jupyter no cluster HDInsight Spark também fornecem o kernel **PySpark** para aplicativos Python2 e o kernel **PySpark3** para aplicativos Python3. Para este artigo, criamos um notebook Scala.

    ![Kernels para Jupyter Notebook no Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels para Jupyter Notebook no Spark")

    Para obter mais informações sobre os kernels, consulte [usar Jupyter Notebook kernels com clusters Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Neste artigo, usamos um kernel Spark (escala) porque há suporte para streaming de dados do Spark para o banco de dado SQL apenas em escala e Java no momento. Embora a leitura e gravação em SQL possam ser feitas usando o Python, visando manter a consistência neste artigo nós usamos o Scala para todas as três operações.

1. Um novo bloco de anotações é aberto com um nome padrão, sem **título**. Clique no nome do notebook e insira o nome de sua escolha.

    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Fornecer um nome para o bloco de anotações")

Agora, você pode iniciar a criação do seu aplicativo.

## <a name="read-data-from-azure-sql-database"></a>Ler dados do banco de dados SQL do Azure

Nesta seção, você faz a leitura dos dados de uma tabela (por exemplo, **SalesLT.Address**) que existe no banco de dados AdventureWorks.

1. Em um novo Jupyter Notebook, em uma célula de código, Cole o trecho a seguir e substitua os valores de espaço reservado pelos valores do seu banco de dados.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Pressione **SHIFT + ENTER** para executar a célula de código.  

1. Use o trecho abaixo para criar uma URL JDBC que você pode passar para as APIs do dataframe do Spark. O código cria um `Properties` objeto para manter os parâmetros. Cole o snippet de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Use o trecho de código abaixo para criar um dataframe com os dados de uma tabela no banco de dado. Neste trecho, usamos uma `SalesLT.Address` tabela que está disponível como parte do banco de dados **AdventureWorksLT** . Cole o snippet de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Agora você pode realizar operações no dataframe, como obter o esquema de dados:

    ```scala
    sqlTableDF.printSchema
    ```

    Você verá uma saída semelhante à imagem a seguir:

    ![saída do esquema](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "saída do esquema")

1. Você também pode fazer operações como, recuperar as 10 primeiras linhas.

    ```scala
    sqlTableDF.show(10)
    ```

1. Ou, recuperar colunas específicas do conjunto de dados.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Gravar dados no banco de dado SQL do Azure

Nesta seção, usamos um arquivo CSV de exemplo disponível no cluster para criar uma tabela em seu banco de dados e preenchê-la com data. O arquivo CSV de exemplo (**HVAC.csv**) está disponível em todos os clusters HDInsight em `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Em um novo Jupyter Notebook, em uma célula de código, Cole o trecho a seguir e substitua os valores de espaço reservado pelos valores do seu banco de dados.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Pressione **SHIFT + ENTER** para executar a célula de código.  

1. O trecho a seguir cria uma URL JDBC que você pode passar para as APIs do dataframe do Spark. O código cria um `Properties` objeto para manter os parâmetros. Cole o snippet de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Use o snippet de código abaixo para extrair o esquema dos dados em HVAC.csv e usar o esquema para carregar os dados do CSV em um dataframe, `readDf`. Cole o snippet de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Use o dataframe `readDf` para criar uma tabela temporária, `temphvactable`. Em seguida, use a tabela temporária para criar uma tabela de hive, `hvactable_hive`.

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Por fim, use a tabela Hive para criar uma tabela em seu banco de dados. O trecho a seguir cria `hvactable` no banco de dados SQL do Azure.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Conecte-se ao banco de dados SQL do Azure usando o SSMS e verifique se você vê `dbo.hvactable` aqui.

    a. Inicie o SSMS e conecte-se ao banco de dados SQL do Azure fornecendo detalhes de conexão, conforme mostrado na captura de tela abaixo.

    ![Conectar-se ao banco de dados SQL usando SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Conectar-se ao banco de dados SQL usando SSMS1")

    b. No **pesquisador de objetos**, expanda o banco de dados e o nó da tabela para ver o **dbo. hvactable** criado.

    ![Conectar-se ao banco de dados SQL usando SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Conectar-se ao banco de dados SQL usando SSMS2")

1. Execute uma consulta no SSMS para ver as colunas na tabela.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Transmita dados para o Azure SQL Database

Nesta seção, transmitimos dados para o `hvactable` que você criou na seção anterior.

1. Como uma primeira etapa, verifique se não há registros no `hvactable` . Usando SSMS, execute a seguinte consulta na tabela.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Crie um novo Jupyter Notebook no cluster HDInsight Spark. Em uma célula de código, cole o seguinte snippet de código e, em seguida, pressione **SHIFT + ENTER**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Nós transmitimos dados do **HVAC.csv** para o `hvactable` . HVAC.csv arquivo está disponível no cluster em `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . No snippet de código a seguir, primeiro recebemos o esquema dos dados a serem transmitidos. Em seguida, criamos um dataframe de transmissão usando esse esquema. Cole o snippet de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. A saída mostra o esquema de **HVAC.csv**. O `hvactable` também tem o mesmo esquema. A saída lista as colunas na tabela.

    ![' tabela de esquema do hdinsight Apache Spark '](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Esquema da tabela")

1. Por fim, use o trecho a seguir para ler dados do HVAC.csv e transmiti-los para o `hvactable` no seu banco de dados. Cole o trecho em uma célula de código, substitua os valores de espaço reservado pelos valores do seu banco de dados e pressione **Shift + Enter** para executar.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. Verifique se os dados estão sendo transmitidos para o `hvactable` executando a seguinte consulta no SQL Server Management Studio (SSMS). Toda vez que você executa a consulta, ele mostra o número de linhas na tabela aumentando.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Próximas etapas

* [Usar o cluster HDInsight Spark para analisar dados no Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Carregar dados e executar consultas em um cluster Apache Spark no Azure HDInsight](apache-spark-load-data-run-query.md)
* [Usar o fluxo estruturado do Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
