---
title: MapReduce com Hadoop no HDInsight | Microsoft Docs
description: Saiba como executar trabalhos do MapReduce no Hadoop em clusters HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 625bbf802888b70ccac57b7da3d7060a9706ddec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Usar o MapReduce no Hadoop do HDInsight

Saiba como executar trabalhos do MapReduce em clusters HDInsight. Use a tabela a seguir para descobrir várias maneiras pelas quais o MapReduce pode ser usado com o HDInsight:

| **Use isto**... | **...Para fazer isso** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |Usar o comando Hadoop por meio de **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [REST](hdinsight-hadoop-use-mapreduce-curl.md) |Enviar o trabalho remotamente usando a **REST** (os exemplos usam cURL) |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |Enviar o trabalho remotamente usando o **Windows PowerShell** |Linux ou Windows |Windows |
| [Área de trabalho remota](hdinsight-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 e 3.3) |Usar o comando Hadoop por meio da **Área de trabalho remota** |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="whatis"></a>O que é o MapReduce

O MapReduce do Hadoop é uma estrutura de software para escrever trabalhos que processam grandes quantidades de dados. Dados de entrada são divididos em partes independentes. Cada bloco é processado em paralelo em todos os nós no cluster. Um trabalho do MapReduce consiste em duas funções:

* **Mapeador**: consome dados de entrada, analisa-os (normalmente com operações de classificação e filtro) e emite tuplas (pares chave-valor)

* **Redutor**: consome tuplas emitidas pelo Mapeador e executa uma operação de resumo que cria um resultado menor e combinado dos dados do Mapeador

Um exemplo básico de trabalho de contagem de palavras do MapReduce está ilustrado no diagrama abaixo:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Ele emite um par de chave/valor cada vez que ocorre uma palavra seguida de um 1. A saída será classificada antes de ser enviada ao redutor.
* Em seguida, o redutor soma essas contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguido pela soma de suas ocorrências.

O MapReduce pode ser implementado em várias linguagens. Java é a implementação mais comum e é usado para fins de demonstração neste documento.

## <a name="development-languages"></a>Linguagens de desenvolvimento

As linguagens ou frameworks que são baseados em Java e a Máquina Virtual Java podem ser executadas diretamente como um trabalho do MapReduce. O exemplo usado neste documento é um aplicativo MapReduce em Java. Linguagens não Java, como C#, Python ou executáveis autônomos, devem usar o streaming do Hadoop.

O streaming do Hadoop se comunica com o mapeador e redutor por STDIN e STDOUT. O mapeador e redutor leem os dados uma linha por vez do STDIN e gravam a saída em STDOUT. Cada linha lida ou emitida pelo mapeador e redutor deve estar no formato de um par de chave/valor, delimitado por um caractere de tabulação:

    [key]/t[value]

Para saber mais, confira [Streaming do Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para ver exemplos do uso de streaming com o HDInsight do Hadoop, consulte os seguintes documentos:

* [Desenvolver trabalhos do MapReduce em C#](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Desenvolver trabalhos do MapReduce Python](hdinsight-hadoop-streaming-python.md)

## <a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados no diretório `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão do cluster. Neste documento, usamos o arquivo `/example/data/gutenberg/davinci.txt`. Esse arquivo contém o cadernos de Leonardo Da Vinci.

## <a id="job"></a>MapReduce de exemplo

Um exemplo de aplicativo de contagem de palavras do MapReduce está incluído no seu cluster HDInsight. Este exemplo está localizado em `/example/jars/hadoop-mapreduce-examples.jar` no armazenamento padrão para o cluster.

O seguinte código Java é o código-fonte do aplicativo MapReduce contida no arquivo `hadoop-mapreduce-examples.jar`:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Para obter instruções para escrever seus próprios aplicativos MapReduce, consulte os seguintes documentos:

* [Desenvolver aplicativos Java MapReduce para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Desenvolver aplicativos Python MapReduce para HDInsight](hdinsight-hadoop-streaming-python.md)

## <a id="run"></a>Executar o MapReduce

O HDInsight pode executar trabalhos de HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use isto**... | **...Para fazer isso** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |Usar o comando Hadoop por meio de **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) |Enviar o trabalho remotamente usando a **REST** |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |Enviar o trabalho remotamente usando o **Windows PowerShell** |Linux ou Windows |Windows |
| [Área de trabalho remota](hdinsight-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 e 3.3) |Usar o comando Hadoop por meio da **Área de trabalho remota** |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Próximas etapas

Para saber mais sobre como trabalhar com os dados no HDInsight, consulte os seguintes documentos:

* [Desenvolver programas Java MapReduce para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Desenvolver programas MapReduce de streaming do Hadoop para o HDInsight](hdinsight-hadoop-streaming-python.md)

* [Desenvolver trabalhos de MapReduce do Scalding com o Apache Hadoop no HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Usar o Hive com o HDInsight][hdinsight-use-hive]

* [Usar o Pig com o HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
