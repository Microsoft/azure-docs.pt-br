---
title: Apache Spark e Hive – Hive Warehouse Connector – Azure HDInsight
description: Saiba como integrar o Apache Spark e o Apache Hive ao Hive Warehouse Connector no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 57a3d76f24c33984a883e926a8d4c68736e9f121
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869881"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrar o Apache Spark e o Apache Hive ao Hive Warehouse Connector no Azure HDInsight

O HWC (Apache Hive Warehouse Connector) é uma biblioteca que permite que você trabalhe mais facilmente com o Apache Spark e o Apache Hive. Ele dá suporte a tarefas como a movimentação de dados entre os dataframes do Spark e as tabelas do Hive. Além disso, direciona dados de streaming do Spark para tabelas do Hive. O Hive Warehouse Connector funciona como uma ponte entre o Spark e o Hive. Ele também dá suporte a Scala, Java e Python como linguagens de programação para desenvolvimento.

O Hive Warehouse Connector permite que você aproveite os recursos exclusivos do Hive e do Spark para criar aplicativos de Big Data poderosos.

O Apache Hive dá suporte a transações de banco de dados ACID (atômicas, consistentes, isoladas e duráveis). Para obter mais informações sobre ACID e transações no Hive, confira [Hive Transactions](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions) (Transações do Hive). O Hive também oferece controles de segurança detalhados por meio do Apache Ranger e LLAP (processamento analítico de baixa latência) não disponíveis no Apache Spark.

O Apache Spark tem uma API de streaming estruturado que fornece funcionalidades de streaming não disponíveis no Apache Hive. Começando com o HDInsight 4.0, o Apache Spark 2.3.1 e o Apache Hive 3.1.0 têm metastores separados. Os metastores separados podem dificultar a interoperabilidade. O Hive Warehouse Connector facilita o uso do Spark e do Hive juntos. A biblioteca HWC carrega dados de daemons do LLAP para executores do Spark em paralelo. Esse processo torna-o mais eficiente e adaptável do que uma conexão JDBC padrão do Spark com o Hive.

:::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png" alt-text="Arquitetura do Hive Warehouse Connector" border="true":::

Algumas das operações com suporte do Hive Warehouse Connector são:

* Descrever uma tabela
* Criar uma tabela para dados formatados por ORC
* Selecionar dados do Hive e recuperar um dataframe
* Gravar um dataframe no Hive em lote
* Executar uma instrução update do Hive
* Ler dados de tabela do Hive, transformá-los no Spark e gravá-los em uma nova tabela do Hive
* Escrever um dataframe ou um fluxo do Spark para o Hive usando HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Configuração do Hive Warehouse Connector

> [!IMPORTANT]
> - A instância interativa do HiveServer2 instalada no Spark 2,4 Enterprise Security Package clusters não tem suporte para uso com o conector do depósito do hive. Em vez disso, você deve configurar um cluster interativo HiveServer2 separado para hospedar suas cargas de trabalho interativas do HiveServer2. Não há suporte para uma configuração de conector de depósito do hive que utilize um único cluster Spark 2,4.
> - Não há suporte para a biblioteca do conector de depósito do hive (HWC) para uso com clusters de consulta interativa em que o recurso de gerenciamento de carga de trabalho (WLM) está habilitado. <br>
Em um cenário em que você tem apenas cargas de trabalho do Spark e deseja usar a biblioteca HWC, verifique se o cluster de consulta interativa não tem o recurso de gerenciamento de carga de trabalho habilitado (a `hive.server2.tez.interactive.queue` configuração não está definida nas configurações do hive). <br>
Para um cenário em que existem cargas de trabalho do Spark (HWC) e cargas de trabalho nativas do LLAP, você precisa criar dois clusters de consulta interativa separados com o banco de dados compartilhado do metastore. Um cluster para cargas de trabalho nativas de LLAP em que o recurso WLM pode ser habilitado com base em necessidade e outro cluster para HWC somente a carga de trabalho em que o recurso WLM não deve ser configurado.
É importante observar que você pode exibir os planos de recursos WLM de ambos os clusters, mesmo se ele estiver habilitado em apenas um cluster. Não faça nenhuma alteração nos planos de recursos no cluster em que o recurso WLM está desabilitado, pois ele pode afetar a funcionalidade WLM em outro cluster.

O Hive Warehouse Connector precisa de clusters separados para cargas de trabalho do Spark e do Interactive Query. Siga estas etapas para configurar esses clusters no Azure HDInsight.

### <a name="create-clusters"></a>Criar clusters

1. Crie um cluster do HDInsight Spark **4.0** com uma conta de armazenamento e uma rede virtual do Azure personalizada. Para obter informações sobre como criar um cluster em uma rede virtual do Azure, confira [Adicionar o HDInsight a uma rede virtual existente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Crie um cluster do LLAP (HDInsight Interactive Query) **4.0** com a mesma conta de armazenamento e rede virtual do Azure que o cluster Spark.

### <a name="configure-hwc-settings"></a>Definir configurações do HWC

#### <a name="gather-preliminary-information"></a>Reunir informações preliminares

1. Em um navegador da Web, navegue até `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE`, em que LLAPCLUSTERNAME é o nome do cluster Interactive Query.

1. Navegue até **Resumo** > **URL JDBC interativa do HiveServer2** e observe o valor. O valor pode ser semelhante a: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive`.

1. Navegue até **Configurações** > **Avançado** > **Site do Hive Avançado** > **hive.zookeeper.quorum** e observe o valor. O valor pode ser semelhante a: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Navegue até **Configurações** > **Avançado** > **Geral** > **hive.metastore.uris** e observe o valor. O valor pode ser semelhante a: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Navegue até **Configurações** > **Avançado** > **Site interativo do Hive Avançado** > **hive.llap.daemon.service.hosts** e observe o valor. O valor pode ser semelhante a: `@llap0`.

#### <a name="configure-spark-cluster-settings"></a>Definir configurações de cluster do Spark

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, em que CLUSTERNAME é o nome do cluster do Apache Spark.

1. Expanda **spark2-defaults personalizados**.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png" alt-text="Configuração do Apache Ambari Spark2" border="true":::

1. Selecione **Adicionar propriedade...** para adicionar as seguintes configurações:

    | Configuração | Valor |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Defina como um diretório de teste adequado compatível com HDFS. Se você tiver dois clusters diferentes, o diretório de teste deverá ser uma pasta no diretório de teste da conta de armazenamento do cluster LLAP para que HiveServer2 tenha acesso a ela.  Substitua `STORAGE_ACCOUNT_NAME` pelo nome da conta de armazenamento que está sendo usada pelo cluster e `STORAGE_CONTAINER_NAME` pelo nome do contêiner de armazenamento. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| O valor obtido anteriormente da **URL JDBC interativa do HiveServer2** |
    |`spark.datasource.hive.warehouse.metastoreUri`| O valor obtido anteriormente de **hive.metastore.uris**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` para o modo de cluster YARN e `false` para o modo de cliente YARN. |
    |`spark.hadoop.hive.zookeeper.quorum`| O valor obtido anteriormente de **hive.zookeeper.quorum**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| O valor obtido anteriormente de **hive.llap.daemon.service.hosts**. |

1. Salve as alterações e reinicie todos os componentes afetados.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Configurar o HWC para clusters do ESP (Enterprise Security Package)

O ESP (Enterprise Security Package) fornece funcionalidades de nível empresarial como autenticação baseada no Active Directory, suporte a multiusuário e controle de acesso baseado em função para clusters Apache Hadoop no Azure HDInsight. Para obter mais informações, confira [Usar o Enterprise Security Package no HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Além das configurações mencionadas na seção anterior, adicione a configuração a seguir para usar o HWC nos clusters ESP.

1. Na interface do usuário da Web do Ambari do cluster Spark, navegue até **Spark2** > **CONFIGURAÇÕES** > **spark2-defaults personalizados**.

1. Atualize as propriedades a seguir.

    | Configuração | Valor |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` onde ClusterName é o nome do seu cluster de consulta interativa. Clique em **HiveServer2 Interactive**. Você verá o FQDN (nome de domínio totalmente qualificado) do nó principal no qual o LLAP está em execução, conforme mostrado na captura de tela. Substituir `<llap-headnode>` pelo valor.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Nó principal do conector do depósito do hive" border="true":::

    * Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster de consulta interativa. Procure `default_realm` o parâmetro no `/etc/krb5.conf` arquivo. Substitua `<AAD-DOMAIN>` por esse valor como uma cadeia de caracteres em maiúsculas, caso contrário, a credencial não será encontrada.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Domínio AAD do conector do depósito do hive" border="true":::

    * Por exemplo, `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .
    
1. Salve as alterações e reinicie os componentes conforme necessário.

## <a name="hive-warehouse-connector-usage"></a>Uso do Hive Warehouse Connector

Você pode escolher entre alguns métodos diferentes para se conectar ao seu cluster Interactive Query e executar consultas usando o Hive Warehouse Connector. Os métodos com suporte incluem as seguintes ferramentas:

* [Spark-shell / PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Abaixo estão alguns exemplos para se conectar ao HWC do Spark.

### <a name="spark-shell"></a>Spark-Shell

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster do Apache Spark. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Em sua sessão SSH, execute o seguinte comando para anotar a versão de `hive-warehouse-connector-assembly`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Edite o código abaixo com a versão de `hive-warehouse-connector-assembly` identificada acima. Em seguida, execute o comando para iniciar o shell do Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Depois de iniciar o shell do Spark, uma instância do Hive Warehouse Connector pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

Depois de criar o código do Scala/Java junto com as dependências em um assembly jar, use o comando abaixo para iniciar um aplicativo Spark. Substitua `<VERSION>` e `<APP_JAR_PATH>` pelos valores reais.

* Modo de cliente YARN
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* Modo de cluster YARN
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Para o Python, adicione também a configuração a seguir. 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Executar consultas em clusters do ESP (Enterprise Security Package)

Use `kinit` antes de iniciar o spark-shell ou spark-submit. Substitua NOME DE USUÁRIO pelo nome de uma conta de domínio com permissões para acessar o cluster e execute o seguinte comando:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Protegendo dados em clusters do Spark ESP

1. Crie uma tabela `demo` com alguns dados de exemplo inserindo os seguintes comandos:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Veja o conteúdo da tabela com o comando a seguir. Antes de aplicar a política, a tabela `demo` mostra a coluna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png" alt-text="tabela de demonstração antes de aplicar a política do Ranger" border="true":::

1. Aplique uma política de mascaramento de coluna que mostre apenas os últimos quatro caracteres da coluna.  
    1. Vá para a interface do usuário administrador do Ranger em `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Clique no serviço do Hive para seu cluster em **Hive**.
        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png" alt-text="gerenciador de serviços do ranger" border="true":::
    1. Clique na guia **Mascaramento** e em **Adicionar Nova Política**

        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png" alt-text="lista de políticas do hive ranger do hive warehouse connector" border="true":::

    1. Forneça um nome de política desejado. Selecione o banco de dados: **Padrão**, tabela Hive: **demonstração**, coluna Hive: **nome**, Usuário: **rsadmin2**, Tipos de acesso: **selecionar** e **Máscara parcial: mostra os últimos quatro** no menu **Selecionar Opção de Mascaramento**. Clique em **Adicionar**.
                :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png" alt-text="criar política" border="true":::
1. Veja o conteúdo da tabela novamente. Depois de aplicar a política do Ranger, podemos ver apenas os últimos quatro caracteres da coluna.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png" alt-text="tabela de demonstração depois de aplicar a política do Ranger" border="true":::

## <a name="next-steps"></a>Próximas etapas

* [Operações do HWC e do Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Usar a Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md)
* [Integração do HWC ao Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Exemplos de interação com o Hive Warehouse Connector usando Zeppelin, Livy, spark-submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
