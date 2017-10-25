---
title: "Usar o C# com o Hive e Pig no Hadoop no HDInsight – Azure | Microsoft Docs"
description: "Saiba como usar as UDFs (Funções Definidas pelo Usuário) do C# com streaming do Hive e Pig no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: b192b19c27045ab6a98baf382e467a0a74d30861
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Usar funções definidas pelo usuário do C# com streaming de Hive e Pig no Hadoop no HDInsight

Saiba como usar as UDFs (funções definidas pelo usuário) do C# com o Apache Hive e Pig no HDInsight.

> [!IMPORTANT]
> As etapas neste documento só funcionam com clusters HDInsight baseados no Linux e Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Controle de versão do componente do HDInsight](hdinsight-component-versioning.md).

Tanto o Hive quanto o Pig podem passar dados para aplicativos externos para processamento. Este processo é conhecido como _streaming_. Ao usar um aplicativo .NET, os dados são passados para o aplicativo em STDIN e o aplicativo retornará os resultados em STDOUT. Para ler e gravar por meio de STDIN e STDOUT, use `Console.ReadLine()` e `Console.WriteLine()` de um aplicativo de console.

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com gravação e compilação de código em C# que se destina ao .NET Framework 4.5.

    * Use o IDE que preferir. Recomendamos o [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 ou o [Visual Studio Code](https://code.visualstudio.com/). As etapas neste tutorial usam o Visual Studio 2017.

* Uma forma de carregar arquivos .exe para o cluster e executar trabalhos de Pig e Hive. Recomendamos as Ferramentas do Data Lake para Visual Studio, o Azure PowerShell e a CLI do Azure. As etapas neste documento usam as Ferramentas do Data Lake para Visual Studio para carregar os arquivos e executar o exemplo de consulta do Hive.

    Para obter informações sobre outras maneiras de executar consultas do Hive e trabalhos do Pig, veja os seguintes documentos:

    * [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)

    * [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)

* Um Hadoop no cluster do HDInsight. Para obter mais informações sobre como criar um cluster, consulte [Criar um cluster do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET no HDInsight

* Clusters do __HDInsight baseado em Linux__ usam [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicativos .NET. O Mono versão 4.2.1 está incluído no HDInsight versão 3.5.

    Para obter mais informações sobre compatibilidade de Mono com versões do .NET Framework, consulte [Compatibilidade de Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

    Para usar uma versão específica do Mono, consulte o documento [Instalar ou atualizar](hdinsight-hadoop-install-mono.md).

* Clusters do __HDInsight baseado em Windows__ usam CLR do Microsoft .NET para executar aplicativos.

Para obter mais informações sobre a versão do .NET Framework e do Mono incluídas no HDInsight, consulte [Versões do componente do HDInsight](hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar os projetos em C\#

### <a name="hive-udf"></a>UDF do Hive

1. Abra o Visual Studio e crie uma solução. Para o tipo de projeto, selecione **Aplicativo de Console (.NET Framework)** e nomeie o novo projeto como **HiveCSharp**.

    > [!IMPORTANT]
    > Selecione __.NET Framework 4.5__ se estiver usando um cluster HDInsight baseado em Linux. Para obter mais informações sobre compatibilidade de Mono com versões do .NET Framework, consulte [Compatibilidade de Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

2. Substitua os conteúdos do **Program.cs** pelo código a seguir:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Compile o projeto.

### <a name="pig-udf"></a>UDF do Pig

1. Abra o Visual Studio e crie uma solução. Para o tipo de projeto, selecione **Aplicativo de Console** e nomeie o novo projeto como **PigUDF**.

2. Substitua o conteúdo do arquivo **Program.cs** pelo seguinte código:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Esse código analisa as linhas enviadas do Pig e reformata as linhas que começam com `java.lang.Exception`.

3. Salve **Program.cs**, e, em seguida, compile o projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

1. No Visual Studio, abra **Gerenciador de Servidores**.

2. Expanda **Azure** e expanda **HDInsight**.

3. Se solicitado, insira suas credenciais de assinatura do Azure e, em seguida, clique em **Entrar**.

4. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto __(Conta de armazenamento padrão)__ é listada.

    ![Gerenciador de Servidores mostrando a conta de armazenamento para o cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Se essa entrada puder ser expandida, você estará usando uma __Conta de Armazenamento do Azure__ como armazenamento padrão do cluster. Para exibir os arquivos no armazenamento padrão para o cluster, expanda a entrada e clique duas vezes no __(Contêiner Padrão)__.

    * Se essa entrada não puder ser expandida, você estará usando __Azure Data Lake Store__ como o armazenamento padrão do cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada __(Conta de Armazenamento Padrão)__.

6. Para carregar os arquivos .exe, use um dos seguintes métodos:

    * Se estiver usando uma __Conta de Armazenamento do Azure__, clique no ícone de upload e, em seguida, navegue até a pasta **bin\debug** do projeto **HiveCSharp**. Por fim, selecione o arquivo **HiveCSharp.exe** e clique em **OK**.

        ![ícone de carregamento](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Se estiver usando o __Azure Data Lake Store__, clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione __Carregar__. Por fim, selecione o arquivo **HiveCSharp.exe** e clique em **Abrir**.

    Após o __HiveCSharp.exe__ ser carregado, repita o processo de upload para o arquivo __PigUDF.exe__.

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

1. No Visual Studio, abra **Gerenciador de Servidores**.

2. Expanda **Azure** e expanda **HDInsight**.

3. Clique com o botão direito do mouse no cluster em que você implantou o aplicativo **HiveCSharp** e, em seguida, selecione **Escrever uma consulta de Hive**.

4. Use o texto a seguir para a consulta de Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Remova a marca de comentário da instrução `add file` que corresponde ao tipo de armazenamento padrão usado para o cluster.

    Esta consulta seleciona os campos `clientid`, `devicemake` e `devicemodel` de `hivesampletable`, e passa os campos para o aplicativo HiveCSharp.exe. A consulta espera que o aplicativo retorne três campos, que são armazenados como `clientid`, `phoneLabel` e `phoneHash`. A consulta também espera encontrar HiveCSharp.exe na raiz do contêiner de armazenamento padrão.

5. Clique em **Enviar** para enviar o trabalho para o cluster HDInsight. A janela **Resumo do trabalho Hive** é aberta.

6. Clique em **Atualizar** para atualizar o resumo até que **Status do trabalho** mude para **Concluído**. Para exibir a saída do trabalho, clique em **Saída do trabalho**.

## <a name="run-a-pig-job"></a>Executar um trabalho Pig

1. Use um dos seguintes métodos para se conectar ao seu cluster HDInsight:

    * Se você estiver usando um cluster HDInsight __baseado em Linux__, use SSH. Por exemplo: `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Para obter mais informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Se você estiver usando um cluster HDInsight __baseado em Windows__, [Conecte-se ao cluster usando a Área de Trabalho Remota](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Use um dos comandos a seguir para iniciar a linha de comando do Pig:

        pig

    > [!IMPORTANT]
    > Se você estiver usando um cluster baseado em Windows, use os seguintes comandos:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Um prompt de `grunt>` é exibido.

3. Digite o seguinte para executar um trabalho do Pig que usa o aplicativo do .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    A instrução `DEFINE` cria um alias de `streamer` para os aplicativos pigudf.exe, enquanto `CACHE` o carrega do armazenamento padrão para o cluster. Posteriormente, `streamer` é usado com o operador `STREAM` para processar as linhas individuais contidas no LOG e retornar os dados como uma série de colunas.

    > [!NOTE]
    > O nome do aplicativo que é usado para streaming deve estar entre o caractere \` (acento grave) quando se tratar de um alias e ' (aspa simples) quando usado com `SHIP`.

4. Depois de inserir a última linha, o trabalho deve ser iniciado. Isso retorna saídas semelhantes ao seguinte texto:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar um aplicativo do .NET Framework do Hive e do Pig no HDInsight. Se você quiser aprender como usar o Python com Hive e Pig, consulte [Usar o Python com o Hive e o Pig no HDInsight](hdinsight-python.md).

Para obter outras formas de usar o Pig e o Hive e para saber como usar o MapReduce, consulte os documentos a seguir:

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
