---
title: Exceções de OutOfMemoryError para Apache Spark no Azure HDInsight
description: Várias exceções OutOfMemoryError para o cluster Apache Spark no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: a15d79f2ae9c3d20a73ec557c57a5c189b18111b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946348"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Exceções de OutOfMemoryError para Apache Spark no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Cenário: exceção OutOfMemoryError para Apache Spark

### <a name="issue"></a>Problema

O aplicativo Apache Spark falhou com uma exceção sem tratamento OutOfMemoryError. Você pode receber uma mensagem de erro semelhante a:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Causa

A causa mais provável dessa exceção é que não há memória de heap suficiente alocada para as JVMs (máquinas virtuais Java). Esses JVMs são iniciados como executores ou drivers como parte do aplicativo Apache Spark.

### <a name="resolution"></a>Resolução

1. Determine o tamanho máximo dos dados com os quais o aplicativo Spark lidará. Faça uma estimativa do tamanho com base no máximo do tamanho dos dados de entrada, os dados intermediários produzidos transformando os dados de entrada e os dados de saída produzidos mais transformando os dados intermediários. Se a estimativa inicial não for suficiente, aumente o tamanho ligeiramente e itere até que os erros de memória sejam sublados.

1. Verifique se o cluster HDInsight a ser usado tem recursos suficientes em termos de memória e também núcleos para aceitar o aplicativo Spark. Isso pode ser determinado exibindo a seção métricas de cluster da interface do usuário do amYARN do cluster para os valores de **memória usados** versus **total de memória** e **VCores usados** versus o **total de VCores**.

    ![exibição de memória do yarn Core](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Defina as seguintes configurações do Spark com os valores apropriados. Equilibre os requisitos do aplicativo com os recursos disponíveis no cluster. Esses valores não devem exceder 90% da memória e dos núcleos disponíveis, conforme exibido pelo YARN, e também devem atender ao requisito mínimo de memória do aplicativo Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Memória total usada por todos os executores =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Memória total usada pelo driver =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Cenário: erro de espaço de heap de Java ao tentar abrir o servidor de histórico de Apache Spark

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao abrir eventos no servidor de histórico do Spark:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Causa

Esse problema geralmente é causado por uma falta de recursos ao abrir grandes arquivos Spark-Event. O tamanho do heap do Spark é definido como 1 GB por padrão, mas grandes arquivos de eventos do Spark podem exigir mais do que isso.

Se você quiser verificar o tamanho dos arquivos que está tentando carregar, poderá executar os seguintes comandos:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Resolução

Você pode aumentar a memória do servidor de histórico do Spark editando a `SPARK_DAEMON_MEMORY` Propriedade na configuração do Spark e reiniciando todos os serviços.

Você pode fazer isso de dentro da interface do usuário do navegador do Ambari selecionando a seção Spark2/config/Advanced Spark2-env.

![Seção spark2-env avançada](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Adicione a seguinte propriedade para alterar a memória do servidor de histórico do Spark de 1g para 4G: `SPARK_DAEMON_MEMORY=4g` .

![Propriedade do Spark](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Certifique-se de reiniciar todos os serviços afetados do Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Cenário: falha na inicialização do servidor Livy no cluster Apache Spark

### <a name="issue"></a>Problema

O servidor Livy não pode ser iniciado em um Apache Spark [(Spark 2,1 no Linux (HDI 3,6)]. A tentativa de reiniciar os resultados na seguinte pilha de erros, dos logs do Livy:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Causa

`java.lang.OutOfMemoryError: unable to create new native thread` OS destaques do sistema operacional não podem atribuir mais threads nativos a JVMs. Confirmado que essa exceção é causada pela violação do limite de contagem de thread por processo.

Quando o Livy Server termina inesperadamente, todas as conexões com clusters Spark também são encerradas, o que significa que todos os trabalhos e dados relacionados serão perdidos. No mecanismo de recuperação de sessão do HDP 2,6 foi introduzido, o Livy armazena os detalhes da sessão em Zookeeper a serem recuperados depois que o servidor de Livy estiver de volta.

Quando um grande número de trabalhos é enviado via Livy, como parte da alta disponibilidade do Livy Server armazena esses Estados de sessão no ZK (em clusters HDInsight) e recupera essas sessões quando o serviço do Livy é reiniciado. Ao reiniciar após o encerramento inesperado, o Livy cria um thread por sessão e acumula um determinado número de sessões a serem recuperadas, causando o excesso de threads sendo criados.

### <a name="resolution"></a>Resolução

Exclua todas as entradas usando as etapas detalhadas abaixo.

1. Obter o endereço IP dos nós Zookeeper usando

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. O comando acima listou todos os zookeepers para o meu cluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Obtenha todo o endereço IP dos nós Zookeeper usando ping ou você também pode se conectar ao Zookeeper no cabeçalho usando o nome do ZK

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Quando você estiver conectado ao Zookeeper, execute o comando a seguir para listar todas as sessões que foram tentadas de reiniciar.

    1. A maioria dos casos pode ser uma lista com mais de 8000 sessões ####

        ```bash
        ls /livy/v1/batch
        ```

    1. O comando a seguir é remover todas as sessões a serem recuperadas. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Aguarde até que o comando acima seja concluído e o cursor para retornar o prompt e reinicie o serviço Livy de Ambari, que deve ter sucesso.

> [!NOTE]
> `DELETE` a sessão Livy depois de concluir sua execução. As sessões do lote Livy não serão excluídas automaticamente assim que o aplicativo Spark for concluído, o que é por design. Uma sessão Livy é uma entidade criada por uma solicitação POST em relação ao servidor REST Livy. `DELETE`É necessária uma chamada para excluir essa entidade. Ou devemos aguardar até que o GC seja ativado.

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* [Visão geral do gerenciamento de memória do Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Depurando o aplicativo Spark em clusters HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).