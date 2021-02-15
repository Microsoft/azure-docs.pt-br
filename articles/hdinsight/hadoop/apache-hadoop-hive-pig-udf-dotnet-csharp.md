---
title: C#, Apache Hive & Apache Pig no Apache Hadoop-Azure HDInsight
description: Aprenda a usar as funções definidas pelo usuário (UDF) do C# com o Apache Hive e o streaming do Apache Pig no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: 97b23a2b9e3b95a5ea0efcd27d0ec185b1c456f1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946546"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Usar funções definidas pelo usuário do C# com Apache Hive e Apache Pig no Apache Hadoop no HDInsight

Saiba como usar o UDF (funções definidas pelo usuário) do C# com o [Apache Hive](https://hive.apache.org) e o [Apache Pig](https://pig.apache.org) no HDInsight.

> [!IMPORTANT]
> As etapas neste documento funcionam com clusters HDInsight baseados em Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Controle de versão do componente do HDInsight](../hdinsight-component-versioning.md).

Tanto o Hive quanto o Pig podem passar dados para aplicativos externos para processamento. Este processo é conhecido como _streaming_. Ao usar um aplicativo .NET, os dados são passados para o aplicativo em STDIN e o aplicativo retornará os resultados em STDOUT. Para ler e gravar por meio de STDIN e STDOUT, use `Console.ReadLine()` e `Console.WriteLine()` de um aplicativo de console.

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com gravação e compilação de código em C# que se destina ao .NET Framework 4.5.

    Use o IDE que preferir. Recomendamos o [Visual Studio](https://www.visualstudio.com/vs) ou o [Visual Studio Code](https://code.visualstudio.com/). As etapas neste documento usam o Visual Studio 2019.

* Uma forma de carregar arquivos .exe para o cluster e executar trabalhos de Pig e Hive. É recomendável [Data Lake ferramentas para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), [Azure PowerShell](/powershell/azure)e [CLI do Azure](/cli/azure/install-azure-cli). As etapas neste documento usam as Ferramentas do Data Lake para Visual Studio para carregar os arquivos e executar o exemplo de consulta do Hive.

    Para obter informações sobre outras maneiras de executar consultas do hive, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md).

* Um Hadoop no cluster do HDInsight. Para obter mais informações sobre como criar um cluster, consulte [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET no HDInsight

Clusters *HDInsight baseados em Linux* usam [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicativos .NET. A versão 4.2.1 do Mono está incluída no HDInsight versão 3.6.

Para obter mais informações sobre compatibilidade de Mono com versões do .NET Framework, consulte [Compatibilidade de Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

Para obter mais informações sobre a versão do .NET Framework e do mono incluído com versões do HDInsight, consulte [versões de componente do hdinsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar os projetos em C\#

As seções a seguir descrevem como criar um projeto C# no Visual Studio para um Apache Hive UDF e um UDF do Apache Pig.

### <a name="apache-hive-udf"></a>UDF do Apache Hive

Para criar um projeto C# para um Apache Hive UDF:

1. Inicie o Visual Studio.

2. Selecione **Criar um novo projeto**.

3. Na janela **criar um novo projeto** , escolha o modelo **aplicativo de console (.NET Framework)** (a versão C#). Em seguida, selecione **Avançar**.

4. Na janela **configurar seu novo projeto** , insira um **nome de projeto** de *HiveCSharp* e navegue até ou crie um **local** para salvar o novo projeto. Em seguida, selecione **Criar**.

5. No IDE do Visual Studio, substitua o conteúdo de *Program.cs* pelo código a seguir:

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

6. Na barra de menus, selecione **criar**  >  **solução de compilação** para compilar o projeto.

7. Feche a solução.

### <a name="apache-pig-udf"></a>UDF do Apache Pig

Para criar um projeto C# para um Apache Hive UDF:

1. Abra o Visual Studio.

2. Na janela **Iniciar**, selecione **Criar projeto**.

3. Na janela **criar um novo projeto** , escolha o modelo **aplicativo de console (.NET Framework)** (a versão C#). Em seguida, selecione **Avançar**.

4. Na janela **configurar seu novo projeto** , insira um **nome de projeto** de *PigUDF* e vá para ou crie um **local** para salvar o novo projeto. Em seguida, selecione **Criar**.

5. No IDE do Visual Studio, substitua o conteúdo de *Program.cs* pelo código a seguir:

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

6. Na barra de menus, escolha **Compilar** compilar  >  **solução** para compilar o projeto.

7. Deixe a solução aberta.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, carregue os aplicativos do hive e do Pig UDF para armazenamento em um cluster HDInsight.

1. No Visual Studio, navegue até **Exibir**  >  **Gerenciador de servidores**.

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse em **Azure**, selecione **conectar à assinatura do Microsoft Azure** e conclua o processo de entrada.

1. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto **(Conta de armazenamento padrão)** é listada.

    ![Conta de armazenamento padrão, cluster HDInsight, Gerenciador de Servidores](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Se essa entrada puder ser expandida, você estará usando uma **conta de armazenamento do Azure** como armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão para o cluster, expanda a entrada e clique duas vezes no **(Contêiner Padrão)**.

    * Se essa entrada não puder ser expandida, você estará usando **Azure data Lake Storage** como o armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada **(Conta de Armazenamento Padrão)**.

1. Para carregar os arquivos .exe, use um dos seguintes métodos:

    * Se você estiver usando uma **conta de armazenamento do Azure**, selecione o ícone **carregar blob** .

        ![Ícone de upload do HDInsight para novo projeto](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Na caixa de diálogo **carregar novo arquivo** , em **nome do arquivo**, selecione **procurar**. Na caixa de diálogo **carregar blob** , vá para a pasta *Bin\debug* do projeto *HiveCSharp* e, em seguida, escolha o arquivo *HiveCSharp.exe* . Por fim, selecione **abrir** e, em seguida, **OK** para concluir o carregamento.

    * Se você estiver usando **Azure data Lake Storage**, clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione **carregar**. Por fim, escolha o arquivo *HiveCSharp.exe* e selecione **abrir**.

    Após o *HiveCSharp.exe* ser carregado, repita o processo de upload para o arquivo *PigUDF.exe*.

## <a name="run-an-apache-hive-query"></a>Executar uma consulta do Apache Hive

Agora você pode executar uma consulta de Hive que usa o aplicativo UDF do hive.

1. No Visual Studio, navegue até **Exibir**  >  **Gerenciador de servidores**.

2. Expanda **Azure** e expanda **HDInsight**.

3. Clique com o botão direito do mouse no cluster em que você implantou o aplicativo *HiveCSharp* e, em seguida, selecione **Escrever uma consulta de Hive**.

4. Use o texto a seguir para a consulta de Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Remova a marca de comentário da instrução `add file` que corresponde ao tipo de armazenamento padrão usado para o cluster.

    Essa consulta seleciona os `clientid` `devicemake` campos, e `devicemodel` de `hivesampletable` e passa os campos para o aplicativo *HiveCSharp.exe* . A consulta espera que o aplicativo retorne três campos, que são armazenados como `clientid`, `phoneLabel` e `phoneHash`. A consulta também espera localizar *HiveCSharp.exe* na raiz do contêiner de armazenamento padrão.

5. Alterne o padrão **interativo** para o **lote** e, em seguida, selecione **Enviar** para enviar o trabalho para o cluster HDInsight. A janela **Resumo do trabalho Hive** é aberta.

6. Selecione **Atualizar** para atualizar o resumo até que o **status do trabalho** seja alterado para **concluído**. Para exibir a saída do trabalho, selecione **saída do trabalho**.

## <a name="run-an-apache-pig-job"></a>Executar um trabalho do Apache Pig

Você também pode executar um trabalho do Pig que usa seu aplicativo Pig UDF.

1. Use o SSH para conectar-se ao cluster HDInsight. (Por exemplo, execute o comando `ssh sshuser@<clustername>-ssh.azurehdinsight.net` .) Para obter mais informações, consulte [usar SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o seguinte comando para iniciar a linha de comando Pig:

    ```shell
    pig
    ```

    Um prompt de `grunt>` é exibido.

3. Digite o seguinte para executar um trabalho do Pig que usa o aplicativo do .NET Framework:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    A `DEFINE` instrução cria um alias do `streamer` para o aplicativo *PigUDF.exe* e o `CACHE` carrega do armazenamento padrão para o cluster. Posteriormente, `streamer` é usado com o `STREAM` operador para processar as linhas individuais contidas no `LOG` e retornar os dados como uma série de colunas.

    > [!NOTE]
    > O nome do aplicativo que é usado para streaming deve ser circundado pelo \` caractere (acento grave) quando com alias e pelo caractere ' (aspa simples) quando usado com `SHIP` .

4. Depois de inserir a última linha, o trabalho deve ser iniciado. Isso retorna saídas semelhantes ao seguinte texto:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Use `exit` para sair do Pig.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar um aplicativo .NET Framework do hive e do Pig no HDInsight. Caso deseje saber como usar o Python com o Hive e o Pig, confira [Usar o Python com o Apache Hive e o Apache Pig no HDInsight](python-udf-hdinsight.md).

Para outras maneiras de usar o hive e para saber mais sobre como usar o MapReduce, consulte os seguintes artigos:

* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
* [Noções básicas do Pig Latin](https://pig.apache.org/docs/latest/basic.html)
