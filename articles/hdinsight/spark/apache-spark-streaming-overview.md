---
title: Spark Streaming no Azure HDInsight
description: Como usar Apache Spark aplicativos de streaming em clusters Spark do HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: bde6c5b2bad12df8642dd3c9b4a49548f7bc9a6d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929512"
---
# <a name="overview-of-apache-spark-streaming"></a>Visão geral de Streaming do Apache Spark

[Apache Spark](https://spark.apache.org/) O streaming fornece processamento de fluxo de dados em clusters do HDInsight Spark. Com uma garantia de que qualquer evento de entrada seja processado exatamente uma vez, mesmo se ocorrer uma falha de nó. Um fluxo do Spark é um trabalho de longa execução que recebe dados de entrada de uma ampla variedade de fontes, incluindo hubs de eventos do Azure. Além disso: Hub IoT do Azure, Apache Kafka, Apache Flume, Twitter, `ZeroMQ` Soquetes TCP brutos ou monitoramento Apache Hadoop sistemas de de yarn. Ao contrário de um processo exclusivamente controlado por eventos, um fluxo do Spark gera dados de entrada em janelas de tempo. Como uma fatia de 2 segundos e, em seguida, transforma cada lote de dados usando operações de mapear, reduzir, unir e extrair. Em seguida, o Spark Stream grava os dados transformados em sistemas de arquivos, bancos de dados, painéis e no console.

![Processamento de Stream com o HDInsight e o Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Os aplicativos de streaming do Spark devem aguardar uma fração de um segundo para coletar cada um `micro-batch` dos eventos antes de enviar esse lote para processamento. Por outro lado, um aplicativo controlado por evento processa cada evento imediatamente. A latência do Spark Streaming normalmente fica abaixo de alguns segundos. Os benefícios da abordagem de microlote são um processamento de dados mais eficiente e cálculos de agregação mais simples.

## <a name="introducing-the-dstream"></a>Introdução ao DStream

O Spark Streaming é um fluxo contínuo de entrada de dados que usa um *fluxo discretizado* chamado DStream. Um DStream pode ser criado A partir de fontes de entrada, como hubs de eventos ou Kafka. Ou aplicando transformações em outro DStream.

Um DStream oferece uma camada de abstração sobre os dados brutos de evento.

Inicie com um evento único, como uma leitura de temperatura de um termostato conectado. Quando esse evento chega ao seu aplicativo Spark streaming, o evento é armazenado de forma confiável, onde é replicado em vários nós. Essa tolerância a falhas garante que a falha de um único nó não resultará na perda do evento. O núcleo do Spark usa uma estrutura de dados que distribui dados em vários nós no cluster. Onde cada nó geralmente mantém seus próprios dados na memória para obter o melhor desempenho. Essa estrutura de dados é chamada de RDD (*conjunto de dados distribuído resiliente*).

Cada RDD representa os eventos coletados por um período de tempo definido pelo usuário, denominado *intervalo de lote*. Com a expiração de cada intervalo de lote, um novo RDD é produzido, contendo todos os dados desse intervalo. O conjunto contínuo de RDDs é coletado em um DStream. Por exemplo, se o intervalo de lote for de um segundo, o DStream emitirá um lote a cada segundo com um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece em um desses lotes. Um aplicativo Spark Streaming processa os lotes que contêm os eventos e, por fim, atua nos dados armazenados em cada RDD.

![Exemplo de DStream com Eventos de Temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Estrutura de um aplicativo Spark Streaming

Um aplicativo Spark streaming é um aplicativo de execução longa que recebe dados de fontes de ingestão. Aplica transformações para processar os dados e, em seguida, envia os dados para um ou mais destinos. A estrutura de um aplicativo Spark Streaming tem uma parte estática e uma parte dinâmica. A parte estática define de onde vêm os dados, o processamento a ser feito nos dados. E onde os resultados devem ir. A parte dinâmica executa o aplicativo por tempo indefinido, aguardando um sinal de parada.

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

Com a instância de StreamingContext, crie um DStream de entrada para a fonte de entrada. Nesse caso, o aplicativo está observando a aparência dos novos arquivos no armazenamento anexado padrão.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Aplicar transformações

O processamento é implementado ao aplicar transformações ao DStream. Esse aplicativo recebe uma linha de texto por vez do arquivo, divide cada linha em palavras. E, em seguida, usa um padrão de redução de mapa para contar o número de vezes que cada palavra é exibida.

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

Para obter detalhes sobre a API do Spark Stream, consulte [Guia de programação de Apache Spark streaming](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

O aplicativo de exemplo a seguir é independente, assim, é possível executá-lo dentro de um [Bloco de Anotações do Jupyter](apache-spark-jupyter-notebook-kernels.md). Este exemplo cria uma fonte de dados fictícia na classe DummySource, que gera o valor de um contador e da hora atual em milissegundos a cada cinco segundos. Um novo objeto de StreamingContext tem um intervalo de lote de 30 segundos. Toda vez que um lote é criado, o aplicativo de streaming examina o RDD produzido. Em seguida, converte o RDD em um dataframe Spark e cria uma tabela temporária sobre o dataframe.

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

Aguarde cerca de 30 segundos depois de iniciar o aplicativo acima.  Em seguida, você pode consultar o dataframe periodicamente para ver o conjunto atual de valores presentes no lote, por exemplo, usando esta consulta SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

A saída resultante é semelhante à seguinte saída:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Há seis valores, pois a DummySource cria um valor a cada 5 segundos e o aplicativo emite um lote a cada 30 segundos.

## <a name="sliding-windows"></a>Janelas deslizantes

Para fazer cálculos agregados em seu DStream durante um período de tempo, por exemplo, para obter uma temperatura média nos últimos dois segundos, use as `sliding window` operações incluídas no Spark streaming. Uma janela deslizante tem uma duração (a duração da janela) e o intervalo durante o qual o conteúdo da janela é avaliado (o intervalo de deslizamento).

As janelas deslizantes podem se sobrepor; por exemplo, é possível definir uma janela com uma duração de dois segundos, que desliza a cada segundo. Essa ação significa que sempre que você fizer um cálculo de agregação, a janela incluirá dados do último segundo da janela anterior. E todos os novos dados no próximo segundo.

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

| value | time |
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

## <a name="checkpointing"></a>Definindo o ponto de verificação

Para fornecer resiliência e tolerância a falhas, o Spark Streaming conta com um ponto de verificação para garantir que o processamento do fluxo continue sem interrupções, mesmo que ocorram falhas nos nós. O Spark cria pontos de verificação para o armazenamento durável (armazenamento do Azure ou Data Lake Storage). Esses pontos de verificação armazenam metadados do aplicativo de streaming, como a configuração e as operações definidas pelo aplicativo. Além disso, todos os lotes que foram enfileirados, mas ainda não foram processados. Às vezes, os pontos de verificação também incluirão salvar os dados no RDDs para reconstruir mais rapidamente o estado dos dados a partir do que está presente no RDDs gerenciado pelo Spark.

## <a name="deploying-spark-streaming-applications"></a>Implantar aplicativos Spark Streaming

Normalmente, você cria um aplicativo de streaming do Spark localmente em um arquivo JAR. Em seguida, implante-o no Spark no HDInsight copiando o arquivo JAR para o armazenamento anexado padrão. Você pode iniciar o aplicativo usando as APIs REST do LIVY disponíveis no cluster usando uma operação POST. O corpo da POSTAgem inclui um documento JSON que fornece o caminho para o JAR. E o nome da classe cujo método principal define e executa o aplicativo de streaming e, opcionalmente, os requisitos de recurso do trabalho (como o número de executores, memória e núcleos). Além disso, todas as definições de configuração necessárias ao código do aplicativo.

![Implantando um aplicativo Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O status de todos os aplicativos também pode ser verificado com uma solicitação GET em um ponto de extremidade LIVY. Por fim, você pode encerrar um aplicativo em execução emitindo uma solicitação de exclusão no ponto de extremidade LIVY. Para detalhes sobre a API LIVY, veja [ Trabalhos remotos com o Apache LIVY ](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar um cluster Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de Streaming do Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Visão geral do Streaming Estruturado do Apache Spark](apache-spark-structured-streaming-overview.md)
