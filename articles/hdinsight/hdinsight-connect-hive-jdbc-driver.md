---
title: "Consultar o Hive por meio do driver JDBC – Azure HDInsight | Microsoft Docs"
description: "Use o driver JDBC de um aplicativo Java para enviar consultas do Hive ao Hadoop no HDInsight. Conecte-se de forma programática e do cliente SQL SQuirrel."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: c57684133ac7e5dfd2cc273db0e1ceca78679178
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="query-hive-through-the-jdbc-driver-in-hdinsight"></a>Consultar o Hive por meio do driver JDBC no HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Saiba como usar o driver JDBC de um aplicativo Java para enviar consultas do Hive para Hadoop no Azure HDInsight. As informações neste documento mostram como se conectar programaticamente e do cliente SQL SQuirrel.

Para obter mais informações sobre a Interface JDBC do Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* Um Hadoop no cluster do HDInsight. Tanto clusters baseados em Linux quanto em Windows funcionarão.

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [HDInsight 3.3 retirement](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight 3.3).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). O SQuirreL é um aplicativo cliente JDBC.

* O [Java Developer Kit (JDK) versão 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior.

* [Apache Maven](https://maven.apache.org). Maven é um sistema de criação de projeto para projetos Java que é usado pelo projeto associado a este artigo.

## <a name="jdbc-connection-string"></a>Cadeia de conexão JDBC

Conexões JDBC para um cluster HDInsight no Azure são feitas em 443, e o tráfego é protegido usando SSL. O gateway público atrás dos quais ficam os clusters redireciona o tráfego para a porta que o HiveServer2 de fato está escutando. A cadeia de conexão a seguir mostra o formato a ser usado para o HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Substitua `CLUSTERNAME` pelo nome do cluster HDInsight.

## <a name="authentication"></a>Autenticação

Ao estabelecer a conexão, você deverá usar o nome e a senha do administrador do cluster HDInsight para se autenticar no gateway do cluster. Durante a conexão de clientes JDBC como o SQuirreL SQL, você deve inserir o nome e a senha do administrador nas configurações do cliente.

De um aplicativo Java, use o nome e a senha ao estabelecer uma conexão. Por exemplo, o código Java a seguir abre uma nova conexão usando a cadeia de conexão, o nome do administrador e a senha:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conectar-se ao cliente do SQuirreL SQL

O SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente as consultas do Hive com o cluster HDInsight. As etapas a seguir pressupõem que você já tenha instalado o SQuirreL SQL.

1. Copie os drivers JDBC do Hive do seu cluster HDInsight.

    * Para um cluster **HDInsight baseado em Linux** versão 3.5 ou 3.6, use as etapas a seguir para baixar os arquivos jar necessários.

        1. Crie um diretório que contenha os arquivos. Por exemplo: `mkdir hivedriver`.

        2. Em uma linha de comando, use os seguintes comandos para copiar os arquivos do cluster do HDInsight:

            ```bash
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libfb*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
            ```

            Substitua `USERNAME` pelo nome da conta de usuário SSH para o cluster. Substitua `CLUSTERNAME` pelo nome do cluster do HDInsight.

    * Para o **HDInsight baseado no Windows**, use as etapas a seguir para baixar os arquivos jar necessários.

        1. No portal do Azure, selecione o cluster HDInsight e selecione o ícone da **Área de Trabalho Remota**.

            ![Ícone da Área de Trabalho Remota](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. Na seção Área de Trabalho Remota, use o botão **Conectar** para se conectar ao cluster. Se a Área de Trabalho Remota não estiver habilitada, use o formulário para fornecer um nome de usuário e uma senha e selecione **Habilitar** para habilitar a Área de Trabalho Remota para o cluster.

            ![Seção Área de Trabalho Remota](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            Depois de selecionar **Conectar**, um arquivo .rdp será baixado. Use esse arquivo para iniciar o cliente da Área de Trabalho Remota. Quando solicitado, use o nome de usuário e a senha inseridos para o acesso à Área de Trabalho Remota.

        3. Uma vez conectado, copie os seguintes arquivos da sessão da Área de Trabalho Remota para o seu computador local. Coloque-os em um diretório local chamado `hivedriver`.

            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [!NOTE]
            > Os números da versão incluídos nos caminhos e os nomes de arquivo podem ser diferentes para o seu cluster.

        4. Desconecte a sessão da Área de Trabalho Remota quando terminar de copiar os arquivos.

2. Inicie o aplicativo SQuirreL SQL. Na parte esquerda da janela, selecione **Drivers**.

    ![Guia Drivers à esquerda da janela](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

3. Nos ícones na parte superior do diálogo **Drivers**, selecione o ícone **+** para criar um driver.

    ![Ícones de Drivers](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

4. No diálogo Adicionar Driver, adicione as informações a seguir:

    * **Nome**: Hive
    * **Exemplo de URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Caminho de Classe Adicional**: use o botão Adicionar para adicionar os arquivos jar baixados anteriormente
    * **Nome de Classe**: org.apache.hive.jdbc.HiveDriver

   ![diálogo adicionar driver](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

   Clique em **OK** para salvar as configurações.

5. À esquerda da janela do SQuirreL SQL, selecione **Aliases**. Em seguida, clique no ícone **+** para criar um alias de conexão.

    ![adicionar novo alias](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

6. Use os valores a seguir para o diálogo **Adicionar Alias**.

    * **Nome**: Hive no HDInsight

    * **Driver**: use o menu suspenso para selecionar o driver do **Hive**

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.

    * **Nome de Usuário**: o nome de conta de logon do cluster para o cluster HDInsight. O padrão é `admin`.

    * **Senha**: a senha da conta de logon do cluster.

 ![diálogo adicionar alias](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    Use o botão **Testar** para verificar se a conexão funciona. Quando o diálogo **Conectar a: Hive no HDInsight** for exibido, selecione **Conectar** para executar o teste. Se o teste tiver êxito, você verá um diálogo **Conexão bem-sucedida**. Se ocorrer um erro, consulte [Solução de problemas](#troubleshooting).

    Use o botão **Ok** na parte inferior do diálogo **Adicionar Alias** para salvar o alias de conexão.

7. Na lista suspensa **Conectar a**, na parte superior do SQuirreL SQL, selecione **Hive no HDInsight**. Quando solicitado, selecione **Conectar**.

    ![diálogo conexão](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

8. Uma vez conectado, insira a consulta a seguir no diálogo Consulta SQL e selecione o ícone **Executar**. A área de resultados deve mostrar os resultados da consulta.

        select * from hivesampletable limit 10;

    ![diálogo consulta sql, incluindo os resultados](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Conectar-se de um aplicativo Java de exemplo

Um exemplo de como usar um cliente Java para consultar o Hive no HDInsight está disponível em [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga as instruções no repositório para compilar e executar o exemplo.

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ocorreu um erro inesperado ao tentar abrir uma conexão SQL

**Sintomas**: ao se conectar a um cluster HDInsight versão 3.3 ou superior, você poderá receber uma mensagem indicando que ocorreu um erro inesperado. O rastreamento de pilha para esse erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: este erro é causado por um arquivo commons-codec.jar de versão mais antiga incluído com o SQuirreL.

**Resolução**: para corrigir esse erro, use as etapas a seguir:

1. Baixe o arquivo commons-codec.jar por meio do cluster HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Saia do SQuirreL e vá para o diretório em que o SQuirreL está instalado em seu sistema. No diretório do SquirreL, sob o diretório `lib` , substitua o commons-codec.jar existente pelo baixado por meio do cluster HDInsight.

3. Reinicie o SQuirreL. O erro não deverá ocorrer mais nas próximas conexões ao Hive no HDInsight.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o JDBC para trabalhar com Hive, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Usar Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](./hdinsight-connect-excel-hive-odbc-driver.md).
* [Conectar o Excel ao Hadoop usando Power Query](./hdinsight-connect-excel-power-query.md).
* [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
