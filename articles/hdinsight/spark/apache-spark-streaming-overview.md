---
title: Spark Streaming no Azure HDInsight
description: Como usar aplicativos Spark Streaming em clusters Spark do HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/11/2019
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 19d77d4aa49008232a01cd3ac2761a796505a35c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098211"
---
# <a name="overview-of-apache-spark-streaming"></a>Visão geral de Streaming do Apache Spark

O [Apache Spark](https://spark.apache.org/) Streaming fornece o processamento do fluxo de dados nos clusters do HDInsight Spark, com a garantia de que qualquer evento de entrada seja processado exatamente uma vez, mesmo que ocorra uma falha no nó. Um Spark Stream é um trabalho de longa execução que recebe dados de entrada de uma ampla variedade de fontes, incluindo Hubs de Eventos do Azure, um Hub IoT do Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), soquetes TCP brutos ou do monitoramento [sistemas de arquivos do Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Ao contrário de um processo exclusivamente controlado por evento, um Spark Stream envia os dados de entrada em lote para janelas de tempo, como uma fatia de dois segundos e, em seguida, transforma cada lote de dados usando operações de mapa, redução, união e extração. Em seguida, o Spark Stream grava os dados transformados em sistemas de arquivos, bancos de dados, painéis e no console.

![Processamento de Stream com o HDInsight e o Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Os aplicativos Spark Streaming precisam aguardar uma fração de segundo para coletar cada *microlote* de eventos antes de enviar esse lote para processamento. Por outro lado, um aplicativo controlado por evento processa cada evento imediatamente. A latência do Spark Streaming normalmente fica abaixo de alguns segundos. Os benefícios da abordagem de microlote são um processamento de dados mais eficiente e cálculos de agregação mais simples.

## <a name="introducing-the-dstream"></a>Introdução ao DStream

O Spark Streaming é um fluxo contínuo de entrada de dados que usa um *fluxo discretizado* chamado DStream. Um DStream pode ser criado com base em fontes de entrada, como os Hubs de Eventos ou o Kafka, ou com a aplicação de transformações em outro DStream.

Um DStream oferece uma camada de abstração sobre os dados brutos de evento. 

Inicie com um evento único, como uma leitura de temperatura de um termostato conectado. Quando esse evento é recebido pelo aplicativo Spark Streaming, ele é armazenado de forma confiável, em um local em que é replicado em vários nós. Essa tolerância a falhas garante que a falha de qualquer nó não resultará na perda do evento. O núcleo do Spark usa uma estrutura de dados que distribui dados em vários nós no cluster, em que cada nó geralmente mantém seu próprios dados na memória para um melhor desempenho. Essa estrutura de dados é chamada de RDD (*conjunto de dados distribuído resiliente*).

Cada RDD representa os eventos coletados por um período de tempo definido pelo usuário, denominado *intervalo de lote*. Com a expiração de cada intervalo de lote, um novo RDD é produzido, contendo todos os dados desse intervalo. O conjunto contínuo de RDDs é coletado em um DStream. Por exemplo, se o intervalo de lote for de um segundo, o DStream emitirá um lote a cada segundo com um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece em um desses lotes. Um aplicativo Spark Streaming processa os lotes que contêm os eventos e, por fim, atua nos dados armazenados em cada RDD.

![Exemplo de DStream com Eventos de Temperatura ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Estrutura de um aplicativo Spark Streaming

O aplicativo Spark Streaming é de longa execução, recebe dados de fontes de ingestão, aplica transformações a fim de processá-los e, em seguida, envia esses dados por push para um ou mais destinos. A estrutura de um aplicativo Spark Streaming tem uma parte estática e uma parte dinâmica. A parte estática define a origem dos dados, como eles serão processados e para que local os resultados devem ser enviados. A parte dinâmica executa o aplicativo por tempo indefinido, aguardando um sinal de parada.

Por exemplo, o aplicativo simples a seguir recebe uma linha de texto em um soquete TCP e conta o número de vezes que cada palavra é exibida.

### <a name="define-the-application"></a>Definir o aplicativo

A definição da lógica do aplicativo tem quatro etapas:

1. Criar um StreamingContext.
2. Criar um DStream com base no StreamingContext.
3. Aplicar transformações ao DStream.
4. Enviar os resultados.

Essa definição é estática e nenhum dado é processado até que o aplicativo seja executado.

#### <a name="create-a-streamingcontext"></a>Criar um StreamingContext
Crie um StreamingContext com base no SparkContext que aponta para o cluster. Ao criar um StreamingContext, especifique o tamanho do lote em segundos, por exemplo:

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Criar um DStream

Com a instância de StreamingContext, crie um DStream de entrada para a fonte de entrada. Nesse caso, o aplicativo está observando se novos arquivos aparecerão no armazenamento padrão anexado ao cluster HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Aplicar transformações

O processamento é implementado ao aplicar transformações ao DStream. Esse aplicativo recebe uma linha de texto de cada vez do arquivo, divide cada linha em palavras e, em seguida, usa um padrão map-reduce para contar o número de vezes que cada palavra aparece.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Enviar os resultados

Envie os resultados da transformação por push para sistemas de destino aplicando operações de saída. Nesse caso, o resultado de cada execução por meio da computação é impresso na saída do console.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Executar o aplicativo
Inicie o aplicativo de streaming e execute-o até receber um sinal de encerramento.

```
ssc.start()
ssc.awaitTermination()
```

Para obter detalhes sobre a API do Spark Stream, junto com as fontes de eventos, transformações e operações de saída suportadas, consulte [Guia de programação de streaming do Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

O aplicativo de exemplo a seguir é independente, assim, é possível executá-lo dentro de um [Bloco de Anotações do Jupyter](apache-spark-jupyter-notebook-kernels.md). Este exemplo cria uma fonte de dados fictícia na classe DummySource, que gera o valor de um contador e da hora atual em milissegundos a cada cinco segundos. Um novo objeto de StreamingContext tem um intervalo de lote de 30 segundos. Sempre que um lote é criado, o aplicativo de streaming examina o RDD produzido, converte-o em um DataFrame do Spark e cria uma tabela temporária no DataFrame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()
```

Aguarde cerca de 30 segundos depois de iniciar o aplicativo acima.  Em seguida, você pode consultar o DataFrame periodicamente para ver o conjunto atual de valores presente no lote, por exemplo, usando esta consulta SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

A saída resultante é semelhante a:

| value | tempo real |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Há seis valores, pois a DummySource cria um valor a cada 5 segundos e o aplicativo emite um lote a cada 30 segundos.

## <a name="sliding-windows"></a>Janelas deslizantes

Para fazer cálculos de agregação no DStream por um período, por exemplo, para obter uma temperatura média nos últimos dois segundos, use as operações de *janela deslizante* incluídas no Spark Streaming. Uma janela deslizante tem uma duração (a duração da janela) e o intervalo durante o qual o conteúdo da janela é avaliado (o intervalo de deslizamento).

As janelas deslizantes podem se sobrepor; por exemplo, é possível definir uma janela com uma duração de dois segundos, que desliza a cada segundo. Isso significa que sempre que você fizer um cálculo de agregação, a janela incluirá os dados do último segundo da janela anterior, bem como os novos dados do próximo segundo.

![Exemplo de Janela Inicial com Eventos de Temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Exemplo de Janela com Eventos de Temperatura após o Deslizamento](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

O exemplo a seguir atualiza o código que usa a DummySource, para coletar os lotes em uma janela com uma duração e um deslizamento, ambos de um minuto.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
} 

// Start the stream processing
ssc.start()
```

Após o primeiro minuto, há 12 entradas – seis entradas de cada um dos dois lotes coletados na janela.

| value | tempo real |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

As funções de janela deslizante disponíveis na API Spark Streaming incluem window, countByWindow, reduceByWindow e countByValueAndWindow. Para obter detalhes sobre essas funções, consulte [Transformações em DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ponto de verificação

Para fornecer resiliência e tolerância a falhas, o Spark Streaming conta com um ponto de verificação para garantir que o processamento do fluxo continue sem interrupções, mesmo que ocorram falhas nos nós. No HDInsight, o Spark cria pontos de verificação para um armazenamento durável (Armazenamento do Azure ou Data Lake Storage). Esses pontos de verificação armazenam os metadados sobre o aplicativo de streaming, como a configuração, as operações definidas pelo aplicativo e quaisquer lotes enfileirados que ainda não foram processados. Em alguns casos, os pontos de verificação também incluirão o salvamento dos dados nos RDDs, a fim de reconstruir o estado dos dados mais rapidamente em relação ao que está presente nos RDDs gerenciados pelo Spark.

## <a name="deploying-spark-streaming-applications"></a>Implantar aplicativos Spark Streaming

Normalmente, você cria um aplicativo Spark Streaming localmente em um arquivo JAR e, em seguida, implanta-o no Spark no HDInsight copiando o arquivo JAR para o armazenamento padrão anexado ao cluster HDInsight. Você pode iniciar o aplicativo usando as APIs REST do LIVY disponíveis no cluster usando uma operação POST. O corpo do POST inclui um documento JSON que fornece o caminho para o JAR, o nome da classe cujo método principal define e executa o aplicativo de streaming e, opcionalmente, os requisitos de recursos do trabalho (como a quantidade de executores, memória e núcleos) e as definições de configuração exigidas pelo código do aplicativo.

![Implantando um aplicativo Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O status de todos os aplicativos também pode ser verificado com uma solicitação GET em um ponto de extremidade LIVY. Por fim, encerre um aplicativo em execução emitindo uma solicitação DELETE no ponto de extremidade do LIVY. Para detalhes sobre a API LIVY, veja [ Trabalhos remotos com o Apache LIVY ](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar um cluster Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de Streaming do Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Iniciar trabalhos do Apache Spark remotamente com LIVY Apache](apache-spark-livy-rest-interface.md)
