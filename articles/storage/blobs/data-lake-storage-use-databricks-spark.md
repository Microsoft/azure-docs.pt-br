---
title: 'Tutorial: Azure Data Lake Storage Gen2, Azure Databricks e Spark | Microsoft Docs'
description: Este tutorial mostra como executar consultas Spark em um cluster do Azure Databricks para acessar dados em uma conta de armazenamento do Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: 5fce5871b4bd6c3e2353f7df04018e88b86ec4c7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912511"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Tutorial: Azure Data Lake Storage Gen2, Azure Databricks e Spark

Este tutorial mostra como conectar seu cluster do Azure Databricks aos dados armazenados em uma conta de Armazenamento do Azure que tenha o Azure Data Lake Storage Gen2 habilitado. Essa conexão permite que você execute nativamente consultas e análises nos dados por meio do cluster.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar um cluster Databricks
> * Ingerir dados não estruturados em uma conta de armazenamento
> * Executar a análise em seus dados no Armazenamento de Blobs

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Criar uma conta do Azure Data Lake Storage Gen2.

  Confira [Criar uma conta de armazenamento para usar com o Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Verifique se a sua conta de usuário tem a [função Colaborador de Dados do Storage Blob](../common/storage-auth-aad-rbac-portal.md) atribuída a ela.

* Instale o AzCopy v10. Confira [Transferir dados com o AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Crie uma entidade de serviço. Veja [Como Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md).

  Há algumas tarefas específicas que você precisará realizar conforme executar as etapas deste artigo.

  :heavy_check_mark: Ao executar as etapas da seção [Atribuir o aplicativo a uma função](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) do artigo, atribua a função **Colaborador dos Dados do Storage Blob** à entidade de serviço.

  > [!IMPORTANT]
  > Atribua a função no escopo da conta de armazenamento do Data Lake Storage Gen2. Você pode atribuir uma função ao grupo de recursos pai ou à assinatura, mas receberá erros relacionados a permissões até que essas atribuições de função sejam propagadas para a conta de armazenamento.

  :heavy_check_mark: Ao executar as etapas da seção [Obter valores para se conectar](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) do artigo, cole a ID do locatário, a ID do aplicativo e os valores do segredo do cliente em um arquivo de texto. Você precisará deles em breve.

### <a name="download-the-flight-data"></a>Baixar os dados de voos

Este tutorial usa dados de voo do Bureau of Transportation Statistics (Departamento de Estatísticas de Transporte dos EUA) para demonstrar como executar uma operação de ETL. Você precisa baixar esses dados para concluir o tutorial.

1. Acesse [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Administração de pesquisa e inovação em tecnologia, Departamento de Estatísticas de Transporte dos EUA).

2. Marque a caixa de seleção **Arquivo Pré-compactado** para selecionar todos os campos de dados.

3. Selecione o botão **Baixar** e salve os resultados no computador. 

4. Descompacte o conteúdo do arquivo compactado e anote o nome e o caminho do arquivo. Você precisará dessas informações em uma etapa posterior.

## <a name="create-an-azure-databricks-service"></a>Criar um serviço do Azure Databricks

Nesta seção, você criará um serviço do Azure Databricks usando o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Serviço do Azure Databricks**, forneça os seguintes valores para criar um serviço do Databricks:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome do workspace**     | Forneça um nome para o seu workspace do Databricks.  |
    |**Assinatura**     | Na lista suspensa, selecione sua assinatura do Azure.        |
    |**Grupo de recursos**     | Especifique se deseja criar um novo grupo de recursos ou usar um existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, consulte [Visão geral do Grupo de Recursos do Azure](../../azure-resource-manager/management/overview.md). |
    |**Localidade**     | Selecione **Oeste dos EUA 2**. Para outras regiões disponíveis, consulte [serviços do Azure por região](https://azure.microsoft.com/regions/services/).       |
    |**Tipo de preço**     |  Selecione **Padrão**.     |

    ![Crie um workspace do Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Criar um serviço do Azure Databricks")

3. A criação da conta leva alguns minutos. Para monitorar o status da operação, veja a barra de progresso na parte superior.

4. Selecione **Fixar no painel** e depois **Criar**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Criar um cluster Spark no Azure Databricks

1. No portal do Azure, acesse o serviço do Databricks criado e selecione **Iniciar Workspace**.

2. Você será redirecionado ao portal do Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, forneça os valores para criar um cluster.

    ![Criar um cluster Spark do Databricks no Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Criar um cluster Spark do Databricks no Azure")

    Preencha os valores para os seguintes campos e aceite os valores padrão para os outros campos:

    - Insira um nome para o cluster.
     
    - Verifique se você marcou a caixa de seleção **Terminar depois de 120 minutos de inatividade**. Forneça uma duração (em minutos) para encerrar o cluster caso ele não esteja sendo usado.

4. Selecione **Criar cluster**. Quando o cluster está em execução, você pode anexar notebooks a ele e executar trabalhos do Spark.

## <a name="ingest-data"></a>Ingestão de dados

### <a name="copy-source-data-into-the-storage-account"></a>Copiar dados de origem para a conta de armazenamento

Use o AzCopy para copiar dados do arquivo *.csv* para sua conta do Data Lake Storage Gen2.

1. Abra uma janela do prompt de comando e insira o comando a seguir para fazer logon em sua conta de armazenamento.

   ```bash
   azcopy login
   ```

   Siga as instruções exibidas na janela do prompt de comando para autenticar sua conta de usuário.

2. Para copiar dados da conta *.csv*, insira o comando a seguir.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Substitua o valor de espaço reservado `<csv-folder-path>` pelo nome caminho no arquivo *.csv*.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

   * Substitua o espaço reservado `<container-name>` pelo nome de um contêiner na sua conta de armazenamento.

## <a name="create-a-container-and-mount-it"></a>Criar um contêiner e montá-lo

Nesta seção, você criará um contêiner e uma pasta em sua conta de armazenamento.

1. No [portal do Azure](https://portal.azure.com), acesse o serviço do Azure Databricks criado e selecione **Iniciar Workspace**.

2. À esquerda, selecione **Workspace**. Na lista suspensa **Workspace**, selecione **Criar** > **Notebook**.

    ![Criar um notebook no Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Criar notebook no Databricks")

3. Na caixa de diálogo **Criar Bloco de Anotações**, digite um nome para o bloco de anotações. Selecione **Python** como a linguagem e, em seguida, selecione o cluster Spark criado anteriormente.

4. Selecione **Criar**.

5. Copie e cole o bloco de código a seguir na primeira célula, mas não execute esse código ainda.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. Neste bloco de código, substitua os valores de espaço reservado `appId`, `clientSecret`, `tenant` e `storage-account-name` pelos valores coletados ao concluir os pré-requisitos deste tutorial. Substitua o valor de espaço reservado `container-name` pelo nome do contêiner.

19. Pressione as teclas **SHIFT+ENTER** para executar o código nesse bloco.

   Mantenha esse notebook aberto, pois você adicionará comandos a ele mais tarde.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Use o Databricks Notebook para converter CSV em Parquet

No notebook criado anteriormente, adicione uma nova célula e cole o código a seguir nessa célula. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Explorar dados

Em uma nova célula, cole o código a seguir para obter uma lista de arquivos CSV carregados por meio do AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Para criar um novo arquivo e listá-los na pasta *parquet/voos*, execute este script:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Com esses exemplos de código, você explorou a natureza hierárquica do HDFS usando dados armazenados em uma conta de armazenamento com o Data Lake Storage Gen2 habilitado.

## <a name="query-the-data"></a>Consultar os dados

Em seguida, você pode começar a consultar os dados carregados na sua conta de armazenamento. Insira cada um dos blocos de código a seguir em **Cmd 1** e pressione **Cmd+Enter** para executar o script de Python.

Para criar quadros de dados para suas fontes de dados, execute o seguinte script:

* Substitua o valor de espaço reservado `<csv-folder-path>` pelo nome caminho no arquivo *.csv*.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Insira esse script para executar algumas consultas básicas de análise nos dados.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos da conta de armazenamento e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Extrair, transformar e carregar dados usando o Apache Hive no HDInsight do Azure](data-lake-storage-tutorial-extract-transform-load-hive.md)