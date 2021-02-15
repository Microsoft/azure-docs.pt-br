---
title: Portas usadas pelos serviços do Hadoop em execução no Azure HDInsight
description: Este artigo fornece uma lista de portas usadas por Apache Hadoop serviços em execução no Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b5dadb886078f701bb01447efd606c862ee73073
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945534"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portas usadas pelos serviços do Apache Hadoop em execução no HDInsight

Este documento fornece uma lista das portas usadas por Apache Hadoop serviços em execução em clusters HDInsight. Ele também fornece informações sobre portas usadas para se conectar ao cluster usando SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portas públicas versus portas não públicas

Os clusters HDInsight baseados em Linux expõem apenas três portas publicamente na Internet: 22, 23 e 443. Essas portas protegem o acesso ao cluster usando SSH e serviços expostos por meio do protocolo HTTPS seguro.

O HDInsight é implementado por várias máquinas virtuais do Azure (nós de cluster) em execução em uma rede virtual do Azure. Dentro da rede virtual, você pode acessar portas não expostas pela Internet. Se você se conectar via SSH ao nó principal, poderá acessar diretamente os serviços em execução nos nós do cluster.

> [!IMPORTANT]  
> Se você não especificar uma Rede Virtual do Azure como uma opção de configuração para o HDInsight, uma será criada automaticamente. No entanto, você não pode unir outros computadores (como outras máquinas virtuais do Azure ou seu computador de desenvolvimento de cliente) a essa rede virtual.

Para ingressar mais computadores na rede virtual, primeiramente é preciso criar a rede virtual e depois especificá-la na criação do cluster HDInsight. Para obter mais informações, consulte [planejar uma rede virtual para o HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Portas públicas

Todos os nós em um cluster HDInsight estão localizados em uma rede virtual do Azure. Os nós não podem ser acessados diretamente da Internet. Um gateway público fornece acesso à internet para as portas a seguir, que são comuns a todos os tipos de cluster HDInsight.

| Serviço | Porta | Protocolo | Descrição |
| --- | --- | --- | --- |
| sshd |22 |SSH |Conecta os clientes a sshd no nó de cabeçalho primário. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Conecta clientes ao sshd no nó de borda. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Conecta os clientes a sshd no nó de cabeçalho secundário. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interface de usuário da Web do Ambari. Veja [Gerenciar HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API REST do Ambari. Veja [Gerenciar HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API REST do HCatalog. Consulte  [usar o MapReduce com ondulação](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBCODBC |Conecta-se ao Hive usando ODBC. Confira [Connect Excel to HDInsight with the Microsoft ODBC driver (Conectar o Excel ao HDInsight com o driver ODBC da Microsoft)](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Conecta-se ao ApacheHive usando JDBC. Veja [Conectar-se ao Apache Hive no Azure HDInsight usando o driver JDBC do Hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Veja a seguir os tipos de cluster específicos que estão disponíveis:

| Serviço | Porta | Protocolo | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |HBase |API REST do HBase. Veja [Começar a usar o Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API REST do Spark. Veja [Enviar Apache trabalhos em Spark remotamente usando Livy Apache](spark/apache-spark-livy-rest-interface.md) |
| Servidor do Spark Thrift |443 |HTTPS |Spark |Servidor do Spark Thrift usado para enviar consultas do Hive. Veja [Usar o Beeline com o Apache Hive no HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Interface de usuário do Storm para Web. Veja [Implantar e gerenciar topologias Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Proxy REST do Kafka |443 |HTTPS |Kafka |API REST do Kafka. Confira [interagir com clusters Apache Kafka no Azure HDInsight usando um proxy REST](kafka/rest-proxy.md) |

### <a name="authentication"></a>Autenticação

Todos os serviços publicamente expostos na Internet devem ser autenticados:

| Porta | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de usuário SSH especificadas durante a criação do cluster |
| 443 |O nome de logon (padrão: admin) e a senha que foram definidos durante a criação do cluster |

## <a name="non-public-ports"></a>Portas não públicas

> [!NOTE]  
> Alguns serviços só estão disponíveis em tipos de cluster específicos. Por exemplo, HBase só está disponível em tipos de cluster HBase.

> [!IMPORTANT]  
> Alguns serviços são executados somente em um nó de cabeçalho por vez. Se você tentar se conectar ao serviço em um nó de cabeçalho primário e receber um erro, tente novamente usando o nó de cabeçalho secundário.

### <a name="ambari"></a>Ambari

| Serviço | Nós | Porta | Caminho da URL | Protocolo |
| --- | --- | --- | --- | --- |
| Interface do usuário da Web do Ambari | Nós de cabeçalho | 8080 | / | HTTP |
| API REST do Ambari | Nós de cabeçalho | 8080 | /api/v1 | HTTP |

Exemplos:

* API REST do Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Portas HDFS

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface de usuário do NameNode na Web |Nós de cabeçalho |30070 |HTTPS |Interface do usuário na Web para exibir status |
| Serviço de metadados NameNode |Nós de cabeçalho |8020 |IPC |Metadados do sistema de arquivos |
| DataNode |Todos os nós de trabalho |30075 |HTTPS |Interface do usuário da Web para exibir status, logs e assim por diante. |
| DataNode |Todos os nós de trabalho |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós de trabalho |30020 |IPC |Operações de metadados |
| NameNode secundário |Nós de cabeçalho |50090 |HTTP |Ponto de verificação para metadados do NameNode |

### <a name="yarn-ports"></a>Portas YARN

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface de usuário na Web do Resource Manager |Nós de cabeçalho |8088 |HTTP |Interface de usuário na Web do Resource Manager |
| Interface de usuário na Web do Resource Manager |Nós de cabeçalho |8090 |HTTPS |Interface de usuário na Web do Resource Manager |
| Interface administrativa do Resource Manager |Nós de cabeçalho |8141 |IPC |Para envios de aplicativos (Hive, servidor Hive, Pig e assim por diante). |
| Agendador do Resource Manager |Nós de cabeçalho |8030 |HTTP |Interface administrativa |
| Interface de aplicativo do Resource Manager |Nós de cabeçalho |8050 |HTTP |Endereço da interface do gerenciador de aplicativos |
| NodeManager |Todos os nós de trabalho |30050 |&nbsp; |O endereço do gerenciador de contêineres |
| Interface de usuário na Web do NodeManager |Todos os nós de trabalho |30060 |HTTP |Interface do Gerenciador de recursos |
| Endereço do Timeline |Nós de cabeçalho |10200 |RPC |O serviço RPC do serviço do Timeline. |
| Interface de usuário na Web do Timeline |Nós de cabeçalho |8188 |HTTP |Interface de usuário na Web do Timeline |

### <a name="hive-ports"></a>Portas Hive

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nós de cabeçalho |10001 |Thrift |Serviço para conexão ao Hive (Thrift/JDBC) |
| Metastore do Hive |Nós de cabeçalho |9083 |Thrift |Serviço para conexão a metadados do Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Portas WebHCat

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor WebHCat |Nós de cabeçalho |30111 |HTTP |API Web sobre o HCatalog e outros serviços do Hadoop |

### <a name="mapreduce-ports"></a>Portas MapReduce

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| JobHistory |Nós de cabeçalho |19888 |HTTP |Interface de usuário na Web JobHistory do MapReduce |
| JobHistory |Nós de cabeçalho |10020 |&nbsp; |Servidor JobHistory do MapReduce |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfere saídas intermediárias Map para redutores de solicitação |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor Oozie |Nós de cabeçalho |11000 |HTTP |URL para o serviço do Oozie |
| Servidor Oozie |Nós de cabeçalho |11001 |HTTP |Porta para administração do Oozie |

### <a name="ambari-metrics"></a>Métricas do Ambari

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| TimeLine (histórico do aplicativo) |Nós de cabeçalho |6188 |HTTP |Interface de usuário na Web do Timeline |
| TimeLine (histórico do aplicativo) |Nós de cabeçalho |30200 |RPC |Interface de usuário na Web do Timeline |

### <a name="hbase-ports"></a>Portas HBase

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nós de cabeçalho |16000 |&nbsp; |&nbsp; |
| Interface de usuário na Web para informações do HMaster |Nós de cabeçalho |16010 |HTTP |A porta para a interface de usuário na Web Mestre do HBase |
| Servidor de região |Todos os nós de trabalho |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes usam para se conectar ao ZooKeeper |

### <a name="kafka-ports"></a>Portas Kafka

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Agente |Nós de trabalho |9092 |Protocolo Kafka Wire |Usado para comunicação do cliente |
| &nbsp; |Nós do Zookeeper |2181 |&nbsp; |A porta que os clientes usam para se conectar ao ZooKeeper |
| Proxy REST | Nós de gerenciamento do Kafka |9400 |HTTPS |[Especificação REST do Kafka](/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Portas do Spark

| Serviço | Nós | Porta | Protocolo | Caminho da URL | Descrição |
| --- | --- | --- | --- | --- | --- |
| Servidores Thrift Spark |Nós de cabeçalho |10002 |Thrift | &nbsp; | Serviço para conexão ao Spark SQL (Thrift/JDBC) |
| Servidor Livy | Nós de cabeçalho | 8998 | HTTP | &nbsp; | Serviço para executar instruções, trabalhos e aplicativos |
| Jupyter Notebook | Nós de cabeçalho | 8001 | HTTP | &nbsp; | Jupyter Notebook site |

Exemplos:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. Nesse exemplo, `10.0.0.11` é o endereço IP do nó de cabeçalho que hospeda o serviço Livy.