---
title: Executar trabalhos do Apache Sqoop usando .NET e HDInsight – Azure
description: Aprenda a usar o SDK do .NET do HDInsight para executar a importação e a exportação do Apache Sqoop entre um cluster do Apache Hadoop e um banco de dados SQL do Azure.
keywords: trabalho de sqoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1bdf1318f93697cd7b479d404f44b7617ad875dc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450147"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar trabalhos do Apache Sqoop usando .NET SDK para Apache Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o SDK do .NET do Azure HDInsight para executar trabalhos do Apache Sqoop no HDInsight para importar e exportar entre um cluster HDInsight e um banco de dados SQL do Azure ou SQL Server.

> [!NOTE]
> As etapas deste artigo podem ser usadas com qualquer cluster HDInsight baseado em Linux ou Windows. No entanto, elas só funcionam em um cliente Windows. Use o seletor de guias na parte superior deste artigo para escolher outros métodos.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte item:

* Um cluster do Apache Hadoop no HDInsight. Para obter mais informações, consulte [Criar um cluster e um banco de dados SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Usar o Sqoop em clusters HDInsight com o SDK do .NET
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. Nesta seção, você cria um C# console de aplicativo para exportar o hivesampletable para a tabela de banco de dados SQL que você criou anteriormente neste artigo.

## <a name="submit-a-sqoop-job"></a>Enviar um trabalho de Sqoop

1. No Visual Studio, crie um aplicativo de console C#.

2. No Console do Gerenciador de Pacotes do Visual Studio, execute o comando do NuGet a seguir para importar o pacote:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Use o seguinte código no arquivo Program.cs:
   
        using System.Collections.Generic;
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Para executar o programa, aperte a tecla **F5**. 

## <a name="limitations"></a>Limitações
O HDInsight baseado em Linux tem as seguintes limitações:

* Exportação em massa: O conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure não permite inserções em massa atualmente.

* Envio em lote: Ao usar o comutador `-batch`, o Sqoop realizará várias inserções em vez de enviar em lote as operações de inserção.

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho do Oozie.
* [Carregar dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para fazer upload de dados para HDInsight ou armazenamento de Blob do Azure.

