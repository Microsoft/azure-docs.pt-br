---
title: Consultar Apache Hive por meio do driver JDBC - Azure HDInsight
description: Use o driver JDBC de um aplicativo Java para enviar consultas do Apache Hive ao Hadoop no HDInsight. Conecte-se de forma programática e do cliente SQL SQuirrel.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 6073000f2f14f835e2bfbd91b41619101c36b10f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866838"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Consultar o Apache Hive por meio do driver JDBC no HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Saiba como usar o driver JDBC de um aplicativo Java. Para enviar Apache Hive consultas para Apache Hadoop no Azure HDInsight. As informações neste documento demonstram como se conectar programaticamente e do cliente SQL SQuirreL.

Para obter mais informações sobre a Interface JDBC do Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* Cluster Hadoop do HDInsight. Para criar um, confira [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Verifique se o serviço HiveServer2 está em execução.
* O [Java Developer Kit (JDK) versão 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou superior.
* [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/). O SQuirreL é um aplicativo cliente JDBC.

## <a name="jdbc-connection-string"></a>Cadeia de conexão JDBC

As conexões JDBC a um cluster HDInsight no Azure são feitas pela porta 443. O tráfego é protegido usando TLS/SSL. O gateway público atrás dos quais ficam os clusters redireciona o tráfego para a porta que o HiveServer2 de fato está escutando. A cadeia de conexão a seguir mostra o formato a ser usado para o HDInsight:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

Substitua `CLUSTERNAME` pelo nome do cluster HDInsight.

Ou você pode obter a conexão por meio **da interface do usuário do Ambari > > configurações do Hive > avançado**.

:::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png" alt-text="Obter cadeia de conexão JDBC por meio de Ambari" border="true":::

### <a name="host-name-in-connection-string"></a>Nome do host na cadeia de conexão

O nome do host ' CLUSTERNAME.azurehdinsight.net ' na cadeia de conexão é o mesmo que a URL do cluster. Você pode obtê-lo por meio de portal do Azure.

### <a name="port-in-connection-string"></a>Porta na cadeia de conexão

Você só pode usar a **porta 443** para se conectar ao cluster de alguns locais fora da rede virtual do Azure. O HDInsight é um serviço gerenciado, o que significa que todas as conexões com o cluster são gerenciadas por meio de um gateway seguro. Você não pode se conectar ao HiveServer 2 diretamente nas portas 10001 ou 10000. Essas portas não são expostas ao exterior.

## <a name="authentication"></a>Autenticação

Ao estabelecer a conexão, use o nome de administrador do cluster HDInsight e a senha para autenticar. De clientes JDBC, como SQuirreL SQL, insira o nome do administrador e a senha nas configurações do cliente.

De um aplicativo Java, use o nome e a senha ao estabelecer uma conexão. Por exemplo, o código Java a seguir abre uma nova conexão:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conectar-se ao cliente do SQuirreL SQL

O SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente as consultas do Hive com o cluster HDInsight. As etapas a seguir pressupõem que você já tenha instalado o SQuirreL SQL.

1. Crie um diretório para conter determinados arquivos a serem copiados do cluster.

2. No script a seguir, substitua `sshuser` pelo nome da conta de usuário SSH para o cluster.  Substitua `CLUSTERNAME` pelo nome do cluster do HDInsight.  Em uma linha de comando, altere seu diretório de trabalho para aquele criado na etapa anterior e, em seguida, digite o seguinte comando para copiar arquivos de um cluster HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Inicie o aplicativo SQuirreL SQL. Na parte esquerda da janela, selecione **Drivers**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png" alt-text="Guia Drivers à esquerda da janela" border="true":::

4. Nos ícones na parte superior do diálogo **Drivers**, selecione o ícone **+** para criar um driver.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png" alt-text="Ícone de drivers de aplicativos do SQL SQuirreL" border="true":::

5. No diálogo Adicionar Driver, adicione as informações a seguir:

    |Propriedade | Valor |
    |---|---|
    |Nome|Hive|
    |Exemplo de URL|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Caminho de classe extra|Use o botão **Adicionar** para adicionar todos os arquivos jar baixados anteriormente.|
    |Nome da Classe|org. Apache. Hive. JDBC. HiveDriver|

   :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png" alt-text="caixa de diálogo Adicionar driver com parâmetros" border="true":::

   Selecione **OK** para salvar essas configurações.

6. À esquerda da janela do SQuirreL SQL, selecione **Aliases**. Em seguida, selecione o **+** ícone para criar um alias de conexão.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png" alt-text="Caixa de diálogo ' SQuirreL SQL Add New Alias '" border="true":::

7. Use os seguintes valores para a caixa de diálogo **Adicionar alias** :

    |Propriedade |Valor |
    |---|---|
    |Nome|Hive no HDInsight|
    |Driver|Use a lista suspensa para selecionar o driver do **Hive** .|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.|
    |Nome do Usuário|O nome da conta de logon do cluster para o cluster HDInsight. O padrão é **admin**.|
    |Senha|A senha da conta de logon do cluster.|

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png" alt-text="caixa de diálogo Adicionar alias com parâmetros" border="true":::

    > [!IMPORTANT]
    > Use o botão **Testar** para verificar se a conexão funciona. Quando o diálogo **Conectar a: Hive no HDInsight** for exibido, selecione **Conectar** para executar o teste. Se o teste tiver êxito, você verá um diálogo **Conexão bem-sucedida**. Se ocorrer um erro, consulte [Solução de problemas](#troubleshooting).

    Use o botão **Ok** na parte inferior do diálogo **Adicionar Alias** para salvar o alias de conexão.

8. Na lista suspensa **Conectar a**, na parte superior do SQuirreL SQL, selecione **Hive no HDInsight**. Quando solicitado, selecione **Conectar**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png" alt-text="caixa de diálogo de conexão com parâmetros" border="true":::

9. Uma vez conectado, insira a consulta a seguir na caixa de diálogo consulta SQL e, em seguida, selecione o ícone **executar** (uma pessoa em execução). A área de resultados deve mostrar os resultados da consulta.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png" alt-text="diálogo consulta sql, incluindo os resultados" border="true":::

## <a name="connect-from-an-example-java-application"></a>Conectar-se de um aplicativo Java de exemplo

Um exemplo de como usar um cliente Java para consultar o hive no HDInsight está disponível em [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . Siga as instruções no repositório para compilar e executar o exemplo.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ocorreu um erro inesperado ao tentar abrir uma conexão SQL

**Sintomas**: ao se conectar a um cluster HDInsight versão 3.3 ou superior, você poderá receber uma mensagem indicando que ocorreu um erro inesperado. O rastreamento de pilha para esse erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: este erro é causado por um arquivo commons-codec.jar de versão mais antiga incluído com o SQuirreL.

**Resolução**: para corrigir esse erro, use as seguintes etapas:

1. Saia do SQuirreL e vá para o diretório em que o SQuirreL está instalado em seu sistema, talvez `C:\Program Files\squirrel-sql-4.0.0\lib` . No diretório do SquirreL, sob o diretório `lib` , substitua o commons-codec.jar existente pelo baixado por meio do cluster HDInsight.

1. Reinicie o SQuirreL. O erro não deverá ocorrer mais nas próximas conexões ao Hive no HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Conexão desconectada pelo HDInsight

**Sintomas**: ao tentar baixar uma grande quantidade de dados (digamos, vários GBS) por meio de JDBC/ODBC, a conexão é desconectada pelo HDInsight inesperadamente durante o download.

**Causa**: esse erro é causado pela limitação em nós de gateway. Ao obter dados do JDBC/ODBC, todos os dados precisam passar pelo nó do gateway. No entanto, um gateway não é projetado para baixar uma grande quantidade de dados, portanto, o gateway poderá fechar a conexão se não puder lidar com o tráfego.

**Resolução**: Evite usar o driver JDBC/ODBC para baixar enormes quantidades de dados. Copie dados diretamente do armazenamento de BLOBs em vez disso.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar o JDBC para trabalhar com o Hive, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Visualize Apache Hive dados com o Microsoft Power bi no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados de hive de consulta interativa com Power bi no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](../use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
