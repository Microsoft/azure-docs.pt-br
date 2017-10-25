---
title: "Criar MapReduce em Java para Hadoop – Azure HDInsight | Microsoft Docs"
description: Saiba como usar o Apache Maven para criar um aplicativo de MapReduce baseado em Java e, em seguida, execute-o com o Hadoop no Azure HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: larryfr
ms.openlocfilehash: 11d63f22204eb2acb530378f53ac72f16a35a4f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Desenvolver programas Java MapReduce para Hadoop no HDInsight

Saiba como usar o Apache Maven para criar um aplicativo de MapReduce baseado em Java e, em seguida, execute-o com o Hadoop no Azure HDInsight.

> [!NOTE]
> Este exemplo foi testado mais recentemente no HDInsight 3.6.

## <a name="prerequisites"></a>Pré-requisitos

* O [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 ou posterior (ou um equivalente, como OpenJDK).
    
    > [!NOTE]
    > O HDInsight nas versões 3.4 e anteriores usam Java 7. HDInsight 3.5 e posterior usa Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* `JAVA_HOME` – deve apontar para o diretório no qual o JRE (Java Runtime Environment) está instalado. Por exemplo, em um sistema OS X, Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`

* `PATH` – deve conter os seguintes caminhos:
  
  * `JAVA_HOME` (ou o caminho equivalente)

  * `JAVA_HOME\bin` (ou o caminho equivalente)

  * O diretório onde o Maven está instalado

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Em uma sessão do terminal ou linha de comando em seu ambiente de desenvolvimento, mude os diretórios para o local em que você deseja armazenar o projeto.

2. Use o comando `mvn`, que é instalado com o Maven, para gerar o scaffolding para o projeto.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Se você estiver usando o PowerShell, coloque os parâmetros `-D` entre aspas duplas.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Esse comando cria um diretório com o nome especificado pelo parâmetro `artifactID` (**wordcountjava** neste exemplo). Esse diretório contém os seguintes itens:

   * `pom.xml` – o [POM (Modelo de Objeto de Projeto)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém informações e detalhes de configuração usados para criar o projeto.

   * `src` – o diretório que contém o aplicativo.

3. Exclua arquivo o `src/test/java/org/apache/hadoop/examples/apptest.java`. Ele não é usado neste exemplo.

## <a name="add-dependencies"></a>Adicionar dependências

1. Edite o arquivo `pom.xml` e adicione o seguinte texto dentro da seção `<dependencies>`:
   
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

    Isso define as bibliotecas necessárias (listadas em &lt;artifactId\>) com uma versão específica (listada em &lt;version\>). Em tempo de compilação, essa dependência será baixada do repositório padrão do Maven. Você pode usar a [pesquisa de repositório do Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para exibir mais informações.
   
    O `<scope>provided</scope>` informa o Maven que essas dependências não devem ser empacotadas com o aplicativo, pois são fornecidas pelo cluster do HDInsight em tempo de execução.

    > [!IMPORTANT]
    > A versão usada deve corresponder à versão do Hadoop no cluster. Para obter mais informações sobre versões, consulte o documento [Versão do componente HDInsight](hdinsight-component-versioning.md).

2. Adicione o seguinte ao arquivo `pom.xml`. Esse texto deve estar dentro das marcas `<project>...</project>` no arquivo, por exemplo, entre `</dependencies>` e `</project>`.

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

    O primeiro plug-in configura o [Plug-in Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), que é usado para criar um uberjar (às vezes chamado de fatjar), que contém as dependências exigidas pelo aplicativo. Ele também evita a duplicação de licenças no pacote jar, que pode causar problemas em alguns sistemas.

    O segundo plug-in configura a versão de Java de destino.

    > [!NOTE]
    > O HDInsight 3.4 e anteriores usam o Java 7. HDInsight 3.5 e posterior usa Java 8.

3. Salve o arquivo `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Criar o aplicativo MapReduce

1. Vá para o diretório `wordcountjava/src/main/java/org/apache/hadoop/examples` e renomeie o arquivo `App.java` para `WordCount.java`.

2. Abra o arquivo `WordCount.java` em um editor de texto e substitua o conteúdo pelo seguinte texto:
   
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

3. Salve o arquivo.

## <a name="build-the-application"></a>Compilar o aplicativo

1. Altere para o diretório `wordcountjava` se ainda não estiver lá.

2. Use o comando a seguir para compilar um arquivo JAR contendo o aplicativo:

   ```
   mvn clean package
   ```

    Esse comando limpa quaisquer artefatos de criação anteriores, baixa quaisquer dependências que ainda não foram instaladas e, então, compila o aplicativo e cria seu pacote.

3. Quando o comando é concluído, o diretório `wordcountjava/target` contém um arquivo chamado `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > O arquivo `wordcountjava-1.0-SNAPSHOT.jar` é um uberjar, que contém não apenas o trabalho WordCount, mas também as dependências necessárias para o trabalho em tempo de execução.

## <a id="upload"></a>Carregar o jar

Use o comando a seguir para carregar o arquivo jar para o nó do HDInsight:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Esse comando copia os arquivos do sistema local para o nó principal. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Para executar o trabalho MapReduce no Hadoop

1. Conecte-se ao HDInsight usando o SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Na sessão do SSH, use o seguinte comando para executar o aplicativo MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Esse comando inicia o aplicativo WordCount MapReduce. O arquivo de entrada é `/example/data/gutenberg/davinci.txt`, e o diretório de saída é `/example/data/wordcountout`. Os arquivos de entrada e saída são armazenados no armazenamento padrão do cluster.

3. Quando o trabalho for concluído, use o seguinte comando para exibir os resultados:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Você deverá receber uma lista de palavras e contagens, com valores semelhantes ao seguinte texto:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Próximas etapas

Neste documento, você aprendeu a desenvolver um trabalho MapReduce em Java. Consulte os seguintes documentos para ver outras maneiras de trabalhar com o HDInsight.

* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações, consulte também o [Centro de desenvolvedores do Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

