---
title: Executar trabalhos do Apache Sqoop usando .NET e HDInsight – Azure
description: Saiba como usar o SDK do .NET do HDInsight para executar a importação e exportação do Apache Sqoop entre um cluster Apache Hadoop e um banco de dados SQL do Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928343"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar trabalhos do Apache Sqoop usando .NET SDK para Apache Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o SDK do .NET do Azure HDInsight para executar trabalhos do Apache Sqoop no HDInsight para importar e exportar entre um cluster HDInsight e um banco de dados SQL do Azure ou um banco de dados SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão de [Configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [Usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* [Do Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Familiaridade com o Sqoop. Para obter mais informações, consulte [Guia do usuário do Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Usar o Sqoop em clusters HDInsight com o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. Nesta seção, você cria um aplicativo de console em C# para exportar o `hivesampletable` para a tabela de banco de dados SQL do Azure que você criou com os pré-requisitos.

## <a name="set-up"></a>Configuração

1. Inicie o Visual Studio e crie um aplicativo de console em C#.

1. Navegue até **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador de pacotes** e execute o seguinte comando:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Exportação do Sqoop

Do Hive para o SQL Server.  Este exemplo exporta dados da tabela `hivesampletable` do Hive para a tabela `mobiledata` no Banco de Dados SQL.

1. Use o código a seguir no arquivo Program.cs. Edite o código para definir os valores para `ExistingClusterName` , e `ExistingClusterPassword` .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
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
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Para executar o programa, aperte a tecla **F5**.

## <a name="sqoop-import"></a>Importação do Sqoop

Do SQL Server para o Armazenamento do Azure. Este exemplo depende da exportação acima que foi executada.  Este exemplo importa dados da `mobiledata` tabela no banco de dados SQL para o `wasb:///tutorials/usesqoop/importeddata` diretório na conta de armazenamento padrão do cluster.

1. Substitua o código acima no `//sqoop start //sqoop end` bloco pelo seguinte código:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Para executar o programa, aperte a tecla **F5**.

## <a name="limitations"></a>Limitações

O HDInsight baseado em Linux tem as seguintes limitações:

* Exportação em massa: O conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure não permite inserções em massa atualmente.

* Envio em lote: usando a `-batch` opção, Sqoop executa várias inserções em vez de executar o lote de operações de inserção.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como usar o Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho do Oozie.
* [Carregar dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para fazer upload de dados para HDInsight ou armazenamento de Blob do Azure.
