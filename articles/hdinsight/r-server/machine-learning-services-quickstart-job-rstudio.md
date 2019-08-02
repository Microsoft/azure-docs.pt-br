---
title: 'Início Rápido: Executar um script R em um cluster de serviços de ML no Azure HDInsight usando o RStudio Server'
description: No início rápido, você executa um script R em um cluster de serviços de ML no Azure HDInsight usando o RStudio Server.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 484763adfa154dcdf226b03f1f591d248532ee35
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451497"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Início Rápido: Executar um script R em um cluster de serviços de ML no Azure HDInsight usando o RStudio Server

O ML Services no Azure HDInsight permite que os scripts R usem Apache Spark e Apache Hadoop MapReduce para executar cálculos distribuídos. O ML Services controla como as chamadas são executadas, definindo o contexto de computação. O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do RevoScaleR nos núcleos do servidor do nó de borda. Você também pode executá-los nos nós do cluster usando os contextos de cálculo Hadoop Map Reduce ou Apache Spark do RevoScaleR.

Neste início rápido, você aprende como executar um script R com o RStudio Server que demonstra o uso do Spark para cálculos de R distribuídos. Você vai definir um contexto de computação para executar cálculos localmente em um nó de borda e distribuídos entre os nós no cluster HDInsight.

## <a name="prerequisite"></a>Pré-requisito

Um cluster do ML Services no HDInsight. Veja [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **ML Services** como **tipo de Cluster**.

## <a name="connect-to-rstudio-server"></a>Conectar-se ao RStudio Server

O RStudio Server é executado no nó de borda do cluster. Vá até a URL a seguir, onde `CLUSTERNAME` é o nome do cluster do ML Services que você criou:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Na primeira vez em que você fizer logon, precisará autenticar duas vezes. No primeiro prompt de autenticação, forneça o logon de administrador do cluster e a senha (o padrão é `admin`). No segundo prompt de autenticação, forneça o logon de SSH e a senha (o padrão é `sshuser`). Os logons subsequentes exigirão apenas as credenciais do SSH.

Quando você estiver conectado, sua tela deve ser semelhante à captura de tela a seguir:

![Detalhes básicos do cluster](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio.png)

## <a name="use-a-compute-context"></a>Use um contexto de computação

1. No RStudio Server, use o seguinte código para carregar dados de exemplo no armazenamento padrão do HDInsight:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Esta etapa pode demorar cerca de 8 minutos para ser concluída.

1. Crie algumas informações de dados e defina duas fontes de dados. Insira o seguinte código no RStudio:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Execute uma regressão logística com os dados usando o contexto de computação **local**. Insira o seguinte código no RStudio:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Os cálculos devem ser concluídos em cerca de 7 minutos. Você deverá visualizar a saída que termina com linhas semelhantes ao seguinte snippet de código:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Execute a mesma regressão logística usando o contexto do **Spark**. O contexto do Spark distribui o processamento em todos os nós de trabalho no cluster HDInsight. Insira o seguinte código no RStudio:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Os cálculos devem ser concluídos em cerca de 5 minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Após concluir o início rápido, poderá ser conveniente excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Para excluir um cluster, confira [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como executar um script R com o RStudio Server que demonstrou o uso do Spark para cálculos de R distribuídos.  Avance para o próximo artigo para aprender sobre as opções que estão disponíveis para especificar se e como a execução é paralelizada entre núcleos do nó de borda ou no cluster HDInsight.

> [!div class="nextstepaction"]
>[Opções de contexto de computação para o ML Services no HDInsight](./r-server-compute-contexts.md)