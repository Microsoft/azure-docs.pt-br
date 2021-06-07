---
title: Usar Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943206"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usar o cliente Apache Beeline com Apache Hive

Este artigo descreve como usar o cliente [Apache beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) de linha de comando para criar e executar Apache Hive consultas em uma conexão SSH.

## <a name="background"></a>Tela de fundo

O Beeline é um cliente Hive que está incluído em nós principais do cluster HDInsight. Para se conectar ao cliente do beeline instalado em seu cluster HDInsight ou instalar o beeline localmente, consulte [conectar-se ou instalar o Apache beeline](connect-install-beeline.md). O Beeline usa o JDBC para se conectar ao HiveServer2, um serviço hospedado em seu cluster HDInsight. Você também pode usar o Beeline para acessar remotamente o Hive no HDInsight pela internet. Os exemplos a seguir fornecem as cadeias de conexão mais comuns usadas para se conectar ao HDInsight do beeline.

## <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

* Um cluster Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Observe o esquema de URI para o armazenamento primário do seu cluster. Por exemplo,  `wasb://` para o armazenamento do Azure, `abfs://` por Azure data Lake Storage Gen2 ou `adl://` para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure, o URI será `wasbs://` . Para obter mais informações, consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria das etapas neste documento pressupõe que você esteja usando o beeline de uma sessão SSH para o cluster. Você também pode usar um cliente beeline local, mas essas etapas não são abordadas neste artigo.

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

Este exemplo se baseia no uso do cliente beeline de uma conexão SSH.

1. Abra uma conexão SSH para o cluster com o código a seguir. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, insira a senha para a conta de usuário SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Conecte-se ao HiveServer2 com o cliente do beeline de sua sessão SSH aberta inserindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos Beeline normalmente começam com um caractere `!`, por exemplo, `!help` exibe a ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há `!sql` , que é usado para executar instruções HiveQL. No entanto, o HiveQL é tão usado que é possível omitir o `!sql`anterior. As duas instruções a seguir são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Em um novo cluster, somente uma tabela é listada: **hivesampletable**.

4. Use o comando a seguir para exibir o esquema para a hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Esse comando retorna as informações a seguir:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Essas informações descrevem as colunas na tabela.

5. Insira as instruções a seguir para criar uma tabela chamada **log4jLogs** usando os dados de exemplo fornecidos com o cluster HDInsight: (revisar conforme necessário com base no seu esquema de URI).

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Essas instruções realizam as seguintes ações:

    |Instrução |Descrição |
    |---|---|
    |DROP TABLE|Se a tabela existir, ela será excluída.|
    |CREATE EXTERNAL TABLE|Cria uma tabela **externa** no hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local original.|
    |FORMATO DE LINHA|Como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.|
    |ARMAZENADO COMO LOCAL DO TEXTFILE|Onde os dados são armazenados e em qual formato de arquivo.|
    |SELECT|Seleciona uma contagem de todas as linhas em que a coluna **T4** contém o valor **[ERROR]**. Essa consulta deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.|
    |INPUT__FILE__NAME como '%. log '|O hive tenta aplicar o esquema a todos os arquivos no diretório. Nesse caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados incorretos nos resultados, essa instrução informa ao Hive que deve retornar apenas dados de arquivos que terminam em .log.|

   > [!NOTE]  
   > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
   >
   > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

    A saída desse comando é semelhante ao texto a seguir:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Sair do beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Executar um arquivo HiveQL

Este exemplo é uma continuação do exemplo anterior. Use as etapas a seguir para criar um arquivo e  executá-lo usando o Beeline.

1. Use o comando a seguir para criar um novo arquivo chamado **query.hql**:

    ```bash
    nano query.hql
    ```

1. Use o texto a seguir como conteúdo do arquivo. Essa consulta cria uma nova tabela 'interna' chamada **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções realizam as seguintes ações:

    |Instrução |Descrição |
    |---|---|
    |CREATE TABLE SE NÃO EXISTIR|Se a tabela ainda não existir, ela será criada. Como a palavra-chave **external** não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e totalmente gerenciadas por ele.|
    |ARMAZENADO COMO ORC|Armazena os dados no formato OCR (Optimized Row Columnar). O formato ORC é altamente otimizado e eficiente para o armazenamento de dados do Hive.|
    |INSERIR SUBSTITUIÇÃO... Não|Seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.|

    > [!NOTE]  
    > Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

1. Para salvar o arquivo, use **Ctrl** + **X**, em seguida, digite **Y** e, por fim, **Enter**.

1. Use o seguinte para executar o arquivo usando Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O parâmetro `-i` inicia o Beeline e executa as instruções no arquivo `query.hql`. Quando a consulta for concluída, você verá um prompt `jdbc:hive2://headnodehost:10001/>`. Você também pode executar um arquivo usando o parâmetro `-f`, que fechará o Beeline após a conclusão da consulta.

1. Para verificar se a tabela **errorLogs** foi criada, use a seguinte instrução para retornar todas as linhas de **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações gerais sobre o hive no HDInsight, consulte [usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

* Para obter mais informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight, consulte [usar o MapReduce com o Apache Hadoop no hdinsight](hdinsight-use-mapreduce.md)
