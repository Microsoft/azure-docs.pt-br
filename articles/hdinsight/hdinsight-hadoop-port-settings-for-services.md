---
title: "Portas usadas por serviços de Hadoop no HDInsight – Azure | Microsoft Docs"
description: "Uma lista de portas usadas pelos serviços do Hadoop em execução no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd14aed9-ec25-4bb3-a20c-e29562735a7d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/23/2017
ms.author: larryfr
ms.openlocfilehash: 1ad536a53d64b0144f6396393830bc0c5cbe4fb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="ports-used-by-hadoop-services-on-hdinsight"></a>Portas usadas pelos serviços do Hadoop em execução no HDInsight

Este documento fornece uma lista das portas usadas pelos serviços do Hadoop em execução em clusters HDInsight baseados em Linux. Ele também fornece informações sobre portas usadas para se conectar ao cluster usando SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portas públicas versus portas não públicas

Os clusters HDInsight baseados em Linux expõem apenas três portas publicamente na Internet: 22, 23 e 443. Essas portas são usadas para acessar com segurança o cluster com SSH e serviços expostos pelo protocolo HTTPS seguro.

Internamente, o HDInsight é implementado por várias Máquinas Virtuais do Azure (os nós no cluster) em execução em uma Rede Virtual do Azure. Dentro da rede virtual, você pode acessar portas não expostas pela Internet. Por exemplo, se você se conectar a um dos nós de cabeçalho usando SSH, do nó de cabeçalho, é possível acessar diretamente os serviços em execução nos nós de cluster.

> [!IMPORTANT]
> Se você não especificar uma Rede Virtual do Azure como uma opção de configuração para o HDInsight, uma será criada automaticamente. No entanto, não é possível ingressar outras máquinas (como outras Máquinas Virtuais do Azure ou seu computador de desenvolvimento do cliente) nessa rede virtual.


Para ingressar mais computadores na rede virtual, primeiramente é preciso criar a rede virtual e depois especificá-la na criação do cluster HDInsight. Para saber mais, confira [Estender os recursos do HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Portas públicas

Todos os nós em um cluster HDInsight estão localizados em uma Rede Virtual do Azure e não podem ser acessados diretamente da internet. Um gateway público fornece acesso à internet para as portas a seguir, que são comuns a todos os tipos de cluster HDInsight.

| O Barramento de | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| sshd |22 |SSH |Conecta os clientes a sshd no nó de cabeçalho primário. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Conecta clientes ao sshd no nó de borda. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Conecta os clientes a sshd no nó de cabeçalho secundário. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interface de usuário da Web do Ambari. Confira [Gerenciar clusters HDInsight usando a interface de usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API REST do Ambari. Confira [Gerenciar clusters HDInsight usando a API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API REST do HCatalog. Confira [Usar Hive com Curl](hdinsight-hadoop-use-pig-curl.md), [Usar Pig com Curl](hdinsight-hadoop-use-pig-curl.md), [Usar MapReduce com Curl](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Conecta-se ao Hive usando ODBC. Confira [Connect Excel to HDInsight with the Microsoft ODBC driver (Conectar o Excel ao HDInsight com o driver ODBC da Microsoft)](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Conecta-se ao Hive usando JDBC. Confira [Conectar-se ao Hive no Azure HDInsight usando o driver JDBC do Hive](hdinsight-connect-hive-jdbc-driver.md) |

Veja a seguir os tipos de cluster específicos que estão disponíveis:

| O Barramento de | Port | Protocolo | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |API REST do HBase. Confira [Introdução ao HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API REST do Spark. Confira [Enviar trabalhos do Spark remotamente usando o Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Storm |443 |HTTPS |Storm |Interface de usuário do Storm para Web. Confira [Implantar e gerenciar topologias Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Autenticação

Todos os serviços publicamente expostos na Internet devem ser autenticados:

| Port | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de usuário SSH especificadas durante a criação do cluster |
| 443 |O nome de logon (padrão: admin) e a senha que foram definidos durante a criação do cluster |

## <a name="non-public-ports"></a>Portas não públicas

> [!NOTE]
> Alguns serviços só estão disponíveis em tipos de cluster específicos. Por exemplo, HBase só está disponível em tipos de cluster HBase.

> [!IMPORTANT]
> Alguns serviços são executados somente em um nó de cabeçalho por vez. Se você tentar se conectar ao serviço em um nó de cabeçalho primário e receber um erro 404, tente novamente usando o nó de cabeçalho secundário.

### <a name="ambari"></a>Ambari

| O Barramento de | Nós | Porta | Caminho da URL | Protocolo | 
| --- | --- | --- | --- | --- |
| Interface do usuário da Web do Ambari | Nós de cabeçalho | 8080 | / | HTTP |
| API REST do Ambari | Nós de cabeçalho | 8080 | /api/v1 | HTTP |

Exemplos:

* API REST do Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Portas HDFS

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface de usuário do NameNode na Web |Nós de cabeçalho |30070 |HTTPS |Interface do usuário na Web para exibir status |
| Serviço de metadados NameNode |Nós de cabeçalho |8020 |IPC |Metadados do sistema de arquivos |
| DataNode |Todos os nós de trabalho |30075 |HTTPS |Interface de usuário na Web para exibir status, logs, etc. |
| DataNode |Todos os nós de trabalho |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós de trabalho |30020 |IPC |Operações de metadados |
| NameNode secundário |Nós de cabeçalho |50090 |HTTP |Ponto de verificação para metadados do NameNode |

### <a name="yarn-ports"></a>Portas YARN

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface de usuário na Web do Resource Manager |Nós de cabeçalho |8088 |HTTP |Interface de usuário na Web do Resource Manager |
| Interface de usuário na Web do Resource Manager |Nós de cabeçalho |8090 |HTTPS |Interface de usuário na Web do Resource Manager |
| Interface administrativa do Resource Manager |Nós de cabeçalho |8141 |IPC |Para envios de aplicativo (Hive, servidor Hive, Pig, etc.) |
| Agendador do Resource Manager |Nós de cabeçalho |8030 |HTTP |Interface administrativa |
| Interface de aplicativo do Resource Manager |Nós de cabeçalho |8050 |HTTP |Endereço da interface do gerenciador de aplicativos |
| NodeManager |Todos os nós de trabalho |30050 |&nbsp; |O endereço do gerenciador de contêineres |
| Interface de usuário na Web do NodeManager |Todos os nós de trabalho |30060 |HTTP |Interface do Resource Manager |
| Endereço do Timeline |Nós de cabeçalho |10200 |RPC |O serviço RPC do serviço do Timeline. |
| Interface de usuário na Web do Timeline |Nós de cabeçalho |8181 |HTTP |Interface de usuário na Web do Timeline |

### <a name="hive-ports"></a>Portas Hive

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nós de cabeçalho |10001 |Thrift |Serviço para conexão ao Hive (Thrift/JDBC) |
| Metastore do Hive |Nós de cabeçalho |9083 |Thrift |Serviço para conexão a metadados do Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Portas WebHCat

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor WebHCat |Nós de cabeçalho |30111 |HTTP |API Web sobre o HCatalog e outros serviços do Hadoop |

### <a name="mapreduce-ports"></a>Portas MapReduce

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| JobHistory |Nós de cabeçalho |19888 |HTTP |Interface de usuário na Web JobHistory do MapReduce |
| JobHistory |Nós de cabeçalho |10020 |&nbsp; |Servidor JobHistory do MapReduce |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfere saídas intermediárias Map para redutores de solicitação |

### <a name="oozie"></a>Oozie

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor Oozie |Nós de cabeçalho |11000 |HTTP |URL para o serviço do Oozie |
| Servidor Oozie |Nós de cabeçalho |11001 |HTTP |Porta para administração do Oozie |

### <a name="ambari-metrics"></a>Métricas do Ambari

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| TimeLine (histórico do aplicativo) |Nós de cabeçalho |6188 |HTTP |Interface de usuário na Web do Timeline |
| TimeLine (histórico do aplicativo) |Nós de cabeçalho |30200 |RPC |Interface de usuário na Web do Timeline |

### <a name="hbase-ports"></a>Portas HBase

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nós de cabeçalho |16000 |&nbsp; |&nbsp; |
| Interface de usuário na Web para informações do HMaster |Nós de cabeçalho |16010 |HTTP |A porta para a interface de usuário na Web Mestre do HBase |
| Servidor de região |Todos os nós de trabalho |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes usam para se conectar ao ZooKeeper |

### <a name="kafka-ports"></a>Portas Kafka

| O Barramento de | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Agente |Nós de trabalho |9092 |[Protocolo Kafka Wire](http://kafka.apache.org/protocol.html) |Usado para comunicação do cliente |
| &nbsp; |Nós do Zookeeper |2181 |&nbsp; |A porta que os clientes usam para se conectar ao ZooKeeper |

### <a name="spark-ports"></a>Portas do Spark

| O Barramento de | Nós | Port | Protocolo | Caminho da URL | Descrição |
| --- | --- | --- | --- | --- | --- |
| Servidores Thrift Spark |Nós de cabeçalho |10002 |Thrift | &nbsp; | Serviço para conexão ao Spark SQL (Thrift/JDBC) |
| Servidor Livy | Nós de cabeçalho | 8998 | HTTP | /batches | Serviço para executar instruções, trabalhos e aplicativos |

Exemplos:

* Livy: `curl "http://10.0.0.11:8998/batches"`. Nesse exemplo, `10.0.0.11` é o endereço IP do nó de cabeçalho que hospeda o serviço Livy.