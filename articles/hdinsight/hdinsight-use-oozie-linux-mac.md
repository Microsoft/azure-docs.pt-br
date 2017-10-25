---
title: Usar os fluxos de trabalho de Sqoop do Hadoop no HDInsight baseado em Linux | Microsoft Docs
description: Usar o Oozie do Hadoop no HDInsight baseado em Linux. Saiba como definir um fluxo de trabalho do Oozie e enviar um trabalho do Oozie.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: larryfr
ms.openlocfilehash: 7f6985b80a88fd2e5d1fe0dafae47b95358d012d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Usar o Oozie com Hadoop para definir e executar um fluxo de trabalho no HDInsight baseado em Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Saiba como usar o Apache Oozie com o Hadoop no HDInsight. O Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos do Hadoop. O Oozie é integrado à pilha do Hadoop e dá suporte aos seguintes trabalhos:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

O Oozie também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell

> [!NOTE]
> Outra opção para definir fluxos de trabalho com o HDInsight é uma Azure Data Factory. Para conhecer mais o Azure Data Factory, confira [Usar o Pig e o Hive com o Data Factory][azure-data-factory-pig-hive].

> [!IMPORTANT]
> O Oozie não está habilitado no HDInsight ingressado no domínio.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster hdinsight**: consulte [Introdução ao HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

  > [!IMPORTANT]
  > As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Fluxo de trabalho de exemplo

O fluxo de trabalho usado neste documento contém duas ações. Ações são definições de tarefas, como Sqoop, Hive, MapReduce ou outro processo de execução:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

1. Uma ação do Hive executa um script HiveQL para extrair os registros de **hivesampletable** incluídos com o HDInsight. Cada linha de dados descreve uma visita de um dispositivo móvel específico. O formato de registro aparece semelhante ao seguinte texto:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    O script do Hive usado neste documento conta o total de visitas de cada plataforma (como Android ou iPhone) e armazena as contagens em uma nova tabela do Hive.

    Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].

2. Uma ação do Sqoop exporta o conteúdo da nova tabela Hive para uma tabela em um Banco de Dados SQL do Azure. Para saber mais sobre o Sqoop, confira [Usar o Hadoop Sqoop com o HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Para obter as versões do Oozie com suporte em clusters HDInsight, confira [Novidades nas versões de clusters Hadoop fornecidas pelo HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Criar o diretório de trabalho

O Oozie espera que os recursos necessários para um trabalho sejam armazenados no mesmo diretório. Este exemplo usa **wasb:///tutorials/useoozie**. Use o comando a seguir para criar esse diretório e o diretório de dados que armazenará a nova tabela do Hive criada por este fluxo de trabalho:

```
hdfs dfs -mkdir -p /tutorials/useoozie/data
```

> [!NOTE]
> O parâmetro `-p` faz com que todos os diretórios no caminho sejam criados. O diretório de **dados** é usado para armazenar dados usados pelo script **useooziewf.hql**.

Além disso, execute o seguinte comando, que garante que o Oozie possa representar a conta de usuário durante a execução de trabalhos de Hive e Sqoop. Substitua **NOME DE USUÁRIO** pelo seu nome de logon:

```
sudo adduser USERNAME users
```

> [!NOTE]
> Você pode ignorar os erros que indicam o usuário como já sendo membro do grupo `users`.

## <a name="add-a-database-driver"></a>Adicionar um driver de banco de dados

Como esse fluxo de trabalho usa Sqoop para exportar dados para o banco de dados SQL, você deve fornecer uma cópia do driver JDBC usado para se comunicar com o Banco de Dados SQL. Use o seguinte comando para copiá-lo para o diretório de trabalho:

```
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

Caso o fluxo de trabalho tenha usado outros recursos, como um jar que contém um aplicativo MapReduce, você precisará adicionar esses recursos também.

## <a name="define-the-hive-query"></a>Definir a consulta de Hive

Use as seguintes etapas para criar um script HiveQL que define uma consulta, que será usada em um fluxo de trabalho Oozie mais adiante neste documento.

1. Conecte-se ao cluster usando o SSH. O comando a seguir é um exemplo de como usar o comando `ssh`. Substitua __NOMEDOUSUÁRIO__ pelo nome de usuário SSH do cluster. Substitua __NOMEDOCLUSTER__ pelo nome do cluster HDInsight.

    ```
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Na conexão SSH, use o seguinte comando para criar um arquivo:

    ```
    nano useooziewf.hql
    ```

3. Quando o editor nano for aberto, use a seguinte consulta como o conteúdo do arquivo:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Duas variáveis são usadas no script:

    * **${hiveTableName}**: contém o nome da tabela a ser criada

    * **${hiveDataFolder}**: contém o local para armazenar os arquivos de dados para a tabela

    O arquivo de definição do fluxo de trabalho (workflow.xml neste tutorial) passa esses valores para o script HiveQL no tempo de execução

4. Para sair do editor, pressione Ctrl-X. Quando solicitado, selecione **Y** para salvar o arquivo e, em seguida, use **Enter** para usar o nome de arquivo **useooziewf.hql**.

5. Use os seguintes comandos para copiar **useooziewf.hql** para **wasb:///tutorials/useoozie/useooziewf.hql**:

    ```
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Esses comandos armazenam o arquivo **useooziewf.hql** no armazenamento compatível com o HDFS para o cluster.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

As definições de fluxos de trabalho do Oozie são escritas em hPDL (uma Linguagem de Definição de Processo XML). Use as etapas a seguir para definir o fluxo de trabalho:

1. Use a instrução a seguir para criar e editar um novo arquivo:

    ```
    nano workflow.xml
    ```

2. Quando o editor nano for aberto, insira o seguinte XML como o conteúdo do arquivo:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Existem duas ações definidas no fluxo de trabalho:

   * **RunHiveScript**: essa ação é a ação inicial e executa o script do Hive **useooziewf.hql**

   * **RunSqoopExport**: essa ação exporta os dados criados por meio do script do Hive para o Banco de Dados SQL usando o Sqoop. Essa ação será executada apenas se a ação **RunHiveScript** for bem-sucedida.

     O fluxo de trabalho tem várias entradas, como `${jobTracker}`. Essas entradas são substituídas por valores usados na definição de trabalho. A definição de trabalho é criada mais tarde neste documento.

     Observe também a entrada `<archive>sqljdbc4.jar</arcive>` na seção Sqoop. Essa entrada instrui o Oozie a disponibilizar esse arquivo morto ao Sqoop quando essa ação é executada.

3. Use Ctrl-X e, em seguida, **Y** e **Enter** para salvar o arquivo.

4. Use o comando a seguir para copiar o arquivo **workflow.xml** para **/tutorials/useoozie/workflow.xml**:

    ```
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Criar o banco de dados

Para criar um Banco de Dados SQL do Azure, siga as etapas do documento [Criar um Banco de Dados SQL](../sql-database/sql-database-get-started.md). Ao criar o banco de dados, use `oozietest` como o nome do banco de dados. Além disso, anote o nome do servidor de banco de dados.

### <a name="create-the-table"></a>Criar a tabela.

> [!NOTE]
> Há várias maneiras para se conectar ao Banco de Dados SQL para criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.


1. Use o seguinte comando para instalar o FreeTDS no cluster do HDInsight:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Uma vez que o FreeTDS tiver sido instalado, use o seguinte comando para conectar-se ao Banco de Dados SQL Server criado anteriormente:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Você receberá saídas semelhantes ao seguinte texto:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Ao prompt `1>` , insira o seguinte:

    ```
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas. Essas instruções criam uma tabela chamada **mobiledata** que é usada pelo fluxo de trabalho.

    Use o seguinte para verificar se a tabela foi criada:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    Você verá uma saída semelhante ao seguinte texto:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo     mobiledata      BASE TABLE
    ```

4. Para sair do utilitário tsql, insira `exit` at the `1>` .

## <a name="create-the-job-definition"></a>Criar a definição de trabalho

A definição de trabalho descreve o local em que o workflow.xml se encontra. Ela também descreve o local em que você pode encontrar outros arquivos usados pelo fluxo de trabalho (como useooziewf.hql). Isso também define os valores para as propriedades usadas no fluxo de trabalho e arquivos associados.

1. Use o comando a seguir para obter o endereço completo do armazenamento padrão. Este endereço será usado no arquivo de configuração em breve:

    ```
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Este comando retorna informações semelhantes ao seguinte XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Se o cluster HDInsight usa o armazenamento do Azure como o armazenamento padrão, o conteúdo de elemento `<value>` começa com `wasb://`. Se o Azure Data Lake Store é usado, ele começa com `adl://`.

    Salve o conteúdo do elemento `<value>`, pois ele será usado nas próximas etapas.

2. Use o seguinte comando para obter o FQDN do headnode do cluster. Estas informações são usadas para o endereço do JobTracker no cluster:

    ```
    hostname -f
    ```

    Isso retorna informações semelhantes ao seguinte texto:

    ```hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net```

    A porta usada para o JobTracker é a 8050, portanto, o endereço completo a ser usado para o JobTracker é `hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050`.

3. Use o seguinte para criar a configuração de definição de trabalho do Oozie:

    ```
    nano job.xml
    ```

4. Quando o editor nano for aberto, use o seguinte XML como o conteúdo do arquivo:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>JOBTRACKERADDRESS</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Substitua todas as instâncias de **wasb://mycontainer@mystorageaccount.blob.core.windows.net** pelo valor que você recebeu anteriormente para armazenamento padrão.

     > [!WARNING]
     > Se o caminho for um caminho `wasb`, use o caminho completo. Não o reduza para apenas `wasb:///`.

   * Substitua **JOBTRACKERADDRESS** pelo endereço de JobTracker/ResourceManager recebido anteriormente.
   * Substitua **YourName** pelo seu nome de logon para o cluster do HDInsight.
   * Substitua **serverName**, **adminLogin** e **adminPassword** pelas informações de seu Banco de Dados SQL do Azure.

     A maioria das informações contidas nesse arquivo é usada para preencher os valores usados nos arquivos workflow.xml ou ooziewf.hql (por exemplo, ${nameNode}.)

     > [!NOTE]
     > A entrada **oozie.wf.application.path** define onde encontrar o arquivo workflow.xml, que contém o fluxo de trabalho executado por esse trabalho.

5. Use Ctrl-X e, em seguida, **Y** e **Enter** para salvar o arquivo.

## <a name="submit-and-manage-the-job"></a>Enviar e gerenciar o trabalho

As etapas a seguir usam o comando Oozie para enviar e gerenciar fluxos de trabalho do Oozie no cluster. O comando do Oozie é uma interface amigável sobre a [API REST do Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Ao usar o comando Oozie, você deve usar o FQDN para headnode do HDInsight. Esse FQDN só está acessível no cluster ou, se o cluster estiver em uma Rede Virtual do Azure, de outros computadores na mesma rede.


1. Use o seguinte para obter a URL para o serviço do Oozie:

    ```
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Isso retorna informações semelhantes ao seguinte XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A parte `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` é a URL a ser usada com o comando do Oozie.

2. Use o seguinte para criar uma variável de ambiente para a URL para que você não precise digitá-la para cada comando:

    ```
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Substitua a URL pela recebida anteriormente.
3. Use o seguinte para enviar o trabalho:

    ```
    oozie job -config job.xml -submit
    ```

    Esse comando carrega as informações do trabalho de **job.xml** e as envia para o Oozie, mas não o executa.

    Quando o comando for concluído, ele deverá retornar a ID do trabalho. Por exemplo: `0000005-150622124850154-oozie-oozi-W`. Essa ID será usada para gerenciar o trabalho.

4. Exiba o status do trabalho usando o seguinte comando:

    ```
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Substitua `<JOBID>` pela ID retornada na etapa anterior.

    Isso retorna informações semelhantes ao seguinte texto:

    ```
    Job ID : 0000005-150622124850154-oozie-oozi-W
    ------------------------------------------------------------------------------------------------------------------------------------
    Workflow Name : useooziewf
    App Path      : wasb:///tutorials/useoozie
    Status        : PREP
    Run           : 0
    User          : USERNAME
    Group         : -
    Created       : 2015-06-22 15:06 GMT
    Started       : -
    Last Modified : 2015-06-22 15:06 GMT
    Ended         : -
    CoordAction ID: -
    ------------------------------------------------------------------------------------------------------------------------------------
    ```

    Este trabalho tem o status `PREP`. Este status indica que o trabalho foi criado, mas não foi iniciado.

5. Use o seguinte comando para iniciar o trabalho:

    ```
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Substitua `<JOBID>` pela ID retornada anteriormente.

    Se você verificar o status após este comando, ele estará em um estado de execução e as informações serão retornadas para as ações dentro do trabalho.

6. Depois que a tarefa for concluída com sucesso, você poderá verificar se os dados foram gerados e exportados para a tabela do Banco de Dados SQL usando os seguintes comandos:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    No prompt `1>`, insira a seguinte consulta:

    ```
    SELECT * FROM mobiledata
    GO
    ```

    As informações retornadas são semelhantes ao seguinte texto:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para obter mais informações sobre o comando do Oozie, consulte [Ferramenta de linha de comando do Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API REST do Oozie

A API REST do Oozie permite que você crie suas próprias ferramentas que funcionam com o Oozie. A seguir estão informações específicas do HDInsight sobre como usar a API REST do Oozie:

* **URI**: a API REST pode ser acessada de fora do cluster em `https://CLUSTERNAME.azurehdinsight.net/oozie`

* **Autenticação**: faça a autenticação na API usando a conta HTTP do cluster (admin) e a senha. Por exemplo:

    ```
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Para obter mais informações sobre como usar a API REST do Oozie, consulte [API de serviços Web do Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interface da Web do Oozie

A IU da Web do Oozie fornece um modo de exibição baseado na web sobre o status dos trabalhos do Oozie no cluster. A interface do usuário da Web permite exibir as seguintes informações:

* Status do trabalho
* Definição de trabalho
* Configuração
* Um gráfico das ações no trabalho
* Logs do trabalho

Você também pode exibir detalhes de ações dentro de um trabalho.

Para acessar a interface do usuário do Oozie da Web, use as seguintes etapas:

1. Crie um túnel SSH para o cluster HDInsight. Para obter informações, consulte o documento [Usar o túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Quando um túnel tiver sido criado, abra a interface do usuário da Web do Ambari no navegador da Web. O URI para o site Ambari é **https://CLUSTERNAME.azurehdinsight.net**. Substitua **CLUSTERNAME** pelo nome do seu cluster do HDInsight baseado em Linux.

3. No lado esquerdo da página, selecione **Oozie** e, em seguida, **Links rápidos** e, finalmente, **Interface do usuário da Web do Oozie**.

    ![imagem dos menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. A interface do usuário da Web do Oozie assume como padrão a exibição de Trabalhos do fluxo de trabalho em execução. Para ver todos os trabalhos de fluxo de trabalho, selecione **Todos os trabalhos**.

    ![Todos os trabalhos exibidos](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Selecione um trabalho para exibir mais informações sobre ele.

    ![Informações do Trabalho](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Na guia Informações do Trabalho, veja informações básicas sobre o trabalho, bem como as ações individuais dentro do trabalho. Usando as guias na parte superior, você pode ver a Definição de trabalho, a Configuração de trabalho, acessar o Log de trabalho ou ver um gráfico acíclico dirigido (DAG) do trabalho.

   * **Log de trabalho**: selecione o botão **GetLogs** para obter todos os logs do trabalho, ou use o campo **Inserir filtro de pesquisa** para filtrar os logs

       ![Log de trabalhos](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **JobDAG**: o DAG é uma visão geral gráfica dos caminhos de dados percorridos pelo fluxo de trabalho

       ![DAG de trabalho](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Selecionar uma das ações na guia **Informações do Trabalho** exibe informações para a ação. Por exemplo, selecione a ação **RunHiveScript** .

    ![Informações da ação](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Você pode ver detalhes da ação, como um link para a **URL do Console**. Esse link pode ser usado para exibir informações do JobTracker para o trabalho.

## <a name="scheduling-jobs"></a>Agendamento de trabalhos

O coordenador permite que você especifique um início, um fim e a frequência de ocorrência para trabalhos. Para definir uma agenda para o fluxo de trabalho, use as seguintes etapas:

1. Use o seguinte para criar um arquivo chamado **coordinator.xml**:

    ```
    nano coordinator.xml
    ```

    Use o seguinte XML como o conteúdo do arquivo:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]
    > As variáveis `${...}` são substituídas pelos valores na definição de trabalho em tempo de execução. As variáveis são:
    >
    > * `${coordFrequency}`: tempo entre as instâncias em execução do trabalho.
    > ** `${coordStart}`: a hora de início do trabalho.
    > * `${coordEnd}`: a hora de término do trabalho.
    > * `${coordTimezone}`: os trabalhos do coordenador estão em um fuso horário fixo sem horário de verão (geralmente representado com o uso do UTC). Esse fuso horário é chamado de "fuso horário de processamento do Oozie".
    > * `${wfPath}`: o caminho para workflow.xml.

2. Para salvar o arquivo, use Ctrl-X, **Y** e **Enter**.

3. Para copiar o arquivo para o diretório de trabalho para este trabalho, use o seguinte:

    ```
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Use o seguinte para modificar o arquivo **job.xml** :

    ```
    nano job.xml
    ```

    Faça as seguintes alterações:

   * Para instruir o Oozie a executar o arquivo coordenador em vez do de fluxo de trabalho, altere `<name>oozie.wf.application.path</name>` para `<name>oozie.coord.application.path</name>`.

   * Para definir o `workflowPath` variável usada pelo coordenador, adicione o XML a seguir:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Substitua o texto `wasb://mycontainer@mystorageaccount.blob.core.windows` pelo valor usado em outras entradas no arquivo job.xml.

   * Para definir o início, o fim e a frequência a usar para o coordenador, adicione o XML a seguir:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Esses valores definem a hora de início como 12h00 em 10 de maio de 10, 2017 e a hora de término como 12 de maio de 12, 2017. O intervalo para execução desse trabalho é diário. A frequência está em minutos, então 24 horas x 60 minutos = 1440 minutos. Por fim, o fuso horário é definido como UTC.

5. Use Ctrl-X e, em seguida, **Y** e **Enter** para salvar o arquivo.

6. Para executar o trabalho, use o seguinte comando:

    ```
    oozie job -config job.xml -run
    ```

    Esse comando envia e inicia o trabalho.

7. Se você visitar a interface do usuário da Web do Oozie e selecionar a guia **Trabalhos do Coordenador**, verá informações semelhantes à seguinte imagem:

    ![guia de trabalhos do coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    A entrada **Próxima Materialização** contém a próxima hora em que o trabalho é executado.

8. De forma semelhante ao fluxo de trabalho anterior, selecionar a entrada de trabalho na interface do usuário da Web exibirá informações sobre o trabalho:

    ![Informações de trabalho do coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Essa imagem mostra apenas as execuções bem-sucedidas do trabalho, não as ações individuais do fluxo de trabalho agendado. Para ver isso, selecione uma das entradas de **Ação** .

    ![Informações da ação](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Solucionar problemas

A interface do usuário do Oozie permite exibir os logs do Oozie. Ela também contém links para logs do JobTracker de tarefas do MapReduce iniciadas pelo fluxo de trabalho. O padrão para solução de problemas deve ser:

1. Exiba o trabalho na interface do usuário da Web do Oozie.

2. Se houver um erro ou falha de uma ação específica, selecione a ação para ver se o campo **Mensagem de Erro** fornece mais informações sobre a falha.

3. Se estiver disponível, use a URL da ação para exibir mais detalhes (como logs do JobTracker) para a ação.

A seguir estão os erros específicos que podem ser encontrados e como resolvê-los.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: não é possível inicializar o cluster

**Sintomas**: o status do trabalho é alterado para **SUSPENSO**. Os detalhes do trabalho mostram o status de RunHiveScript como **START_MANUAL**. Selecionar a ação exibirá a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: os endereços de WASB usados no arquivo **job.xml** não contêm o contêiner de armazenamento ou o nome da conta de armazenamento. O formato do endereço WASB deve ser `wasb://containername@storageaccountname.blob.core.windows.net`.

**Resolução**: alterar os endereços WASB usados pelo trabalho.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: o Oozie não tem permissão para representar o &lt;USER>

**Sintomas**: o status do trabalho é alterado para **SUSPENSO**. Os detalhes do trabalho mostram o status de RunHiveScript como **START_MANUAL**. Selecionar a ação exibirá a seguinte mensagem de erro:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: as configurações de permissão atuais não permitem ao Oozie representar a conta de usuário especificada.

**Resolução**: o Oozie tem permissão para representar os usuários no grupo **usuários**. Use o `groups USERNAME` para ver os grupos do qual a conta de usuário é membro. Se o usuário não for membro do grupo **usuários** , use o seguinte comando para adicionar o usuário ao grupo:

    sudo adduser USERNAME users

> [!NOTE]
> Pode levar vários minutos antes de o HDInsight reconhecer que o usuário foi adicionado ao grupo.

### <a name="launcher-error-sqoop"></a>ERRO do Iniciador (Sqoop)

**Sintomas**: o status do trabalho é alterado para **ENCERRADO**. Os detalhes do trabalho mostram o status de RunSqoopExport como **ERRO**. Selecionar a ação exibirá a seguinte mensagem de erro:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: o Sqoop não conseguiu carregar o driver do banco de dados necessário para acessar o banco de dados.

**Resolução**: ao usar o Sqoop em um trabalho do Oozie, você deve incluir o driver de banco de dados com os outros recursos (como o workflow.xml) usados pelo trabalho. Também referencie o arquivo morto que contém o driver de banco de dados na seção `<sqoop>...</sqoop>` do workflow.xml.

Por exemplo, para o trabalho neste documento, você usaria o seguinte procedimento:

1. Copie o arquivo sqljdbc4.1.jar para o diretório /tutorials/useoozie:

    ```
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Modifique o workflow.xml para adicionar o seguinte XML a uma nova linha acima `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a definir um fluxo de trabalho do Oozie e a executar um trabalho do Oozie. Para saber mais sobre como trabalhar com o HDInsight, consulte os seguintes artigos:

* [Usar o Coordenador do Oozie baseado em tempo com o HDInsight][hdinsight-oozie-coordinator-time]
* [Carregar dados para trabalhos do Hadoop no HDInsight][hdinsight-upload-data]
* [Usar Sqoop com o Hadoop no HDInsight][hdinsight-use-sqoop]
* [Usar o Hive com Hadoop no HDInsight][hdinsight-use-hive]
* [Usar o Pig com Hadoop no HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
