---
title: Usar C# com MapReduce no Hadoop no HDInsight – Azure
description: Saiba como usar C# para criar soluções de MapReduce com Apache Hadoop no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 6e2758fd650547978924c25f8f3faf1e3e7194f6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699378"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Use C# com fluxo de MapReduce no Apache Hadoop no HDInsight

Saiba como usar C# para criar uma solução de MapReduce no HDInsight.

Apache Hadoop streaming permite executar trabalhos MapReduce usando um script ou executável. Aqui, o .NET é usado para implementar o mapeador e o redutor para uma solução de contagem de palavras.

## <a name="net-on-hdinsight"></a>.NET no HDInsight

Os clusters HDInsight usam [mono https://mono-project.com) (](https://mono-project.com) para executar aplicativos .net. A versão 4.2.1 do Mono está incluída no HDInsight versão 3.6. Para obter mais informações sobre a versão do mono incluída com o HDInsight, consulte [Apache Hadoop componentes disponíveis com versões do hdinsight](../hdinsight-component-versioning.md).

Para obter mais informações sobre compatibilidade de Mono com versões do .NET Framework, consulte [Compatibilidade de Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona o Hadoop Streaming

O processo básico usado para streaming neste documento é o seguinte:

1. O Hadoop passa dados para o mapeador (*mapper.exe* neste exemplo) em stdin.
2. O mapeador processa os dados e emite pares de chave/valor delimitados por tabulação para STDOUT.
3. A saída é lida pelo Hadoop e, em seguida, passada para o redutor (*reducer.exe* neste exemplo) em stdin.
4. O redutor lê os pares de chave/valor delimitados por tabulação, processa os dados e, em seguida, emite o resultado como pares de chave/valor delimitados por tabulação no STDOUT.
5. A saída é lido pelo Hadoop e gravada no diretório de saída.

Para mais informações sobre streaming, consulte [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Familiaridade com gravação e compilação de código em C# que se destina ao .NET Framework 4.5.

* Uma forma de carregar arquivos .exe no cluster. As etapas neste documento usam o Data Lake Tools para Visual Studio para carregar os arquivos no armazenamento primário do cluster.

* Se estiver usando o PowerShell, você precisará do [Az Module](/powershell/azure/).

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* O esquema de URI do seu armazenamento primário de clusters. Esse esquema seria `wasb://` para o armazenamento do Azure, `abfs://` por Azure data Lake Storage Gen2 ou `adl://` para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure ou Data Lake Storage Gen2, o URI será `wasbs://` ou `abfss://` , respectivamente.

## <a name="create-the-mapper"></a>Criar o mapeador

No Visual Studio, crie um novo aplicativo de console .NET Framework chamado *mapeador*. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Depois de criar o aplicativo, compile-o para produzir o arquivo de *mapper.exe/bin/Debug/* no diretório do projeto.

## <a name="create-the-reducer"></a>Criar o redutor

No Visual Studio, crie um novo aplicativo de console .NET Framework chamado *redutor*. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Depois de criar o aplicativo, compile-o para produzir o arquivo de *reducer.exe/bin/Debug/* no diretório do projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, você precisa carregar o *mapeador* e os aplicativos *redutor* no armazenamento do HDInsight.

1. No Visual Studio, selecione **Exibir**  >  **Gerenciador de servidores**.

1. Clique com o botão direito do mouse em **Azure**, selecione **conectar-se a Microsoft Azure assinatura...** e conclua o processo de entrada.

1. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto **(Conta de armazenamento padrão)** é listada.

   ![Conta de armazenamento, cluster HDInsight, Gerenciador de Servidores, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Se a entrada **(conta de armazenamento padrão)** puder ser expandida, você estará usando uma **conta de armazenamento do Azure** como armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, expanda a entrada e clique duas vezes em **(contêiner padrão)**.

   * Se a entrada **(conta de armazenamento padrão)** não puder ser expandida, você estará usando **Azure data Lake Storage** como o armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada **(Conta de Armazenamento Padrão)**.

1. Para carregar os arquivos .exe, use um dos seguintes métodos:

    * Se você estiver usando uma **conta de armazenamento do Azure**, selecione o ícone **carregar blob** .

        ![Ícone de upload do HDInsight para mapeador, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Na caixa de diálogo **carregar novo arquivo** , em **nome do arquivo**, selecione **procurar**. Na caixa de diálogo **carregar blob** , vá para a pasta *bin\Debug* do projeto *mapeador* e escolha o arquivo *mapper.exe* . Por fim, selecione **abrir** e, em seguida, **OK** para concluir o carregamento.

    * Para **Azure data Lake Storage**, clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione **carregar**. Por fim, selecione o arquivo *mapper.exe* e, em seguida, selecione **abrir**.

    Após o *mapper.exe* cser carregado, repita o processo de upload para o arquivo *reducer.exe*.

## <a name="run-a-job-using-an-ssh-session"></a>Executar um trabalho: usando uma sessão SSH

O procedimento a seguir descreve como executar um trabalho MapReduce usando uma sessão SSH:

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Use um dos seguintes comandos para iniciar o trabalho MapReduce:

   * Se o armazenamento padrão for o **armazenamento do Azure**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Se o armazenamento padrão for **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Se o armazenamento padrão for **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   A lista a seguir descreve o que cada parâmetro e opção representa:

   |Parâmetro | Descrição |
   |---|---|
   |hadoop-streaming.jar|Especifica o arquivo JAR que contém a funcionalidade de streaming MapReduce.|
   |-arquivos|Especifica o *mapper.exe* e *reducer.exe* arquivos para esse trabalho. A `wasbs:///` `adl:///` declaração de protocolo, ou antes de `abfs:///` cada arquivo é o caminho para a raiz do armazenamento padrão para o cluster.|
   |-mapeador|Especifica o arquivo que implementa o mapeador.|
   |-redutor|Especifica o arquivo que implementa o redutor.|
   |-entrada|Especifica os dados de entrada.|
   |-saída|Especifica o diretório de saída.|

1. Depois que o trabalho MapReduce for concluído, use o seguinte comando para exibir os resultados:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   A seguinte lista é um exemplo dos dados retornados pelos comandos anteriores:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Executar um trabalho: usando o PowerShell

Use o seguinte script de PowerShell para executar um trabalho MapReduce e baixar os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Esse script solicita nome e senha da conta de logon do cluster, juntamente com o nome do cluster HDInsight. Quando o trabalho for concluído, a saída será baixada em um arquivo chamado *output.txt*. O seguinte texto é um exemplo dos dados no arquivo `output.txt`:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Próximas etapas

* [Use o MapReduce no Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).
* [Use uma função definida pelo usuário do C# com Apache Hive e Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Desenvolver programas Java MapReduce](apache-hadoop-develop-deploy-java-mapreduce-linux.md)