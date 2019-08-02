---
title: 'Tutorial: Usar o Apache Storm para ler e gravar dados com o Apache Kafka – Azure HDInsight'
description: Saiba como criar um pipeline de streaming usando o Apache Storm e o Apache Kafka no HDInsight. Neste tutorial, você usará os componentes KafkaBolt e KafkaSpout para transmitir dados do Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 53a46c6137c3336c405eb62dc7b254ac26c13d9e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406004"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Tutorial: Usar o Apache Storm com o Apache Kafka no HDInsight

Este tutorial demonstra como usar uma topologia do [Apache Storm](https://storm.apache.org/) para ler e gravar dados com o [Apache Kafka](https://kafka.apache.org/) no HDInsight. Este tutorial também demonstra como manter os dados no armazenamento compatível com [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) no cluster Storm.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Storm e Kafka
> * Compreender o código
> * Criar clusters Kafka e Storm
> * Compilar a topologia
> * Configurar a topologia
> * Criar o tópico Kafka
> * Iniciar as topologias
> * Interromper as topologias
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com a criação de tópicos Kafka. Para obter mais informações, consulte o documento [Início rápido do Kafka no HDInsight](./kafka/apache-kafka-get-started.md).

* Familiaridade com a criação e a implantação de soluções Storm (topologias). Especificamente, topologias que usam a estrutura [Apache Storm Flux](https://storm.apache.org/releases/current/flux.html). Para obter mais informações, confira o documento [Criar uma topologia do Apache Storm em Java](./storm/apache-storm-develop-java-topology.md).

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) ou superior. HDInsight 3.5 ou superior exige Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Um cliente SSH (você precisa dos comandos `ssh` e `scp`) — para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* `JAVA_HOME` – deve apontar para o diretório em que o JDK está instalado.
* `PATH` – deve conter os seguintes caminhos:
  
    * `JAVA_HOME` (ou o caminho equivalente).
    * `JAVA_HOME\bin` (ou o caminho equivalente).
    * O diretório em que o Maven está instalado.

> [!IMPORTANT]  
> As etapas neste documento exigem um grupo de recursos do Azure que contém um Storm no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Storm se comunique diretamente com o cluster Kafka.
> 
> Para sua conveniência, este documento direciona para um modelo que pode criar todos os recursos necessários do Azure. 
>
> Para obter mais informações sobre como usar o HDInsight em uma rede virtual, confira o documento [Planejar uma rede virtual para o HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="storm-and-kafka"></a>Storm e Kafka

O Apache Storm fornece vários componentes para trabalhar com o Apache Kafka. Os componentes a seguir são usados neste tutorial:

* `org.apache.storm.kafka.KafkaSpout`: esse componente lê dados do Kafka. Este componente conta com os seguintes componentes:

    * `org.apache.storm.kafka.SpoutConfig`: fornece a configuração do componente spout.

    * `org.apache.storm.spout.SchemeAsMultiScheme` e `org.apache.storm.kafka.StringScheme`: como os dados do Kafka são transformados em uma tupla do Storm.

* `org.apache.storm.kafka.bolt.KafkaBolt`: Este componente grava dados no Kafka. Este componente conta com os seguintes componentes:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: descreve o tópico ao qual é gravado.

    * `org.apache.kafka.common.serialization.StringSerializer`: configura o bolt para serializar os dados como valor de cadeia de caracteres.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: mapeia da estrutura de dados de tupla usada dentro da topologia do Storm para os campos armazenados no Kafka.

Esses componentes estão disponíveis no pacote `org.apache.storm : storm-kafka`. Use a versão do pacote que corresponde à versão do Storm. Para HDInsight 3.6, a versão do Storm é a 1.1.0.
Também é necessário o pacote `org.apache.kafka : kafka_2.10`, que contém componentes adicionais do Kafka. Use a versão do pacote que corresponda à versão do Kafka. Para HDInsight 3.6, a versão do Kafka é a 1.1.1.

O XML a seguir é a declaração de dependência no `pom.xml` para um projeto do [Apache Maven](https://maven.apache.org/):

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Compreender o código

O código usado neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Existem duas topologias fornecidas com esse tutorial:

* Gravador de Kafka: gera sentenças aleatórias e as armazena no Kafka.

* Leitor de Kafka: lê dados do Kafka e, em seguida, os armazena no repositório de arquivos compatível com HDFS para o cluster Storm.

    > [!WARNING]  
    > Para habilitar o Storm para funcionar com o armazenamento compatível com HDFS usado pelo HDInsight, é necessária uma ação de script. O script instala vários arquivos jar no caminho `extlib` para o Storm. O modelo neste tutorial usa o script automaticamente durante a criação do cluster.
    >
    > Se você não usar o modelo deste documento para criar o cluster Storm, deverá aplicar manualmente a ação de script ao seu cluster.
    >
    > A ação de script está localizada em [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) e é aplicada aos nós nimbus e de supervisor do cluster Storm. Para saber mais sobre o uso de ações de script, consulte o documento [Personalizar o HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

As topologias são definidas usando o [Flux](https://storm.apache.org/releases/1.1.2/flux.html). O Flux foi introduzido no Storm 0.10.x e permite que você separe a configuração de topologia do código. Para topologias que usam a estrutura Flux, a topologia é definida em um arquivo YAML. O arquivo do YAML pode ser incluído como parte da topologia. Ele também pode ser um arquivo autônomo usado ao enviar a topologia. O Flux também dá suporte à substituição de variáveis em tempo de execução, o que é usado neste exemplo.

Os seguintes parâmetros são definidos em tempo de execução para essas topologias:

* `${kafka.topic}`: o nome do tópico do Kafka na qual as topologias leem/gravam.

* `${kafka.broker.hosts}`: os hosts no qual os agentes do Kafka são executados. As informações de agente são usadas pelo KafkaBolt ao gravar no Kafka.

* `${kafka.zookeeper.hosts}`: os hosts nos quais o ZooKeeper é executado no cluster Kafka.

* `${hdfs.url}`: a URL do sistema de arquivos para o componente HDFSBolt. Indica se os dados são gravados em uma conta de Armazenamento do Azure ou no Azure Data Lake Storage.

* `${hdfs.write.dir}`: o diretório no qual os dados são gravados.

Para obter mais informações sobre topologias de Flux, consulte [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

### <a name="kafka-writer"></a>Gravador de Kafka

Na topologia do gravador de Kafka, o componente de bolt do Kafka usa dois valores de cadeia de caracteres como parâmetros. Esses parâmetros indicam quais campos de tupla o bolt envia para o Kafka como valores de __chave__ e __mensagem__. A chave é usada para particionar dados no Kafka. A mensagem são os dados sendo armazenados.

Neste exemplo, o componente `com.microsoft.example.SentenceSpout` emite uma tupla que contém dois campos, `key` e `message`. O bolt do Kafka extrai esses campos e envia os dados contidos neles para o Kafka.

Os campos não precisam usar os nomes `key` e `message`. Esses nomes são usados neste projeto para facilitar a compreensão do mapeamento.

O YAML a seguir é a definição para o componente gravador de Kafka:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Leitor de Kafka

Na topologia do leitor de Kafka, o componente spout lê dados do Kafka como valores de cadeia de caracteres. Os dados são gravados no log do Storm pelo componente de registro em log e no sistema de arquivos compatível com HDFS para o cluster Storm pelo componente de bolt do HDFS.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Substituições de propriedade

O projeto contém um arquivo chamado `dev.properties` que é usado para passar parâmetros usados pelas topologias. Ele define as seguintes propriedades:

| Arquivo dev.properties | DESCRIÇÃO |
| --- | --- |
| `kafka.zookeeper.hosts` | Os hosts do [Apache Zookeeper](https://zookeeper.apache.org/) para o cluster do Kafka. |
| `kafka.broker.hosts` | Os hosts do agente do Kafka (nós de trabalho). |
| `kafka.topic` | O tópico Kafka usado pelas topologias. |
| `hdfs.write.dir` | O diretório no qual topologia do leitor de Kafka grava. |
| `hdfs.url` | O sistema de arquivos usado pelo cluster Storm. Para contas de Armazenamento do Azure, use um valor de `wasb:///`. Para o Azure Data Lake Storage Gen2, use um valor igual a `abfs:///`. Para o Azure Data Lake Storage Gen1, use um valor igual a `adl:///`. |

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer coisa que usa Kafka deve estar na mesma rede virtual do Azure. Neste tutorial, os clusters de Storm e Kafka estão localizados na mesma rede virtual do Azure. 

O diagrama a seguir mostra como a comunicação flui entre o Storm e o Kafka:

![Diagrama de clusters Storm e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]  
> Outros serviços no cluster, como SSH e [Apache Ambari](https://ambari.apache.org/), podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para criar uma Rede Virtual do Azure e, em seguida, criar os clusters Kafka e Storm dentro dela, use as seguintes etapas:

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo do Azure Resource Manager está localizado em **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** . Ele cria os seguintes recursos:
    
    * Grupo de recursos do Azure
    * Rede Virtual do Azure
    * Conta de Armazenamento do Azure
    * Kafka no HDInsight versão 3.6 (três nós de trabalho)
    * Storm no HDInsight versão 3.6 (três nós de trabalho)

   > [!WARNING]  
   > Para garantir a disponibilidade do Kafka no HDInsight, o cluster deve conter pelo menos três nós de trabalho. Este modelo cria um cluster de Kafka que contém três nós de trabalho.

2. Use as seguintes diretrizes para preencher as entradas na seção **Implantação personalizada**:

   1. Use as informações a seguir para preencher as entradas na seção **Modelo personalizado**:

      | Configuração | Valor |
      | --- | --- |
      | Assinatura | Sua assinatura do Azure |
      | Grupo de recursos | O grupo de recursos que contém os recursos. |
      | Local padrão | A região do Azure em que os recursos são criados. |
      | Nome do cluster do Kafka | O nome do cluster do Kafka. |
      | Nome do Cluster Storm | O nome do cluster Storm. |
      | Nome de usuário de logon do cluster | O nome de usuário administrador para os clusters. |
      | Senha de logon do cluster | A senha de usuário administrador para os clusters. |
      | Nome de usuário do SSH | O usuário do SSH para criar os clusters. |
      | Senha SSH | A senha para o usuário do SSH. |
   
      ![Imagem dos parâmetros de modelo](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

4. Por fim, marque **Fixar no painel** e selecione **Comprar**.

> [!NOTE]  
> A criação de clusters pode levar até 20 minutos.

## <a name="build-the-topology"></a>Compilar a topologia

1. No ambiente de desenvolvimento, baixe o projeto a partir de [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), abra uma linha de comando e altere os diretórios para o local que você baixou o projeto.

2. No diretório **hdinsight-storm-java-kafka**, use o seguinte comando para compilar o projeto e criar um pacote para implantação:

   ```bash
   mvn clean package
   ```

    O processo de pacote cria um arquivo chamado `KafkaTopology-1.0-SNAPSHOT.jar` no `target` diretório.

3. Use os seguintes comandos para copiar o pacote para o cluster Storm no HDInsight. Substitua `sshuser` pelo nome de usuário SSH para o cluster. Substitua `stormclustername` pelo nome do cluster __Storm__.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    Quando solicitado, digite a senha usada durante a criação dos clusters.

## <a name="configure-the-topology"></a>Configurar a topologia

1. Use um dos seguintes métodos para descobrir os hosts do agente do Kafka para o **Kafka** no cluster do HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > O exemplo de Bash a seguir supõe que `$CLUSTERNAME` contenha o nome do cluster __Kafka__. Ele também pressupõe que [jq](https://stedolan.github.io/jq/) versão 1.5 ou posterior está instalado. Quando solicitado, insira a senha para a conta de logon do cluster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    O valor retornado é semelhante ao seguinte texto:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]  
    > Embora possa haver mais de dois hosts de agente para seu cluster, não será preciso fornecer uma lista completa de todos os hosts aos clientes. Um ou dois são suficientes.

2. Use um dos seguintes métodos para descobrir os hosts do Zookeeper para o __Kafka__ no cluster do HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > O exemplo de Bash a seguir supõe que `$CLUSTERNAME` contenha o nome do cluster __Kafka__. Ele também pressupõe que [jq](https://stedolan.github.io/jq/) esteja instalado. Quando solicitado, insira a senha para a conta de logon do cluster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    O valor retornado é semelhante ao seguinte texto:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]  
    > Embora haja mais de dois nós Zookeeper, você não precisa fornecer uma lista completa de todos os hosts aos clientes. Um ou dois são suficientes.

    Salve esse valor, pois ele é usado mais tarde.

3. Edite o arquivo `dev.properties` na raiz do projeto. Adicione as informações de hosts do Agente e do Zookeeper às linhas correspondentes do cluster __Kafka__ nesse arquivo. O seguinte exemplo é configurado usando os valores de exemplo das etapas anteriores:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]  
    > A entrada `hdfs.url` é configurada para um cluster que usa uma conta do Armazenamento do Azure. Para usar essa topologia com um cluster Storm que usa o Data Lake Storage, altere esse valor de `wasb` para `adl`.

4. Salve o arquivo `dev.properties` e, em seguida, use o seguinte comando para carregá-lo no cluster **Storm**:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Substitua **NOMEDOUSUÁRIO** pelo nome de usuário SSH do cluster. Substitua **BASENAME** com o nome base que você usou ao criar o cluster.

## <a name="create-the-kafka-topic"></a>Criar o tópico Kafka

O Kafka armazena dados em um _tópico_. Você deve criar o tópico antes de iniciar as topologias do Storm. Para criar a topologia, realize as seguintes etapas:

1. Conecte-se ao cluster do __Kafka__ por meio de SSH usando o comando a seguir. Substitua `sshuser` pelo nome de usuário do SSH usado ao criar o cluster. Substitua `kafkaclustername` pelo nome do cluster do Kafka:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Quando solicitado, digite a senha usada durante a criação dos clusters.
   
    Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para criar o tópico Kafka, use o seguinte comando. Substitua `$KAFKAZKHOSTS` pelas informações do host do Zookeeper usadas ao configurar a topologia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Esse comando conecta-se ao Zookeeper para o cluster Kafka e cria um novo tópico denominado `stormtopic`. Esse tópico é usado pelas topologias do Storm.

## <a name="start-the-writer"></a>Iniciar o gravador

1. Use o seguinte para se conectar ao cluster **Storm** usando o SSH. Substitua `sshuser` pelo nome de usuário do SSH usado ao criar o cluster. Substitua `stormclustername` pelo nome do cluster Storm:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Quando solicitado, digite a senha usada durante a criação dos clusters.
   
    Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Na conexão SSH ao cluster Storm, use o seguinte comando para iniciar a topologia de gravador:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Os parâmetros usados com esse comando são:

    * `org.apache.storm.flux.Flux`: use o Flux para configurar e executar essa topologia.

    * `--remote`: envie a topologia para o Nimbus. A topologia é distribuída entre os nós de trabalho no cluster.

    * `-R /writer.yaml`: use o arquivo `writer.yaml` para configurar a topologia. `-R` indica que esse recurso está incluído no arquivo jar. Está na raiz do jar, então `/writer.yaml` é o caminho para ele.

    * `--filter`: popule entradas na topologia `writer.yaml` usando os valores do arquivo `dev.properties`. Por exemplo, o valor da entrada `kafka.topic` no arquivo é usada para substituir a entrada `${kafka.topic}` na definição de topologia.

## <a name="start-the-reader"></a>Iniciar o leitor

1. Na sessão SSH ao cluster Storm, use o seguinte comando para iniciar a topologia de leitor:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Aguarde um minuto e, em seguida, use o seguinte comando para exibir os arquivos criados pela topologia do leitor:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    A saída é semelhante ao texto a seguir:

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. Para exibir o conteúdo do arquivo, use o seguinte comando. Substitua `filename.txt` pelo nome de um arquivo:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    O texto a seguir é um exemplo do conteúdo do arquivo:

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Interromper as topologias

Em uma sessão SSH ao cluster Storm, use os seguintes comandos para interromper as topologias Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma topologia do [Apache Storm](https://storm.apache.org/) para gravar e ler do [Apache Kafka](https://kafka.apache.org/) no HDInsight. Você também aprendeu a armazenar dados no armazenamento compatível com [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) usado pelo HDInsight.

> [!div class="nextstepaction"]
> [Usar a API de Produtor e Consumidor do Apache Kafka](kafka/apache-kafka-producer-consumer-api.md)