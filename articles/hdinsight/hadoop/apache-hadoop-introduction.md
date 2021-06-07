---
title: O que são Apache Hadoop e MapReduce – Azure HDInsight
description: Uma introdução ao HDInsight e aos componentes e a pilha de tecnologias do Apache Hadoop.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: ad1f7422919f224889db84a2599ad3c1c48efcc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863803"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>O que é o Apache Hadoop no Azure HDInsight?

O [Apache Hadoop](https://hadoop.apache.org/) era a estrutura de código aberto original para processamento distribuído e análise de conjuntos de Big Data em clusters. O ecossistema do Hadoop inclui software e utilitários relacionados, inclusive o Apache Hive, o Apache HBase, o Spark, o Kafka e muitos outros.

O HDInsight do Azure é um serviço de análise totalmente gerenciado, completo e de fonte aberta na nuvem para empresas. O tipo de cluster do Apache Hadoop no Azure HDInsight permite usar o [HDFS (Sistema de Arquivos Distribuído do Apache Hadoop)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), [YARN do Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) e gerenciamento de recursos, bem como um modelo de programação [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) simples, para processar e analisar dados em lote em paralelo.  Os clusters Hadoop no HDInsight são compatíveis com o [Armazenamento de blobs do Azure](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) ou [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

Para ver os componentes disponíveis da pilha de tecnologia do Hadoop no HDInsight, confira [Componentes e versões disponíveis com o HDInsight](../hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>O que é o MapReduce

O [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) é uma estrutura de software para gravar trabalhos que processam grandes quantidades de dados. Dados de entrada são divididos em partes independentes. Cada bloco é processado em paralelo em todos os nós no cluster. Um trabalho do MapReduce consiste em duas funções:

* **Mapeador**: consome dados de entrada, analisa-os (normalmente com operações de classificação e filtro) e emite tuplas (pares chave-valor)

* **Redutor**: consome tuplas emitidas pelo Mapeador e executa uma operação de resumo que cria um resultado menor e combinado dos dados do Mapeador

Um exemplo básico de trabalho de contagem de palavras do MapReduce está ilustrado no diagrama abaixo:

 :::image type="content" source="./media/apache-hadoop-introduction/hdi-word-count-diagram.gif" alt-text="HDI.WordCountDiagram" border="true":::

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Ele emite um par de chave/valor cada vez que ocorre uma palavra seguida de um 1. A saída será classificada antes de ser enviada ao redutor.
* Em seguida, o redutor soma essas contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguido pela soma de suas ocorrências.

O MapReduce pode ser implementado em várias linguagens. Java é a implementação mais comum e é usado para fins de demonstração neste documento.

## <a name="development-languages"></a>Linguagens de desenvolvimento

As linguagens ou estruturas baseadas em Java e a Máquina Virtual Java podem ser executadas diretamente como um trabalho do [MapReduce](..//hadoop/submit-apache-hadoop-jobs-programmatically.md). O exemplo usado neste documento é um aplicativo MapReduce em Java. Linguagens não Java, como C#, Python ou executáveis autônomos devem usar o **streaming do Hadoop**.

O streaming do Hadoop se comunica com o mapeador e redutor por STDIN e STDOUT. O mapeador e redutor leem os dados uma linha por vez do STDIN e gravam a saída em STDOUT. Cada linha lida ou emitida pelo mapeador e redutor deve estar no formato de um par de chave/valor, delimitado por um caractere de tabulação:

`[key]\t[value]`

Para saber mais, confira [Streaming do Hadoop](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Para ver exemplos do uso de streaming do Hadoop com o HDInsight, confira os seguintes documentos:

* [Desenvolver trabalhos do MapReduce em C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Por onde começar

* [Início Rápido: Criar cluster do Apache Hadoop no Azure HDInsight usando o portal do Azure](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Tutorial: Enviar trabalhos do Apache Hadoop no HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Desenvolver programas Java MapReduce para o Apache Hadoop no HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Usar o Apache Hive como uma ferramenta ETL (extração, transformação e carregamento)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [ETL (extração, transformação e carregamento) em escala](../hadoop/apache-hadoop-etl-at-scale.md)
* [Operacionalize um pipeline de análise de dados](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar cluster do Apache Hadoop no HDInsight usando o portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Criar um cluster do Apache Hadoop no HDInsight usando o modelo do ARM](../hadoop/apache-hadoop-linux-tutorial-get-started.md)