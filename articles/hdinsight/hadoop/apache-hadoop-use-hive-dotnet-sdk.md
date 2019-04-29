---
title: Executar consultas do Apache Hive usando SDK do .NET do HDInsight - Azure
description: Saiba como enviar trabalhos do Apache Hadoop para o Apache Hadoop do Azure HDInsight usando SDK do .NET do HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 947e797842000e4da2f9e22077bc32c24d6c6a74
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095364"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Executar consultas do Apache Hive usando SDK do .NET do HDInsight
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como enviar consultas do Apache Hive usando SDK do .NET do HDInsight. Você escreve um programa em C# para enviar uma consulta do Hive para listar tabelas de Hive e exibir os resultados.

> [!NOTE]  
> As etapas neste artigo devem ser executadas em um cliente do Windows. Para obter informações sobre como usar um cliente Linux, OS X ou Unix para trabalhar com o Hive, use o seletor de tabulação mostrado na parte superior do artigo.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter os seguintes itens:

* **Um cluster do Apache Hadoop no HDInsight**. Confira a [Introdução ao uso do Hadoop baseado em Linux no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]  
    > A partir de 15 de setembro de 2017, o HDInsight .NET SDK suporta apenas resultados de consulta de Hive retornados de contas de armazenamento do Azure. Se você usar este exemplo com um cluster HDInsight que usa o Azure Data Lake Storage como armazenamento primário, você não poderá recuperar os resultados da pesquisa usando o .NET SDK.

* **Visual Studio 2013/2015/2017**.

## <a name="run-a-hive-query"></a>Executar uma consulta de Hive
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight do .NET. 

**Para enviar trabalhos**

1. No Visual Studio, crie um aplicativo de console C#.
2. No Console do Gerenciador de Pacotes NuGet, execute o comando a seguir:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Use o seguinte código:

    ```csharp
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
    ```
4. Pressione **F5** para executar o aplicativo.

A saída do aplicativo deverá ser semelhante a:

![Saída do trabalho do Hive do Hadoop HDInsight](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Criar clusters do Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Referência de SDK .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o Apache Sqoop com HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Criar aplicativos .NET HDInsight de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


