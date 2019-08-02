---
title: 'Tutorial: Usar a API de Streams do Apache Kafka – Azure HDInsight '
description: Tutorial - Saiba como usar a API de Streams do Apache Kafka com o Kafka no HDInsight. Essa API permite realizar processamento de fluxo entre tópicos no Kafka.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 0639ecaa0e4ae0581a6c88e1ea9a47de870a8355
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446389"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Tutorial: Usar a API de streams do Apache Kafka no Azure HDInsight

Saiba como criar um aplicativo que usa a API de Streams do Apache Kafka e executá-lo com o Kafka no HDInsight.

O aplicativo usado neste tutorial é uma contagem de palavras de streaming. Ele lê dados de texto de um tópico Kafka, extrai palavras individuais e, em seguida, armazena a palavra e a contagem em outro tópico Kafka.

O processamento de fluxo do Kafka normalmente é feito usando o Apache Spark ou Apache Storm. A versão Kafka 1.1.0 (no HDInsight 3.5 e 3.6) introduziu a API de Streams do Kafka. Essa API permite transformar fluxos de dados entre tópicos de entrada e saída. Em alguns casos, isso pode ser uma alternativa para criar uma solução de streaming Spark ou Storm.

Para obter mais informações sobre Streams do Kafka, consulte a documentação[Introdução a Streams](https://kafka.apache.org/10/documentation/streams/) em Apache.org.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Compreender o código
> * Compilar e implantar o aplicativo
> * Configurar tópicos Kafka
> * Executar o código

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Kafka no HDInsight 3.6. Para saber como criar um Kafka no cluster HDInsight, consulte o documento [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md).

* Conclua as etapas no documento [API de Produtor e Consumidor do Apache Kafka](apache-kafka-producer-consumer-api.md). As etapas neste documento usam o aplicativo de exemplo e os tópicos criados neste tutorial.

* [JDK (Java Developer Kit) versão 8](https://aka.ms/azure-jdks) ou um equivalente, como o OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

O aplicativo de exemplo está localizado em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), além do subdiretório `Streaming`. O aplicativo consiste de dois arquivos:

* `pom.xml`: este arquivo define as dependências do projeto, versão do Java e os métodos de empacotamento.
* `Stream.java`: Este arquivo implementa a lógica de streaming.

### <a name="pomxml"></a>Pom.xml

As coisas importantes para entender no arquivo `pom.xml` são:

* Dependências: Este projeto depende da API do Kafka Streams, que é fornecida pelo pacote `kafka-clients`. O seguinte código XML define essa dependência:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    A entrada `${kafka.version}` é declarada na seção `<properties>..</properties>` de `pom.xml`, e está configurada para a versão Kafka do cluster HDInsight.

* Plug-ins: os plug-ins do Maven oferecem várias funcionalidades. Neste projeto, são usados os seguintes plug-ins:

    * `maven-compiler-plugin`: usado para definir a versão do Java usada pelo projeto como 8. Java 8 é exigido pelo HDInsight 3.6.
    * `maven-shade-plugin`: usado para gerar um uber jar que contém esse aplicativo, bem como todas as dependências. Ele também é usado para definir o ponto de entrada do aplicativo, para que você possa executar diretamente o arquivo Jar sem a necessidade de especificar a classe principal.

### <a name="streamjava"></a>Stream.java

O arquivo [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) usa a API do Streams para implementar um aplicativo de contagem de palavras. Ele lê dados de um tópico Kafka chamado `test` e grava as contagens de palavras em um tópico chamado `wordcounts`.

O código a seguir define o aplicativo de contagem de palavras:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>Compilar e implantar o exemplo

Para criar e implantar o projeto para o Kafka no Cluster HDInsight, utilize as seguintes etapas:

1. Defina o diretório atual para o local do diretório `hdinsight-kafka-java-get-started-master\Streaming` e, em seguida, use o seguinte comando para criar um pacote jar:

    ```cmd
    mvn clean package
    ```

    Este comando cria o pacote em `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `clustername` pelo nome do cluster. Use o seguinte comando para copiar o arquivo `kafka-streaming-1.0-SNAPSHOT.jar` para o Cluster HDInsight. Se solicitado, insira a senha para a conta de usuário SSH.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Criar tópicos do Apache Kafka

1. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Abra uma conexão SSH para o cluster inserindo o seguinte comando. Se solicitado, insira a senha para a conta de usuário SSH.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Instale [jq](https://stedolan.github.io/jq/), um processador JSON de linha de comando. Na conexão SSH aberta, digite o seguinte comando para instalar o `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Configurar variáveis de ambiente. Substitua `PASSWORD` e `CLUSTERNAME` pela senha de logon do cluster e pelo nome do cluster, respectivamente, e digite o comando:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Extraia o nome do cluster com grafia correta de maiúsculas e minúsculas. A grafia de maiúsculas e minúsculas real do nome do cluster pode ser diferente do esperado, dependendo de como o cluster foi criado. Esse comando obterá a grafia de maiúsculas e minúsculas real, a armazenará em uma variável e, em seguida, exibirá o nome com grafia correta de maiúsculas e minúsculas e o nome fornecido anteriormente. Digite o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Para obter os hosts de broker Kafka e os hosts Apache Zookeeper, use os comandos a seguir. Quando solicitado, insira a senha para a conta de logon do cluster (admin). Você receberá uma solicitação de senha duas vezes.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`;
    export KAFKABROKERS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`;
    ```

6. Para criar os tópicos usados pela operação de streaming, use os seguintes comandos:

    > [!NOTE]  
    > Você pode receber um erro que o tópico `test` já existe. Não há problema nisso, pois ele pode ter sido criado no tutorial de API de produtor e consumidor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Os tópicos são usados para as seguintes finalidades:

   * `test`: É neste tópico que os registros são recebidos. O aplicativo de streaming faz a leitura daqui.
   * `wordcounts`: É neste tópico que o aplicativo de transmissão armazena sua saída.
   * `RekeyedIntermediateTopic`: Este tópico é usado para reparticionar dados conforme a contagem é atualizada pelo operador `countByKey`.
   * `wordcount-example-Counts-changelog`: Este tópico é um armazenamento de estado usado pela operação `countByKey`

    O Kafka no HDInsight também pode ser configurado para criar tópicos automaticamente. Para obter mais informações, consulte o documento [Configurar a criação automática de tópicos](apache-kafka-auto-create-topics.md).

## <a name="run-the-code"></a>Executar o código

1. Para iniciar o aplicativo de streaming como processo em segundo plano, use o seguinte comando:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Você pode receber um aviso sobre o Apache log4j. Você pode ignorar esse aviso.

2. Para enviar registros para o tópico `test`, use o seguinte comando para iniciar o aplicativo produtor:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Após a conclusão do produtor, use o seguinte comando para exibir as informações armazenadas no tópico `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    Os parâmetros `--property` informam ao consumidor do console para imprimir a chave (palavra) juntamente com a contagem (valor). Esses parâmetros também configuram o desserializador a ser usado ao fazer a leitura desses valores do Kafka.

    A saída é semelhante ao texto a seguir:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    O parâmetro `--from-beginning` configura o consumidor para começar do início dos registros armazenados no tópico. A contagem aumenta sempre que uma palavra é encontrada, logo o tópico contém várias entradas para cada palavra, com uma contagem crescente.

4. Use __Ctrl + C__ para sair do produtor. Continue usando __Ctrl + C__ para sair do aplicativo e do consumidor.

5. Para excluir os tópicos usados pela operação de streaming, use os seguintes comandos:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API de Streams do Apache Kafka com Kafka no HDInsight. Confira o seguinte para saber mais sobre como trabalhar com o Kafka.

> [!div class="nextstepaction"]
> [Analisar logs do Apache Kafka](apache-kafka-log-analytics-operations-management.md)