---
title: Exemplo de topologia Java do Apache Storm – Azure HDInsight
description: Aprenda a criar topologias Apache Storm em Java criando um exemplo de topologia de contagem de palavras.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020,devx-track-java
ms.date: 04/27/2020
ms.openlocfilehash: 8e51f99496514e40d6c3f85000384a14baf84946
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865359"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Criar uma topologia Apache Storm em Java

Aprenda a criar uma topologia baseada em Java para o Apache Storm. Crie uma topologia Storm que implementa um aplicativo de contagem de palavras. Use o Apache Maven para compilar e empacotar o projeto. Em seguida, você aprende a definir a topologia usando a estrutura Apache Storm fluxo.

Depois de concluir as etapas neste documento, você pode implantar a topologia para o Apache Storm no HDInsight.

> [!NOTE]  
> Uma versão completa dos exemplos de topologia do Storm criados neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) .

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.

## <a name="test-environment"></a>Ambiente de teste

O ambiente usado para este artigo foi um computador que executa o Windows 10.  Os comandos foram executados em um prompt de comando e os vários arquivos foram editados com o bloco de notas.

Em um prompt de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Digite o seguinte comando para criar um projeto Maven chamado **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Esse comando cria um diretório chamado `WordCount` no local atual, que contém um projeto básico do Maven. O segundo comando altera o diretório de trabalho atual para `WordCount` . O terceiro comando cria um novo diretório, `resources` , que será usado posteriormente.  O diretório `WordCount` contém os seguintes itens:

* `pom.xml`: contém configurações para o projeto Maven.
* `src\main\java\com\microsoft\example`: contém o código do aplicativo.
* `src\test\java\com\microsoft\example`: contém testes para o seu aplicativo.  

### <a name="remove-the-generated-example-code"></a>Remover o exemplo de código gerado

Exclua os arquivos de teste e de aplicativo gerados `AppTest.java` e `App.java` Insira os comandos abaixo:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Adicionar repositórios Maven

Como o HDInsight tem base no HDP (Hortonworks Data Platform), recomendamos o uso do repositório Hortonworks para baixar dependências de seus projetos do Apache Storm.  

Abra `pom.xml` inserindo o comando a seguir:

```cmd
notepad pom.xml
```

Em seguida, adicione o seguinte XML após a `<url>https://maven.apache.org</url>` linha:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Adicionar propriedades

O Maven permite que você defina valores de nível de projeto chamados propriedades. No `pom.xml` , adicione o seguinte texto após a `</repositories>` linha:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Agora você pode usar esse valor em outras seções do `pom.xml`. Por exemplo, ao especificar a versão dos componentes do Storm, podemos usar `${storm.version}` em vez de fazer hard-coding de um valor.

## <a name="add-dependencies"></a>Adicionar dependências

Adicione uma dependência para componentes do Storm. No `pom.xml` , adicione o seguinte texto na `<dependencies>` seção:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

No momento da compilação, o Maven usa essas informações para pesquisar `storm-core` no repositório Maven. Ele primeiro procura no repositório em seu computador local. Se os arquivos não estiverem lá, o Maven os baixará do repositório Maven público e os armazenará no repositório local.

> [!NOTE]  
> Observe a linha `<scope>provided</scope>` nesta seção. Essa configuração informa ao Maven para excluir o **storm-core** de qualquer arquivo JAR criado, pois ele será fornecido pelo sistema.

## <a name="build-configuration"></a>Configuração de compilação

Plug-ins do Maven permitem que você personalize os estágios de compilação do projeto. Por exemplo, como o projeto é compilado ou como compactá-lo em um arquivo JAR. No `pom.xml` , adicione o texto a seguir diretamente acima da `</project>` linha.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Esta seção será usada para adicionar plug-ins, recursos e outras opções de configuração de compilação. Para obter uma referência completa do `pom.xml` arquivo, consulte [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) .

### <a name="add-plug-ins"></a>Adicionar plug-ins

* **Plug-in exec Maven**

    Para topologias Apache Storm implementadas em Java, o [plug-in Maven Exec](https://www.mojohaus.org/exec-maven-plugin/) é útil porque permite que você execute com facilidade a topologia localmente em seu ambiente de desenvolvimento. Adicione o seguinte à seção `<plugins>` do arquivo `pom.xml` para incluir o plug-in Exec Maven:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Plug-in do compilador Apache Maven**

    Outro plug-in útil é o [`Apache Maven Compiler Plugin`](https://maven.apache.org/plugins/maven-compiler-plugin/) , que é usado para alterar as opções de compilação. Altere a versão Java que o Maven usa para a origem e o destino do seu aplicativo.

  * Para HDInsight __3.4 ou anterior__, defina a versão Java de origem e de destino como __1.7__.

  * Para HDInsight __3.5__, defina a versão Java de origem e de destino como __1.8__.

  Adicione o seguinte texto à seção `<plugins>` do arquivo `pom.xml` para incluir o plug-in Compilador do Apache Maven. Este exemplo especifica a versão 1.8. Portanto, a versão do HDInsight de destino será a 3.5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Configurar recursos

A seção de recursos permite que você inclua recursos que não são código, por exemplo, arquivos de configuração necessários aos componentes na topologia. Para este exemplo, adicione o texto a seguir na `<resources>` seção do `pom.xml` arquivo. Em seguida, salve e feche o arquivo.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Esse exemplo adiciona o diretório de recursos na raiz do projeto (`${basedir}`) como um local que contém os recursos e inclui o arquivo `log4j2.xml`. Esse arquivo é usado para configurar quais informações são registradas pela topologia.

## <a name="create-the-topology"></a>Criar a topologia

Uma topologia Apache Storm baseada em Java consiste em três componentes que você deve criar (ou referenciar) como uma dependência.

* **Spouts**: lê dados de fontes externas e a emite fluxos de dados para a topologia.

* Os **parafusos**: o processamento em fluxos emitidos por esgotamentos ou outros parafusos e emite um ou mais fluxos.

* **Topologia**: define como os spouts e bolts são organizados e fornece o ponto de entrada para a topologia.

### <a name="create-the-spout"></a>Criar o spout

Para reduzir os requisitos para configurar fontes de dados externas, o seguinte spout simplesmente emite sentenças aleatórias. É uma versão modificada de um Spout que é fornecido com os [exemplos do Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Embora essa topologia use um Spout, outras pessoas podem ter vários que alimentam dados de diferentes fontes na topologia`.`

Digite o comando a seguir para criar e abrir um novo arquivo `RandomSentenceSpout.java` :

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Em seguida, copie e cole o código Java abaixo no novo arquivo.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Para obter um exemplo de um spout que lê de uma fonte de dados externa, consulte um dos exemplos a seguir:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): um exemplo de Spout que lê do Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): um Spout que lê de Kafka.

### <a name="create-the-bolts"></a>Criar os bolts

Bolts manipulam o processamento de dados. Os bolts podem fazer qualquer coisa, por exemplo, computação, persistência ou conversar com componentes externos. Essa topologia usa dois bolts:

* **SplitSentence**: divide as sentenças emitidas por **RandomSentenceSpout** em palavras individuais.

* **WordCount**: conta quantas vezes cada palavra ocorreu.

#### <a name="splitsentence"></a>SplitSentence

Digite o comando a seguir para criar e abrir um novo arquivo `SplitSentence.java` :

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Em seguida, copie e cole o código Java abaixo no novo arquivo.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Digite o comando a seguir para criar e abrir um novo arquivo `WordCount.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Em seguida, copie e cole o código Java abaixo no novo arquivo.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Definir a topologia

A topologia une os limites e os parafusos em um grafo. O grafo define como os dados fluem entre os componentes. Ele também fornece dicas de paralelismo que o Storm usará ao criar instâncias dos componentes de dentro do cluster.

A imagem a seguir é um diagrama básico do grafo de componentes para esta topologia.

:::image type="content" source="./media/apache-storm-develop-java-topology/word-count-topology1.png" alt-text="diagrama mostrando a organização de spouts e bolts" border="false":::

Para implementar a topologia, digite o comando a seguir para criar e abrir um novo arquivo `WordCountTopology.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Em seguida, copie e cole o código Java abaixo no novo arquivo.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Configurar o registro em log

O Storm usa o [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para registrar informações em log. Se você não configurar o registro em log, a topologia emitirá informações de diagnóstico. Para controlar o que é registrado em log, crie um arquivo chamado `log4j2.xml` no `resources` diretório digitando o comando a seguir:

```cmd
notepad resources\log4j2.xml
```

Em seguida, copie e cole o texto XML abaixo no novo arquivo.  Em seguida, feche o arquivo.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Esse XML configura um novo agente de log para a classe `com.microsoft.example`, que inclui os componentes nesta topologia de exemplo. O nível é definido como rastreamento para esse agente, que captura as informações de registro em log emitidas pelos componentes nessa topologia.

A seção `<Root level="error">` configura o nível raiz do registro em log (tudo que não está em `com.microsoft.example`) para registrar apenas as informações de erro.

Para obter mais informações sobre como configurar o log para Log4J 2, consulte [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) .

> [!NOTE]  
> Storm versão 0.10.0 e superior usa Log4j 2.x. Versões mais antigas do storm usavam Log4j 1.x, que usava um formato diferente para a configuração de log. Para obter informações sobre a configuração mais antiga, consulte [https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat](https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat) .

## <a name="test-the-topology-locally"></a>Testar a topologia localmente

Depois de salvar os arquivos, use o comando a seguir para testar a topologia localmente.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

À medida que for executada, a topologia exibirá informações de inicialização. O texto a seguir é um exemplo da saída de contagem de palavras:

```output
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
```

Esse log de exemplo indica que a palavra 'and' foi emitida 113 vezes. A contagem continua aumentando, desde que a topologia seja executada. Esse aumento ocorre porque o Spout emite continuamente as mesmas sentenças.

Há um intervalo de 5 segundos entre a emissão de palavras e contagens. O componente **WordCount** está configurado para emitir informações somente quando chega uma tupla de escala. Ele solicita que as tuplas de escala sejam entregues somente a cada cinco segundos.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia para Flux

O [fluxo](https://storm.apache.org/releases/2.0.0/flux.html) é uma nova estrutura disponível com o Storm 0.10.0 e superior. O fluxo permite que você separe a configuração da implementação. Os componentes ainda são definidos em Java, mas a topologia é definida usando um arquivo YAML. Você pode empacotar uma definição de topologia padrão com seu projeto, ou usar um arquivo autônomo ao enviar a topologia. Ao enviar a topologia para o Storm, use variáveis de ambiente ou arquivos de configuração para preencher os valores de definição de topologia YAML.

O arquivo YAML define os componentes a serem usados para a topologia, e o fluxo de os dados entre eles. Você pode incluir um arquivo YAML como parte do arquivo jar. Ou você pode usar um arquivo YAML externo.

Para obter mais informações sobre Flux, consulte [Estrutura do Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Devido a um [bug (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055) com o Storm 1.0.1, será necessário instalar um [ambiente de desenvolvimento Storm](https://storm.apache.org/releases/current/Setting-up-development-environment.html) para executar topologias do Flux localmente.

1. Anteriormente, `WordCountTopology.java` defini a topologia, mas não é necessária com o fluxo. Exclua o arquivo com o seguinte comando:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Digite o comando a seguir para criar e abrir um novo arquivo `topology.yaml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    Em seguida, copie e cole o texto abaixo no novo arquivo.  Em seguida, feche o arquivo.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Digite o comando a seguir para abrir `pom.xml` para fazer as revisões descritas abaixo:

    ```cmd
    notepad pom.xml
    ```

   1. Adicione a nova dependência a seguir à seção `<dependencies>` :

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Adicione o plug-in a seguir à seção `<plugins>` . Este plug-in cuida da criação de um pacote (arquivo jar) para o projeto e aplica algumas transformações específicas ao Flux durante a criação do pacote.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. Para a seção plug-in exec Maven, navegue até `<configuration>`  >  `<mainClass>` e altere `${storm.topology}` para `org.apache.storm.flux.Flux` . Essa configuração permite que o Flux cuide da execução da topologia localmente no desenvolvimento.

   1. Na `<resources>` seção, adicione o seguinte a `<includes>` . Esse XML inclui o arquivo YAML que define a topologia como parte do projeto.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testar a topologia do Flux localmente

1. Digite o seguinte comando para compilar e executar a topologia de fluxo usando o Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Se a topologia usar bits do Storm 1.0.1, esse comando falhará. Essa falha é causada pelo [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) . Em vez disso, [instale o Storm no ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html) e use as informações a seguir:
    >
    > Se você tiver [instalado o Storm no ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html), poderá usar os seguintes comandos em vez disso:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    O parâmetro `--local` executa a topologia no modo local no ambiente de desenvolvimento. O parâmetro `-R /topology.yaml` usa o recurso de arquivo `topology.yaml` do arquivo jar para definir a topologia.

    À medida que for executada, a topologia exibirá informações de inicialização. O texto a seguir é um exemplo de saída:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Há um atraso de 10 segundos entre os lotes de informações registradas.

2. Crie um novo YAML de topologia do projeto.

    1. Digite o comando a seguir para abrir `topology.xml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Localize a seção a seguir e altere o valor de `10` para `5` . Essa modificação altera o intervalo entre as emissões de lotes de contagens de palavras de 10 segundos para 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Salvar arquivo como `newtopology.yaml` .

3. Para executar a topologia, digite o seguinte comando:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Se preferir, se você tiver o Storm no ambiente de desenvolvimento:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Esse comando usa o `newtopology.yaml` como a definição de topologia. Como não incluímos o parâmetro `compile`, o Maven usará a versão do projeto criado nas etapas anteriores.

    Depois que a topologia for iniciada, você deverá observar que o tempo entre os lotes emitidos foi alterado para refletir o valor em `newtopology.yaml` . Veja então que você pode alterar sua configuração por meio de um arquivo YAML sem ter que recompilar a topologia.

Para obter mais informações sobre esses e outros recursos da estrutura do Flux, consulte [Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

O [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) é uma abstração de alto nível fornecida pelo Storm. Ele dá suporte ao processamento com monitoramento de estado. A principal vantagem do Trident é que ele garante que todas as mensagens que entram na topologia sejam processadas apenas uma vez. Sem usar o Trident, sua topologia só pode garantir que as mensagens sejam processadas pelo menos uma vez. Também existem outras diferenças, como componentes internos que podem ser usados em vez da criação de bolts. Os parafusos são substituídos por componentes menos genéricos, como filtros, projeções e funções.

Os aplicativos Trident podem ser criados usando projetos Maven. Use as mesmas etapas básicas como apresentado anteriormente neste artigo — somente o código é diferente. O Trident também não pode ser usado (atualmente) com a estrutura de fluxo.

Para obter mais informações sobre o Trident, consulte a [Visão geral da API do Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como criar uma topologia de Apache Storm usando Java. Agora saiba como:

* [Implantar e gerenciar topologias Apache Storm no HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Desenvolver topologias usando Python](apache-storm-develop-python-topology.md)

Para obter mais topologias Apache Storm, consulte [Topologias de exemplo para o Apache Storm no HDInsight](apache-storm-example-topology.md).