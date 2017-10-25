---
title: "UDF (função definida pelo usuário) do Java com o Hive no HDInsight – Azure | Microsoft Docs"
description: "Saiba como criar um UDF (função definida pelo usuário) baseado em Java que funcione com o Hive. Este UDF de exemplo converte uma tabela de cadeias de caracteres de texto em minúsculas."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: larryfr
ms.openlocfilehash: 896874f66457ddf29379fa6585953a117587c032
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Usar um Java UDF com o Hive no HDInsight

Saiba como criar um UDF (função definida pelo usuário) baseado em Java que funcione com o Hive. O UDF Java neste exemplo converte uma tabela de cadeias de caracteres de texto em caracteres minúsculos.

## <a name="requirements"></a>Requisitos

* Um cluster HDInsight 

    > [!IMPORTANT]
    > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

    A maioria das etapas neste documento funciona tanto em clusters baseados em Windows quanto nos baseados em Linux. No entanto, as etapas usadas para carregar o UDF compilado ao cluster e executá-lo são específicas para clusters baseados em Linux. São fornecidos links para informações que podem ser usados com clusters baseados em Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 ou posterior (ou um equivalente, como OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Um editor de texto ou Java IDE

    > [!IMPORTANT]
    > Se você criar os arquivos de Python em um cliente Windows, você deverá usar um editor que usa LF como uma terminação de linha. Se você não tem certeza se o seu editor usa LF ou CRLF, veja a seção de [Solução de problemas](#troubleshooting) para ver as etapas para remover o caractere CR.

## <a name="create-an-example-java-udf"></a>Criar UDF Java de exemplo 

1. Na linha de comando, use o seguinte para criar um novo projeto Maven:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Se você estiver usando o PowerShell, deverá inserir os parâmetros entre aspas. Por exemplo: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Esse comando cria um diretório chamado **exampleudf**, que contém o projeto Maven.

2. Quando o projeto tiver sido criado, exclua o diretório **exampleudf/src/teste** que foi criado como parte do projeto.

3. Abra o **exampleudf/pom.xml** e substitua a entrada `<dependencies>` existente pelo seguinte XML:

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

    Essas entradas especificam a versão do Hadoop e do Hive incluídas com o HDInsight 3.5. Você pode encontrar informações sobre as versões do Hadoop e do Hive fornecidas com o HDInsight no documento [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md) .

    Adicione uma seção `<build>` antes da linha `</project>` no final do arquivo. Esta seção deve conter o XML a seguir:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.5  -->
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
                <version>2.3</version>
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

4. Renomeie **exampleudf/src/main/java/com/microsoft/examples/App.java** para **ExampleUDF.java** e abra o arquivo no editor.

5. Substitua o conteúdo do arquivo **ExampleUDF.java** pelo seguinte e, em seguida, salve o arquivo.

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

1. Use o seguinte comando para compilar e empacotar o UDF:

    ```bash
    mvn compile package
    ```

    Este comando cria e empacota o UDF no arquivo `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Use o comando `scp` para copiar o arquivo para o cluster do HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Substitua `myuser` pela conta de usuário SSH para o cluster. Substitua `mycluster` pelo nome do cluster. Se você tiver usado uma senha para proteger a conta SSH, você precisará digitá-la. Se você tiver usado um certificado, talvez precise usar o parâmetro `-i` para especificar o arquivo da chave privada.

3. Conecte-se ao cluster usando o SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

4. Na sessão de SSH, copie o arquivo jar para o armazenamento do HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Usar o UDF do Hive

1. Use o seguinte para iniciar o cliente Beeline na sessão de SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Esse comando presume que você usou o padrão de **admin** para a conta de logon para o cluster.

2. Quando você chegar ao prompt `jdbc:hive2://localhost:10001/>` , digite o seguinte para adicionar o UDF no Hive e expô-lo como uma função.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Este exemplo assume que o Armazenamento do Azure é o armazenamento padrão do cluster. Se o cluster usar o Data Lake Store em vez disso, altere o valor `wasb:///` para `adl:///`.

3. Use o UDF para converter valores recuperados de uma tabela em cadeias de caracteres de letras minúsculas.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Essa consulta seleciona a plataforma do dispositivo (Android, Windows, iOS etc.) na tabela, converte a cadeia de caracteres em letras minúsculas e as exibe. A saída é semelhante ao texto a seguir:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Próximas etapas

Para ver outras maneiras de trabalhar com o Hive, consulte [Usar o Hive com o HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre funções definidas pelo usuário do Hive, consulte a seção [Operadores e funções definidas pelo usuário do Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do wiki Hive em apache.org.
