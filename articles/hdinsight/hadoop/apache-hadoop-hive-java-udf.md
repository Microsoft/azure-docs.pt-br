---
title: UDF (função definida pelo usuário) Java com Apache Hive Azure HDInsight
description: Saiba como criar uma baseado em Java-função definida pelo usuário (UDF) que funciona com o Apache Hive. Este UDF de exemplo converte uma tabela de cadeias de caracteres de texto em minúsculas.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946556"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Usar um Java UDF com Apache Hive no HDInsight

Saiba como criar uma baseado em Java-função definida pelo usuário (UDF) que funciona com o Apache Hive. O UDF Java neste exemplo converte uma tabela de cadeias de caracteres de texto em caracteres minúsculos.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.
* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do seu armazenamento primário de clusters. Isso seria wasb://para o armazenamento do Azure, abfs://para Azure Data Lake Storage Gen2 ou adl://para Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o Armazenamento do Azure, o URI será `wasbs://`.  Confira também [Transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Um editor de texto ou Java IDE

    > [!IMPORTANT]  
    > Se você criar os arquivos de Python em um cliente Windows, você deverá usar um editor que usa LF como uma terminação de linha. Se você não tem certeza se o seu editor usa LF ou CRLF, veja a seção de [Solução de problemas](#troubleshooting) para ver as etapas para remover o caractere CR.

## <a name="test-environment"></a>Ambiente de teste

O ambiente usado para este artigo foi um computador que executa o Windows 10.  Os comandos foram executados em um prompt de comando e os vários arquivos foram editados com o bloco de notas. Modifique de acordo com o seu ambiente.

Em um prompt de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Criar UDF Java de exemplo

1. Crie um novo projeto Maven inserindo o seguinte comando:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Este comando cria um diretório chamado `exampleudf` , que contém o projeto Maven.

2. Depois que o projeto tiver sido criado, exclua o `exampleudf/src/test` diretório que foi criado como parte do projeto digitando o seguinte comando:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Abra `pom.xml` inserindo o comando a seguir:

    ```cmd
    notepad pom.xml
    ```

    Em seguida, substitua a `<dependencies>` entrada existente pelo seguinte XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Essas entradas especificam a versão do Hadoop e do Hive incluídas com o HDInsight 3.6. Você pode encontrar informações sobre as versões do Hadoop e do Hive fornecidas com o HDInsight no documento [Controle de versão de componente do HDInsight](../hdinsight-component-versioning.md) .

    Adicione uma seção `<build>` antes da linha `</project>` no final do arquivo. Esta seção deve conter o XML a seguir:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
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
        </plugins>
    </build>
    ```

    Essas entradas definem como compilar o projeto. Especificamente, a versão do Java que o projeto usa e como criar um uberjar para implantação no cluster.

    Salve o arquivo depois que as alterações forem feitas.

4. Digite o comando a seguir para criar e abrir um novo arquivo `ExampleUDF.java` :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Em seguida, copie e cole o código Java abaixo no novo arquivo. Em seguida, feche o arquivo.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Este código implementa um UDF que aceita um valor de cadeia de caracteres e retorna uma versão da cadeia de caracteres em minúsculas.

## <a name="build-and-install-the-udf"></a>Compilar e instalar o UDF

Nos comandos abaixo, substitua `sshuser` pelo nome de usuário real, se for diferente. Substituir `mycluster` pelo nome real do cluster.

1. Compile e empacote o UDF digitando o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Este comando cria e empacota o UDF no arquivo `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Use o `scp` comando para copiar o arquivo para o cluster HDInsight digitando o seguinte comando:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Conecte-se ao cluster usando SSH digitando o seguinte comando:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Na sessão SSH aberta, copie o arquivo JAR para o armazenamento do HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Usar o UDF do Hive

1. Inicie o cliente beeline da sessão SSH digitando o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Esse comando presume que você usou o padrão de **admin** para a conta de logon para o cluster.

2. Quando você chegar ao prompt `jdbc:hive2://localhost:10001/>` , digite o seguinte para adicionar o UDF no Hive e expô-lo como uma função.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Use o UDF para converter valores recuperados de uma tabela em cadeias de caracteres de letras minúsculas.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Essa consulta seleciona o estado da tabela, converte a cadeia de caracteres em letras minúsculas e, em seguida, exibe-as junto com o nome não modificado. A saída é semelhante ao texto a seguir:

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>Solução de problemas

Ao executar o trabalho do hive, você pode vir um erro semelhante ao seguinte texto:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Esse problema pode ser causado pelas terminações de linha no arquivo do Python. Muitos editores Windows usam CRLF como padrão como a terminação de linha, mas aplicativos Linux geralmente esperam LF.

Você pode seguir as seguintes instruções do PowerShell para remover os caracteres CR antes de carregar o arquivo no HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Próximas etapas

Para ver outras maneiras de trabalhar com o Hive, confira [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre Funções Definidas pelo Usuário do Hive, confira a seção [Operadores e funções definidas pelo usuário do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do wiki do Hive em apache.org.