---
title: 'Tutorial: As APIs de produtor e consumidor do Apache Kafka – Azure HDInsight'
description: Saiba como utilizar as APIs de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Neste tutorial, você aprenderá como usar essas APIs com Kafka no HDInsight de um aplicativo Java.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: e5a635a8837aadaf423c6f3a0925dbac4080e60f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945174"
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

* Apache Kafka no cluster do HDInsight. Para saber como criar o cluster, confira [Primeiros passos com o Apache Kafka no HDInsight](apache-kafka-get-started.md).
* [JDK (Java Developer Kit) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) ou um equivalente, como o OpenJDK.
* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.
* Um cliente SSH como Putty. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

O aplicativo de exemplo está localizado em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), além do subdiretório `Producer-Consumer`. Se você estiver usando um cluster Kafka habilitado para **ESP (Enterprise Security Package)** , deverá usar a versão do aplicativo localizada no subdiretório `DomainJoined-Producer-Consumer`.

O aplicativo consiste principalmente em quatro arquivos:
* `pom.xml`: este arquivo define as dependências do projeto, versão do Java e os métodos de empacotamento.
* `Producer.java`: este arquivo envia sentenças aleatórias para o Kafka usando a API de produtor.
* `Consumer.java`: este arquivo usa a API de consumidor para ler dados do Kafka e emiti-los para STDOUT.
* `AdminClientWrapper.java`: Esse arquivo usa a API de administração para criar, descrever e excluir tópicos do Kafka.
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

O produtor se comunica com os hosts de agente de Kafka (nós de trabalho) e envia dados a um tópico Kafka. O seguinte snippet de código é do arquivo [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) do [repositório GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra como definir as propriedades de produtor. Para clusters habilitados para o Enterprise Security, mais uma propriedade deve ser adicionada "properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

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

O cliente se comunica com os hosts de broker Kafka (nós de trabalho) e lê registros em um loop. O seguinte snippet de código do arquivo [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) define as propriedades do consumidor. Para clusters habilitados para o Enterprise Security, mais uma propriedade deve ser adicionada "properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

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

### <a name="use-pre-built-jar-files"></a>Usar arquivos JAR pré-criados

Baixe os jars do [Exemplo de introdução do Kafka para o Azure](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). Se o cluster for habilitado para **ESP (Enterprise Security Package)** , use kafka-producer-consumer-esp.jar. Use o comando abaixo para copiar os jars para seu cluster.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Compilar os arquivos JAR do código

Se você quiser ignorar esta etapa, será possível baixar os jars pré-criados do subdiretório `Prebuilt-Jars`. Baixe kafka-producer-consumer.jar. Se o cluster for habilitado para **ESP (Enterprise Security Package)** , use kafka-producer-consumer-esp.jar. Execute a etapa 3 para copiar o jar em seu cluster HDInsight.

1. Baixar e extrair os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Defina seu diretório atual como a loca.lização do diretório `hdinsight-kafka-java-get-started\Producer-Consumer`. Se você estiver usando um cluster Kafka habilitado para **ESP (Enterprise Security Package)** , deverá definir a localização como o subdiretório `DomainJoined-Producer-Consumer`. Use o comando a seguir para compilar o aplicativo:

    ```cmd
    mvn clean package
    ```

    Esse comando cria um diretório chamado `target`, que contém um arquivo chamado `kafka-producer-consumer-1.0-SNAPSHOT.jar`. Para clusters ESP, o arquivo será `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Insira o seguinte comando para copiar o arquivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` para o cluster HDInsight. Quando solicitado, insira a senha do usuário do SSH.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Executar o exemplo

1. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Abra uma conexão SSH para o cluster inserindo o seguinte comando. Se solicitado, insira a senha para a conta de usuário SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para obter os hosts do agente do Kafka, substitua os valores de `<clustername>` e `<password>` no comando a seguir e execute-o. Use a mesma capitalização para `<clustername>`, conforme mostrado na portal do Azure. Substitua `<password>` pela senha de logon do cluster e execute:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso do Ambari. Se o cluster estiver atrás de um NSG, execute esse comando em um computador que possa acessar o Ambari.

1. Crie o tópico Kafka, `myTest`, inserindo o comando a seguir:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Para executar o produtor e fazer gravação de dados para o tópico, utilize o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Quando produtor concluir, use o seguinte comando para a leitura do tópico:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    São exibidos os registros lidos, juntamente com uma contagem de registros.

1. Use __Ctrl + C__ para sair do consumidor.

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

## <a name="common-issues-faced"></a>Problemas comuns

1. **Falha na criação do tópico** Se o cluster estiver com o Pacote de Segurança Enterprise habilitado, use os [arquivos JAR pré-criados para produtor e consumidor](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar). O jar do ESP pode ser criado com base no código no [subdiretório `DomainJoined-Producer-Consumer`](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer). As propriedades do produtor e do consumidor têm uma propriedade `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` adicional para os clusters habilitados para ESP.

2. **Falha em clusters habilitados para ESP**: se as operações de produção e consumo falharem e você estiver usando um cluster habilitado para ESP, verifique se o usuário `kafka` está presente em todas as políticas do Ranger. Se não estiver presente, adicione-o a todas as políticas do Ranger.

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados por este tutorial, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Confira o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Usar o proxy REST do Kafka](rest-proxy.md)
* [Analisar logs do Apache Kafka](apache-kafka-log-analytics-operations-management.md)