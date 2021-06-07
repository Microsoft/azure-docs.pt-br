---
title: Criar o Java MapReduce para o Apache Hadoop - Azure HDInsight
description: Saiba como usar o Apache Maven para criar um aplicativo de MapReduce baseado em Java e, em seguida, execute-o com o Hadoop no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 7c1a6623883cbee46ba98982808f3c392dc50ffa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946643"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Desenvolva programas Java MapReduce para o Apache Hadoop no HDInsight

Aprenda a usar o Apache Maven para criar um aplicativo MapReduce baseado em Java e, em seguida, execute-o com o Apache Hadoop no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

O ambiente usado para este artigo foi um computador que executa o Windows 10. Os comandos foram executados em um prompt de comando e os vários arquivos foram editados com o bloco de notas. Modifique de acordo com o seu ambiente.

Em um prompt de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Digite o seguinte comando para criar um projeto Maven chamado **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Esse comando cria um diretório com o nome especificado pelo `artifactID` parâmetro (**wordcountjava** neste exemplo). Esse diretório contém os seguintes itens:

    * `pom.xml` – o [POM (Modelo de Objeto de Projeto)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém informações e detalhes de configuração usados para criar o projeto.
    * src\main\java\org\apache\hadoop\examples: contém o código do aplicativo.
    * src\test\java\org\apache\hadoop\examples: contém testes para seu aplicativo.

1. Remova o código de exemplo gerado. Exclua os arquivos de teste e de aplicativo gerados `AppTest.java` e `App.java` Insira os comandos abaixo:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Atualize o modelo do objeto do projeto

Para obter uma referência completa do arquivo pom.xml, consulte https://maven.apache.org/pom.html. Abra `pom.xml` inserindo o comando a seguir:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Adicionar dependências

No `pom.xml` , adicione o seguinte texto na `<dependencies>` seção:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Isso define as bibliotecas necessárias (listadas em &lt;artifactId\>) com uma versão específica (listada em &lt;version\>). Em tempo de compilação, essa dependência será baixada do repositório padrão do Maven. Você pode usar a [pesquisa de repositório do Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para exibir mais informações.

O `<scope>provided</scope>` informa o Maven que essas dependências não devem ser empacotadas com o aplicativo, pois são fornecidas pelo cluster do HDInsight em tempo de execução.

> [!IMPORTANT]
> A versão usada deve corresponder à versão do Hadoop no cluster. Para obter mais informações sobre versões, consulte o documento [Versão do componente HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Configuração de compilação

Plug-ins do Maven permitem que você personalize os estágios de compilação do projeto. Esta seção será usada para adicionar plug-ins, recursos e outras opções de configuração de compilação.

Adicione o seguinte código ao `pom.xml` arquivo e, em seguida, salve e feche o arquivo. Esse texto deve estar dentro das marcas `<project>...</project>` no arquivo, por exemplo, entre `</dependencies>` e `</project>`.

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

Esta seção configura o plug-in do compilador Apache Maven e o plug-in de sombreamento Apache Maven. O plug-in compilador é usado para compilar a topologia. O plug-in de tonalidade é usado para evitar a duplicação de licenças no pacote JAR que é criado pelo Maven. O plug-in serve para evitar o erro de arquivos de licença duplicados no momento da execução no cluster do HDInsight. Utilize o plug-in de tonalidade do Maven com a implementação `ApacheLicenseResourceTransformer` para isso.

O plug-in de tonalidade do Maven também produzirá um uber jar, que contém todas as dependências exigidas pelo aplicativo.

Salve o arquivo `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Criar o aplicativo MapReduce

1. Digite o comando a seguir para criar e abrir um novo arquivo `WordCount.java` . Selecione **Sim** no prompt para criar um novo arquivo.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Em seguida, copie e cole o código Java abaixo no novo arquivo. Em seguida, feche o arquivo.

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

    Observe que o nome do pacote é `org.apache.hadoop.examples` e o nome de classe é `WordCount`. Você usará esses nomes quando enviar o trabalho MapReduce.

## <a name="build-and-package-the-application"></a>Compilar e criar o pacote do aplicativo

Use o diretório `wordcountjava`, use o comando a seguir para compilar um arquivo JAR que contém o aplicativo:

```cmd
mvn clean package
```

Esse comando limpa quaisquer artefatos de criação anteriores, baixa quaisquer dependências que ainda não foram instaladas e, então, compila o aplicativo e cria seu pacote.

Quando o comando é concluído, o diretório `wordcountjava/target` contém um arquivo chamado `wordcountjava-1.0-SNAPSHOT.jar`.

> [!NOTE]
> O arquivo `wordcountjava-1.0-SNAPSHOT.jar` é um uberjar, que contém não apenas o trabalho WordCount, mas também as dependências necessárias para o trabalho em runtime.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Carregue o arquivo JAR e execute trabalhos (SSH)

As etapas a seguir usam `scp` para copiar o JAR para o nó principal primário de seu Apache HBase no cluster HDInsight. Então, o comando `ssh` será utilizado para se conectar ao cluster e executar o exemplo diretamente no nó principal.

1. Carregue o JAR no cluster. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight e, em seguida, digite o seguinte comando:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Conecte-se ao cluster. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight e, em seguida, digite o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Na sessão do SSH, use o seguinte comando para executar o aplicativo MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Esse comando inicia o aplicativo WordCount MapReduce. O arquivo de entrada é `/example/data/gutenberg/davinci.txt`, e o diretório de saída é `/example/data/wordcountout`. Os arquivos de entrada e saída são armazenados no armazenamento padrão do cluster.

1. Quando o trabalho for concluído, use o seguinte comando para exibir os resultados:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Você deverá receber uma lista de palavras e contagens, com valores semelhantes ao seguinte texto:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a desenvolver um trabalho MapReduce em Java. Consulte os seguintes documentos para ver outras maneiras de trabalhar com o HDInsight.

* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
* [Centro de desenvolvedores de Java](https://azure.microsoft.com/develop/java/)