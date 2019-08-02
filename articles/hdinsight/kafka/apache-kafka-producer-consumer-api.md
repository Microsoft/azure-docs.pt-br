---
title: 'Tutorial: Usar as APIs de produtor e consumidor do Apache Kafka – Azure HDInsight '
description: Saiba como utilizar as APIs de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Neste tutorial, você aprenderá como usar essas APIs com Kafka no HDInsight de um aplicativo Java.
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 7a23d30e940417a6191cf14ad5d60159bd11c3da
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446408"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Usar as APIs de produtor e consumidor do Apache Kafka

Saiba como utilizar as APIs de produtor e consumidor do Apache Kafka com o Kafka no HDInsight.

A API de produtor do Kafka permite que os aplicativos enviam fluxos de dados para o cluster Kafka. A API de consumidor do Kafka permite que os aplicativos leiam fluxos de dados do cluster.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Pré-requisitos
> * Compreender o código
> * Compilar e implantar o aplicativo
> * Executar o aplicativo no cluster

Para obter mais informações sobre as APIs, consulte a documentação do Apache sobre [API de Produtor](https://kafka.apache.org/documentation/#producerapi) e [API de Consumidor](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Pré-requisitos

* Apache Kafka no HDInsight 3.6. Para saber como criar um Kafka no cluster HDInsight, confira [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md) (Começar com o Apache Kafka no HDInsight).

* [JDK (Java Developer Kit) versão 8](https://aka.ms/azure-jdks) ou um equivalente, como o OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

O aplicativo de exemplo está localizado em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), além do subdiretório `Producer-Consumer`. O aplicativo consiste principalmente em quatro arquivos:

* `pom.xml`: este arquivo define as dependências do projeto, versão do Java e os métodos de empacotamento.
* `Producer.java`: este arquivo envia sentenças aleatórias para o Kafka usando a API de produtor.
* `Consumer.java`: este arquivo usa a API de consumidor para ler dados do Kafka e emiti-los para STDOUT.
* `Run.java`: a interface de linha de comando usada para executar o código de produtor e consumidor.

### <a name="pomxml"></a>Pom.xml

As coisas importantes para entender no arquivo `pom.xml` são:

* Dependências: este projeto depende das APIs de produtor e consumidor do Kafka, fornecidas pelo pacote `kafka-clients`. O seguinte código XML define essa dependência:

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

    * `maven-compiler-plugin`: usado para definir a versão do Java usada pelo projeto como 8. Essa é a versão do Java usado pelo HDInsight 3.6.
    * `maven-shade-plugin`: usado para gerar um uber jar que contém esse aplicativo, bem como todas as dependências. Ele também é usado para definir o ponto de entrada do aplicativo, para que você possa executar diretamente o arquivo Jar sem a necessidade de especificar a classe principal.

### <a name="producerjava"></a>Producer.java

O produtor se comunica com os hosts de agente de Kafka (nós de trabalho) e envia dados a um tópico Kafka. O seguinte snippet de código é do arquivo [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) do [repositório GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra como definir as propriedades de produtor:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

O cliente se comunica com os hosts de broker Kafka (nós de trabalho) e lê registros em um loop. O seguinte trecho de código do arquivo [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) define as propriedades do consumidor:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

Nesse código, o consumidor é configurado para ler a partir do início do tópico (`auto.offset.reset` é definido como `earliest`.)

### <a name="runjava"></a>Run.java

O arquivo [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fornece uma interface de linha de comando que executa o código de produtor ou consumidor. Você deve fornecer as informações do host de broker Kafka como um parâmetro. Opcionalmente, é possível incluir um valor de ID de grupo, que é usado pelo processo de consumidor. Se você criar várias instâncias de consumidor usando a mesma ID de grupo, elas balancearão a carga de leitura do tópico.

## <a name="build-and-deploy-the-example"></a>Compilar e implantar o exemplo

1. Baixar e extrair os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Defina seu diretório atual como a localização do diretório `hdinsight-kafka-java-get-started\Producer-Consumer` e use o seguinte comando:

    ```cmd
    mvn clean package
    ```

    Esse comando cria um diretório chamado `target`, que contém um arquivo chamado `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Insira o seguinte comando para copiar o arquivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` para o cluster HDInsight. Quando solicitado, insira a senha do usuário do SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> Executar o exemplo

1. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Abra uma conexão SSH para o cluster inserindo o seguinte comando. Se solicitado, insira a senha para a conta de usuário SSH.

    ```cmd
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

5. Para obter os hosts do agente do Kafka e os hosts do Apache ZooKeeper, use os comandos a seguir:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

6. Crie o tópico Kafka, `myTest`, inserindo o comando a seguir:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

7. Para executar o produtor e fazer gravação de dados para o tópico, utilize o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

8. Quando produtor concluir, use o seguinte comando para a leitura do tópico:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    São exibidos os registros lidos, juntamente com uma contagem de registros.

9. Use __Ctrl + C__ para sair do consumidor.

### <a name="multiple-consumers"></a>Vários consumidores

Os consumidores do Kafka usam um grupo de consumidores ao ler os registros. Usar o mesmo grupo com vários consumidores resulta em leituras de balanceamento de carga de um tópico. Cada consumidor no grupo recebe uma parte dos registros.

O aplicativo do consumidor aceita um parâmetro que é utilizado como a ID do grupo. Por exemplo, o seguinte comando inicia um consumidor usando uma ID do grupo `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Use __Ctrl + C__ para sair do consumidor.

Para ver esse processo em ação, use o seguinte comando:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Esse comando usa `tmux` para dividir o terminal em duas colunas. Um consumidor é iniciado em cada coluna, com o mesmo valor de ID de grupo. Depois que os consumidores terminarem a leitura, observe que cada um leu apenas uma parte dos registros. Use __Ctrl + C__ duas vezes para sair do `tmux`.

O consumo por clientes no mesmo grupo é manipulado por meio das partições do tópico. Neste exemplo de código, o tópico `test` criado anteriormente tem oito partições. Se você iniciar os oito consumidores, cada consumidor lê os registros de uma única partição para o tópico.

> [!IMPORTANT]  
> Não pode haver mais instâncias de consumidores do que partições em um grupo de consumidores. Neste exemplo, um grupo de consumidores pode conter até oito consumidores, já que esse é o número de partições no tópico. Ou você pode ter vários grupos de consumidores, cada um com no máximo oito consumidores.

Os registros armazenados no Kafka são armazenados na ordem em que são recebidos em uma partição. Para garantir a entrega ordenada em registros *em uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponda ao número de partições. Para garantir a entrega ordenada em registros *no tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Confira o seguinte para obter mais informações sobre como trabalhar com o Kafka:

> [!div class="nextstepaction"]
> [Analisar logs do Apache Kafka](apache-kafka-log-analytics-operations-management.md)