---
title: Integrar o Apache Spark e Apache Hive com o conector de Warehouse do Hive
description: Saiba como integrar o Apache Spark e Apache Hive com o conector de Warehouse do Hive no HDInsight do Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/18/2019
ms.openlocfilehash: b450fe763104adbbd08e4b5f362bd51ffbf82c81
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126538"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrar o Apache Spark e Apache Hive com o conector de Warehouse do Hive

O Apache Hive Warehouse Connector (HWC) é uma biblioteca que permite que você trabalhe com mais facilidade com o Apache Spark e Apache Hive, oferecendo suporte a tarefas, como mover dados entre o Spark DataFrames e tabelas do Hive e também direcionando o Spark streaming de dados em tabelas do Hive. Conector do Warehouse funciona como uma ponte entre o Spark e Hive do hive. Ele dá suporte ao Scala, Java e Python para o desenvolvimento.

O conector de Warehouse do Hive permite que você aproveite os recursos exclusivos do Hive e Spark para criar aplicativos poderosos de big data. Apache Hive oferece suporte para transações de banco de dados que são atômico, consistente, isolado e durável (ACID). Para obter mais informações sobre ACID e transações no Hive, consulte [transações Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive também oferece controles de segurança detalhada por meio do Apache Ranger e baixa latência de processamento analítico não está disponível no Apache Spark.

Apache Spark, tem uma API de Streaming estruturado que fornece recursos de streaming não está disponíveis no Apache Hive. Começando com a Hortonworks Data Platform (HDP) 3.0, Apache Spark e Apache Hive tem metastores separado, que pode dificultar a interoperabilidade. O conector de Warehouse do Hive torna mais fácil de usar o Spark e Hive juntos. A biblioteca HWC carrega dados de daemons LLAP para executores Spark em paralelo, tornando mais eficiente e escalonável do que usar uma conexão JDBC padrão do Spark ao Hive.

![Arquitetura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Algumas das operações compatíveis com o conector de Warehouse do Hive são:

* Que descreve uma tabela
* Criando uma tabela de dados em formato ORC
* Selecionando dados de Hive e recuperando um DataFrame
* Gravar um DataFrame do Hive em lote
* Executar uma instrução de atualização do Hive
* Lendo dados da tabela do Hive, transformando-o no Spark e gravá-la em uma nova tabela de Hive
* Gravar um fluxo de DataFrame Spark ou do Hive usando HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Instalação do conector de Warehouse do hive

Siga estas etapas para configurar o conector de Warehouse do Hive entre um cluster Spark e consulta interativa no HDInsight do Azure:

1. Crie um cluster HDInsight Spark 4.0 usando o portal do Azure com uma conta de armazenamento e uma rede virtual do Azure personalizada. Para obter informações sobre como criar um cluster em uma rede virtual do Azure, consulte [adicionar HDInsight a uma rede virtual existente](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Crie um cluster HDInsight Interactive Query (LLAP) 4.0 usando o portal do Azure com a mesma conta de armazenamento e rede virtual do Azure como o cluster Spark.
1. Copie o conteúdo do `/etc/hosts` arquivo no headnode0 do cluster consulta interativa para o `/etc/hosts` arquivo no headnode0 do cluster do Spark. Esta etapa permitirá que seu cluster Spark para resolver endereços IP de nós no cluster de consulta interativa. Exibir o conteúdo do arquivo atualizado com `cat /etc/hosts`. A saída deve ser algo parecido com o que é mostrado na captura de tela abaixo.

    ![exibindo o arquivo de hosts](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Defina as configurações de cluster do Spark seguindo as etapas a seguir: 
    1. Vá para o portal do Azure, selecione clusters do HDInsight e, em seguida, clique no nome do seu cluster.
    1. No lado direito, sob **painéis do Cluster**, selecione **Ambari base**.
    1. Na interface do usuário do web Ambari, clique em **SPARK2** > **configurações** > **spark2-defaults personalizados**.

        ![Configuração do Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Definir `spark.hadoop.hive.llap.daemon.service.hosts` com o mesmo valor da propriedade **nome do aplicativo LLAP** sob **Advanced hive interativo env**. Por exemplo, `@llap0`

    1. Definir `spark.sql.hive.hiveserver2.jdbc.url` na cadeia de caracteres de conexão do JDBC, que conecta-se ao Hiveserver2 no cluster da consulta interativa. A cadeia de caracteres de conexão para seu cluster se parecerá com o URI abaixo. `CLUSTERNAME` é o nome do seu cluster Spark e o `user` e `password` parâmetros são definidos para os valores corretos para seu cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > A URL do JDBC deve conter credenciais para conectar-se ao Hiveserver2 incluindo um nome de usuário e senha.

    1. Definir `spark.datasource.hive.warehouse.load.staging.dir` para um diretório de preparo adequado compatível com HDFS. Se você tiver dois clusters diferentes, o diretório de preparo deve ser uma pasta na pasta de preparação do cluster LLAP conta de armazenamento para que o HiveServer2 tem acesso a ele. Por exemplo, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` onde `STORAGE_ACCOUNT_NAME` é o nome da conta de armazenamento que está sendo usado pelo cluster, e `STORAGE_CONTAINER_NAME` é o nome do contêiner de armazenamento.

    1. Definir `spark.datasource.hive.warehouse.metastoreUri` com o valor do URI do cluster da consulta interativa do metastore. Para localizar o metastoreUri para seu cluster LLAP, procure o **hive.metastore.uris** propriedade da UI do Ambari para seu LLAP do cluster sob **Hive** > **avançado**  >  **Gerais**. O valor será algo parecido com o seguinte URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Definir `spark.security.credentials.hiveserver2.enabled` para `false` implante o modo para o cliente do YARN.
    1. Definir `spark.hadoop.hive.zookeeper.quorum` no quorum Zookeeper do Cluster LLAP. Para localizar o quorum Zookeeper para o cluster LLAP, procure o **hive.zookeeper.quorum** propriedade da UI do Ambari para seu LLAP do cluster sob **Hive** > **avançado**  >  **Site hive avançado**. O valor será algo parecido com a cadeia de caracteres a seguir:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Para testar a configuração do seu conector de Warehouse do Hive, siga as etapas na seção [consultas em execução e está se conectando](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Usar o conector de Warehouse do Hive

### <a name="connecting-and-running-queries"></a>Conectar-se e execução de consultas

Você pode escolher entre alguns métodos diferentes para se conectar ao seu cluster de consulta interativa e executar consultas usando o conector de Warehouse do Hive. Métodos com suporte incluem as seguintes ferramentas:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Todos os exemplos fornecidos neste artigo serão executados por meio do shell do spark.

Para iniciar uma sessão do shell do spark, execute as seguintes etapas:

1. SSH no nó principal do cluster. Para obter mais informações sobre como se conectar ao seu cluster com SSH, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Mude para o diretório correto digitando `cd /usr/hdp/current/hive_warehouse_connector` ou fornecer o caminho completo para todos os arquivos jar usados como parâmetros no comando spark-shell.
1. Insira o seguinte comando para iniciar o shell do spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Você verá uma mensagem de boas-vinda e um `scala>` prompt onde você pode inserir comandos.

1. Depois de iniciar o shell do spark, uma instância do conector de Warehouse do Hive pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Conectar-se e execução de consultas em clusters do pacote de segurança Enterprise (ESP)

O pacote de segurança de Enterprise (ESP) fornece recursos de nível empresarial como autenticação baseada no Active Directory, suporte a vários usuários e controle de acesso baseado em função para clusters do Apache Hadoop no HDInsight do Azure. Para obter mais informações sobre o ESP, consulte [uma introdução à segurança do Apache Hadoop com o pacote de segurança empresarial](../domain-joined/apache-domain-joined-introduction.md).

1. Siga as etapas iniciais 1 e 2 sob [consultas em execução e está se conectando](#connecting-and-running-queries).
1. Tipo `kinit` e faça logon com um usuário de domínio.
1. Iniciar o spark-shell com a lista completa de parâmetros de configuração, conforme mostrado abaixo. Todos os valores em letras maiusculas entre colchetes angulares devem ser especificados com base em seu cluster. Se você precisar descobrir os valores de entrada para qualquer um dos parâmetros abaixo, consulte a seção sobre [instalação do conector de Warehouse do Hive](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Criando o Spark DataFrames consultas de Hive

Os resultados de todas as consultas usando a biblioteca HWC são retornados como um DataFrame. Os exemplos a seguir demonstram como criar uma consulta básica.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são Spark DataFrames, que pode ser usado com bibliotecas de Spark como MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Gravação out Spark DataFrames para tabelas Hive

Spark não oferece suporte a gravação em tabelas gerenciadas de ACID do Hive. Usando o HWC, no entanto, você pode gravar qualquer DataFrame em uma tabela do Hive. Você pode ver essa funcionalidade em funcionamento no exemplo a seguir:

1. Criar uma tabela chamada `sampletable_colorado` e especifique suas colunas usando o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Filtrar a tabela `hivesampletable` em que a coluna `state` é igual a `Colorado`. Essa consulta da tabela de Hive é retornada como um DataFrame do Spark. Em seguida, o DataFrame é salvo na tabela de Hive `sampletable_colorado` usando o `write` função.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Você pode ver a tabela resultante na captura de tela abaixo.

![Mostrar tabela resultante](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Gravações de streaming estruturado

Usando o conector de Warehouse do Hive, você pode usar para gravar dados em tabelas de Hive de streaming do Spark.

Siga as etapas abaixo para criar um exemplo de conector de Warehouse do Hive que recebe dados de um Spark stream na porta do localhost 9999 em uma tabela Hive.

1. Abra um terminal no seu cluster Spark.
1. Iniciar o spark stream com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Gere dados para o Spark stream que você criou seguindo as etapas a seguir:
    1. Abra outro terminal no mesmo cluster Spark.
    1. No prompt de comando, digite `nc -lk 9999`. Esse comando usa o utilitário netcat para enviar dados de linha de comando para a porta especificada.
    1. Digite as palavras que você gostaria que o Spark stream para ingestão, seguido por um retorno de carro.
        ![dados de entrada para o spark stream](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Crie uma nova tabela de Hive para manter os dados de streaming. No shell do spark, digite os seguintes comandos:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Grave o fluxo de dados na tabela criada recentemente usando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > O `metastoreUri` e `database` opções no momento devem ser definidas manualmente a um problema conhecido no Apache Spark. Para obter mais informações sobre esse problema, consulte [25460 SPARK](https://issues.apache.org/jira/browse/SPARK-25460).

1. Você pode exibir os dados inseridos na tabela com o seguinte comando:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Protegendo dados em clusters do Spark ESP

1. Criar uma tabela `demo` com alguns dados de exemplo, inserindo os comandos a seguir:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Exiba o conteúdo da tabela com o comando a seguir. Antes de aplicar a política, o `demo` tabela exibe a coluna inteira.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabela demonstração antes de aplicar a política do ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplica uma coluna que mostra apenas os últimos quatro caracteres da coluna de política de mascaramento.  
    1. Vá para a interface do usuário do Ranger Admin no `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Clique no serviço do Hive para seu cluster sob **Hive**.
        ![tabela demonstração antes de aplicar a política do ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique no **mascaramento** guia e, em seguida **adicionar nova política** ![lista de políticas](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Forneça um nome de política desejadas. Selecione o banco de dados: **Padrão**, tabela do Hive: **demonstração**, a coluna de Hive: **nome**, usuário: **rsadmin2**, tipos de acesso: **selecione**e **Máscara parcial: mostrar as últimas 4** da **Selecionar opção de máscara** menu. Clique em **Adicionar**.
                ![lista de políticas](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Exiba o conteúdo da tabela novamente. Depois de aplicar a política do ranger, podemos ver apenas os últimos quatro caracteres da coluna.

    ![tabela demonstração após a aplicação de política do ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Próximas etapas

* [Usar a consulta interativa com o HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Exemplos de interagir com o conector de Warehouse do Hive usando o Zeppelin, Livy, spark-submit e o pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
