---
title: 'Tutorial: Usar R em um contexto de computação do Spark Azure HDInsight'
description: Tutorial-introdução ao R e ao Spark em um cluster de serviços de Machine Learning do Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: f072b6905881da7b7854b0e51d690dbbd40dffb5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227435"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Tutorial: Usar R em um contexto de computação do Spark Azure HDInsight

Este tutorial apresenta uma introdução passo a passo para usar as funções de R no Apache Spark em execução em um cluster de serviços de Machine Learning no Azure HDInsight.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Baixar os dados de exemplo para o armazenamento local
> * Copiar os dados para o armazenamento padrão
> * Configurar um repositório de dados
> * Criar fontes de dados
> * Criar um contexto de computação para o Spark
> * Ajustar um modelo linear
> * Usar arquivos de composição XDF
> * Converter XDF em CSV

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços de Machine Learning do Azure HDInsight. Acesse [Criar clusters do Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e, para **Tipo de cluster**, selecione **Serviços de ML**.

## <a name="connect-to-rstudio-server"></a>Conectar-se ao RStudio Server

O RStudio Server é executado no nó de borda do cluster. Vá para o site a seguir (em que *CLUSTERNAME* na URL é o nome do cluster de serviços de Machine Learning do HDInsight que você criou):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Na primeira vez em que você se conecta, você se autentica duas vezes. No primeiro prompt de autenticação, forneça o nome de usuário e senha do administrador do cluster (o padrão é *admin*). No segundo prompt de autenticação, forneça o nome de usuário e senha do SSH (o padrão é *sshuser*). As entradas subsequentes exigirão apenas as credenciais do SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Baixar os dados de exemplo para o armazenamento local

O *Conjunto de Dados no Horário de Companhias Aéreas para 2012* consiste em 12 arquivos separados por vírgulas contendo detalhes de partidas e chegadas de voos para todos os voos comerciais nos EUA para o ano de 2012. Este conjunto de dados é grande, com mais de 6 milhões de observações.

1. Inicialize algumas variáveis de ambiente. No console do servidor do RStudio, digite o seguinte código:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. No painel à direita, selecione a guia **Ambiente**. As variáveis são exibidas em **Valores**.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

1.  Crie um diretório local e baixe os dados de exemplo. Insira o seguinte código no RStudio:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    O download deve ser concluído em aproximadamente nove minutos e meio.

## <a name="copy-the-data-to-default-storage"></a>Copiar os dados para o armazenamento padrão

O local do HDFS (Sistema de Arquivos Distribuído Hadoop) é especificado com a variável `airDataDir`. Insira o seguinte código no RStudio:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

A etapa deve ser concluída em aproximadamente dez segundos.

## <a name="set-up-a-dataset"></a>Configurar um repositório de dados

1. Crie um objeto de sistema de arquivos que use os valores padrão. Insira o seguinte código no RStudio:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Já que os arquivos CSV originais têm nomes de variável bastante complicados, você fornece uma lista *colInfo* para torná-los mais gerenciáveis. Insira o seguinte código no RStudio:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Criar fontes de dados

Em um contexto de computação do Spark, você pode criar fontes de dados usando as funções a seguir:

|Função | DESCRIÇÃO |
|---------|-------------|
|`RxTextData` | Uma fonte de dados de texto delimitado por vírgula. |
|`RxXdfData` | Dados no formato de arquivo de dados XDF. No RevoScaleR, o formato de arquivo XDF é modificado para Hadoop para armazenar os dados em um conjunto composto por arquivos, em vez de um único arquivo. |
|`RxHiveData` | Gera um objeto de Fonte de Dados do Hive.|
|`RxParquetData` | Gera um objeto de Fonte de Dados do Parquet.|
|`RxOrcData` | Gera um objeto de Fonte de Dados do Orc.|

Crie um objeto [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) usando os arquivos copiados para o HDFS. Insira o seguinte código no RStudio:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Criar um contexto de computação para o Spark

Para carregar os dados e executar análises em nós de trabalho, defina o contexto de computação em seu script como [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Nesse contexto, as funções de R automaticamente distribuem a carga de trabalho em todos os nós de trabalho sem necessidade interna de gerenciar trabalhos nem a fila. O contexto de computação do Spark é estabelecido por meio de `RxSpark` ou `rxSparkConnect()` para criar o contexto de computação do Spark e usa `rxSparkDisconnect()` para retornar para um contexto de computação local. Insira o seguinte código no RStudio:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Ajustar um modelo linear

1. Use a função [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) de acordo com um modelo linear usando sua fonte de dados `airDS`. Insira o seguinte código no RStudio:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Esta etapa deve ser concluída em dois a três minutos.

1. Exiba os resultados. Insira o seguinte código no RStudio:

    ```R
    summary(delayArr)
    ```

    Você deve ver o seguintes resultados:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Os resultados indicam que você processou todos os dados, seis milhões de observações, usando todos os arquivos CSV no diretório especificado. Observe também que, já que especificou `cube = TRUE`, você tem um coeficiente estimado para cada dia da semana (e não a interceptação).

## <a name="use-composite-xdf-files"></a>Usar arquivos de composição XDF

Como visto, é possível analisar arquivos CSV diretamente com R no Hadoop. Mas você poderá fazer a análise mais rapidamente se armazenar os dados em um formato mais eficiente. O formato XDF do R é eficiente, mas é um pouco modificado para o HDFS para que arquivos individuais permaneçam dentro de um único bloco do HDFS. (O tamanho do bloco do HDFS varia conforme a instalação, mas normalmente tem 64 MB ou 128 MB.) 

Quando usa [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) no Hadoop para criar um conjunto de arquivos XDF compostos, você deve especificar uma fonte de dados `RxTextData` (por exemplo, `AirDS`) como o inData e uma fonte de dados `RxXdfData` que tenha o fileSystem definido como um sistema de arquivos HDFS como o argumento outFile. O objeto `RxXdfData` pode então ser usado como o argumento de dados nas próximas análises de R.

1. Defina um objeto `RxXdfData`. Insira o seguinte código no RStudio:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Defina um tamanho de bloco de 250 mil linhas e especifique que podemos ler todos os dados. Insira o seguinte código no RStudio:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importe os dados usando `rxImport`. Insira o seguinte código no RStudio:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Esta etapa deve ser concluída em alguns minutos.

1. Estime novamente o mesmo modelo linear usando a fonte de dados nova e mais rápida. Insira o seguinte código no RStudio:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Esta etapa deve ser concluída em menos de um minuto.

1. Exiba os resultados. Os resultados devem ser os mesmos que os dos arquivos CSV. Insira o seguinte código no RStudio:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Converter XDF em CSV

### <a name="in-a-spark-context"></a>Em um contexto do Spark

Se você tiver convertido seus arquivos CSV no formato de arquivo XDF para uma maior eficiência ao executar análises, mas agora quiser converter seus dados de volta para CSV, poderá fazer isso usando [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Para criar uma pasta de arquivos CSV, primeiro crie um objeto `RxTextData` usando um nome de diretório como o argumento de arquivo. Esse objeto representa a pasta na qual os arquivos CSV são criados. Esse diretório é criado quando você executa `rxDataStep`. Em seguida, aponte para este objeto `RxTextData` no argumento `outFile` do `rxDataStep`. Cada CSV criado terá um nome conforme o nome do diretório e seguido por um número.

Suponha que você queira gravar uma pasta de arquivos CSV no HDFS de nosso XDF composto `airDataXdf` depois de você realizar a regressão logística e a previsão, de modo que os novos arquivos CSV contenham os valores previstos e os resíduos. Insira o seguinte código no RStudio:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Esta etapa deve ser concluída em aproximadamente dois minutos e meio.

O `rxDataStep` escreveu um arquivo CSV para cada arquivo XDFD no arquivo XDF composto de entrada. Esse é o comportamento padrão para escrever arquivos CSV usando arquivos XDF compostos para HDFS quando o contexto de computação é definido como `RxSpark`.

### <a name="in-a-local-context"></a>Em um contexto local

Como alternativa, ao terminar de executar suas análises, você poderá alternar seu contexto de computação de volta para `local` a fim de aproveitar os dois argumentos dentro de `RxTextData`, o que lhe dá um pouco mais controle ao gravar arquivos CSV para HDFS: `createFileSet` e `rowsPerOutFile`. Quando `createFileSet` é definido para `TRUE`, uma pasta de arquivos CSV é gravada no diretório que você especificar. Quando `createFileSet` é definido para `FALSE`, um único arquivo CSV é escrito. O segundo argumento, `rowsPerOutFile`, pode ser definido como um inteiro para indicar quantas linhas gravar em cada arquivo CSV quando `createFileSet` é `TRUE`.

Insira o seguinte código no RStudio:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Esta etapa deve ser concluída em aproximadamente dez minutos.

Ao usar um contexto de computação `RxSpark`, `createFileSet` usa `TRUE` como padrão e `rowsPerOutFile` não tem nenhum efeito. Assim, se você deseja criar um único CSV ou personalizar o número de linhas por arquivo, execute o `rxDataStep` em um contexto de computação `local` (os dados ainda podem estar em HDFS).

## <a name="final-steps"></a>Etapas finais

1. Limpe os dados. Insira o seguinte código no RStudio:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Interrompa o aplicativo remoto do Spark. Insira o seguinte código no RStudio:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Saia da sessão do R. Insira o seguinte código no RStudio:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Já que os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estão em uso.

Para excluir um cluster, confira [Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar as funções do R no Apache Spark em execução em um cluster de Serviços de Machine Learning no HDInsight. Para obter mais informações, consulte os seguintes artigos:

* [Opções de contexto de computação para um cluster de serviços de Machine Learning do Azure HDInsight](r-server-compute-contexts.md)
* [Funções do R para Spark no Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
