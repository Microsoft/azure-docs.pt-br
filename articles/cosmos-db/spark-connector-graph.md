---
title: "BD Cosmos do Azure: executar análise de gráfico usando o Spark e o Gremlin do Apache TinkerPops | Microsoft Docs"
description: "Este artigo apresenta instruções para configurar e executar computação paralela e análise de gráfico no BD Cosmos do Azure com Spark e TinkerPop SparkGraphComputer."
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.openlocfilehash: 3ba34ac177cb9b88030be85288e3b3a429d4fd8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>BD Cosmos do Azure: executar análise de gráfico usando o Spark e o Gremlin do Apache TinkerPop

[Azure Cosmos DB](introduction.md) é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do BD Cosmos do Azure. O BD Costmos do Azure dá suporte a cargas de trabalho gráficas OLTP (processamento de transações online) que usam o [Gremlin do Apache TinkerPop](graph-introduction.md).

[Spark](http://spark.apache.org/) é um projeto Apache Software Foundation voltado para o processamento de dados OLAP (processamento analítico online de uso geral). O Spark fornece um modelo de computação distribuída híbrido em memória/baseado em disco que é semelhante ao modelo MapReduce do Hadoop. Você pode implantar o Apache Spark na nuvem usando o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Combinando o BD Cosmos do Azure e o Spark, você pode executar cargas de trabalho OLTP e OLAP quando usar o Gremlin. Este artigo rápido demonstra como executar consultas do Gremlin no Banco de Dados Cosmos do Azure em um cluster Spark do Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Antes que possa executar esta amostra, você deverá ter os seguintes pré-requisitos:
* Cluster Spark do Azure HDInsight 2.0
* JDK 1.8 + (execute `apt-get install default-jdk` se você não tiver o JDK)
* Maven (execute `apt-get install maven` se você não tiver o Maven)
* Uma assinatura do Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Para obter informações sobre como configurar um cluster Spark no HDInsight, consulte [Provisionando clusters HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Criar uma conta de banco de dados BD Cosmos do Azure

Primeiro, crie uma conta de banco de dados com a API do Graph fazendo o seguinte:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Obter o Apache TinkerPop

Obtenha o Apache TinkerPop fazendo o seguinte:

1. Vá remotamente para o nó principal do cluster HDInsight`ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Clonar o código-fonte do TinkerPop3, compilá-lo localmente e instalar no cache do Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Instalar o plug-in Spark-Gremlin 

    a. A instalação do plug-in é tratada pelo Grape. Preencha as informações de repositórios do Grape para que possa baixar o plug-in e suas dependências. 

      Crie o arquivo de configuração do Grape se não estiver presente no `~/.groovy/grapeConfig.xml`. Use as configurações a seguir:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Inicie o console do Gremlin `bin/gremlin.sh`.
        
    c. Instale o plug-in Spark-Gremlin com a versão 3.3.0-SNAPSHOT que você criou nas etapas anteriores:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Verifique se `Hadoop-Gremlin` é ativado com `:plugin list`. Desabilite esse plug-in, pois ele pode interferir no plug-in Spark-Gremlin `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Preparar dependências TinkerPop3

Quando você criou o TinkerPop3 na etapa anterior, o processo também puxou todas as dependências jar para o Spark e o Hadoop no diretório de destino. Use os jars pré-instalado com HDI e só use dependências adicionais se necessário.

1. Vá para o diretório de destino do Console do Gremlin em `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Mova todos os jars em `ext/` para `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Remova todas as bibliotecas jar em `lib/` que não estiverem na seguinte lista:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Obter o conector Spark do BD Cosmos do Azure

1. Obter o Conector Spark do BD Cosmos do Azure `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` e o SDK Java do BD Cosmos `azure-documentdb-1.10.0.jar` no [Conector Spark para BD Cosmos do Azure no GitHub](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Como alternativa, você pode criá-lo localmente. Como a versão mais recente do Spark-Gremlin foi criada com o Spark 1.6.1 e não é compatível com o Spark 2.0.2, que é usado atualmente no Conector Spark do BD Cosmos do Azure, você pode compilar o código TinkerPop3 mais recente e instalar os jars manualmente. Faça o seguinte:

    a. Clone o conector Spark do BD Cosmos do Azure.

    b. Crie o TinkerPop3 (já feito nas etapas anteriores). Instale todos os jars TinkerPop 3.3.0-SNAPSHOT localmente.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Atualize `tinkerpop.version` `azure-documentdb-spark/pom.xml` para `3.3.0-SNAPSHOT`.
    
    d. Compile com Maven. Os jars necessários são colocados em `target` e `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copie os jars mencionados anteriormente para um diretório local em ~/azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuir as dependências nos nós de trabalho Spark 

1. Como a transformação de dados do gráfico depende do TinkerPop3, você precisa distribuir as dependências relacionadas para todos os nós de trabalho Spark.

2. Copie as dependências do Gremlin mencionadas anteriormente, além do jar conector Spark do CosmosDB e do SDK Java do CosmosDB para os nós de trabalho fazendo o seguinte:

    a. Copie todos os jars em `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Obtenha a lista de todos os nós de trabalho Spark encontrados no painel do Ambari, na lista `Spark2 Clients` da seção `Spark2`.

    c. Copiar o diretório para cada um dos nós.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Configurar as variáveis de ambiente

1. Localize a versão HDP do cluster Spark. É o nome do diretório em `/usr/hdp/` (por exemplo, 2.5.4.2-7).

2. Defina hdp.version para todos os nós. No painel do Ambari, vá para a **seção YARN** > **Configurações** > **Avançado** e faça o seguinte: 
 
    a. Em `Custom yarn-site`, adicione uma nova propriedade `hdp.version` com o valor da versão do HDP no nó principal. 
     
    b. Salve as configurações. Existem avisos, mas eles podem ser ignorados. 
     
    c. Reinicie os serviços YARN e Oozie conforme indicado pelos ícones de notificação.

3. Defina as seguintes variáveis de ambiente no nó principal (substitua os valores conforme apropriado):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Preparar a configuração do gráfico

1. Crie um arquivo de configuração com os parâmetros de conexão do BD Cosmos do Azure e as configurações do Spark e coloque-o em`tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Atualize `spark.driver.extraClassPath` e `spark.executor.extraClassPath` para incluir o diretório dos jars distribuído na etapa anterior, neste caso, `/home/sshuser/azure-documentdb-spark/*`.

3. Forneça os seguintes detalhes para o BD Cosmos do Azure:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Carregar o gráfico do TinkerPop e salve-o no BD Cosmos do Azure
Para demonstrar como persistir um gráfico no BD Cosmos do Azure, este exemplo usa o gráfico moderno TinkerPop predefinido. O gráfico é armazenado no formato Kryo e fornecido no repositório TinkerPop.

1. Como você está executando o Gremlin no modo YARN, deve disponibilizar os dados do gráfico no sistema de arquivos Hadoop. Use os comandos a seguir para criar um diretório e copiam o arquivo do gráfico local nele. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Atualize temporariamente o arquivo `gremlin-spark.properties` para usar `GryoInputFormat` e ler o gráfico. Indique também `inputLocation` como o diretório criado, como no exemplo abaixo:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Inicie o Console do Gremlin e crie as seguintes etapas de computação para persistir dados para a coleção do BD Cosmos do Azure configurada:  

    a. Crie o gráfico `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Use SparkGraphComputer para gravar `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. No Data Explorer, você pode verificar se os dados foram persistidos para o BD Cosmos do Azure.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Carregue o gráfico do BD Cosmos do Azure e execute consultas do Gremlin

1. Para carregar o gráfico, edite `gremlin-spark.properties` para definir `graphReader` como `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Carregue o gráfico, percorra os dados e execute consultas do Gremlin com ele fazendo o seguinte:

    a. Inicie o Console do Gremlin `bin/gremlin.sh`.

    b. Crie um gráfico com as configurações`graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Crie uma passagem de gráfico com SparkGraphComputer`g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Execute as seguintes consultas de gráfico do Gremlin:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Para ver o log mais detalhado, defina o log de nível em `conf/log4j-console.properties` como um nível mais detalhado.
>

## <a name="next-steps"></a>Próximas etapas

Este artigo de início rápido, você aprendeu como trabalhar com gráficos, combinando o banco de dados do Azure Cosmos e Spark.

> [!div class="nextstepaction"]
