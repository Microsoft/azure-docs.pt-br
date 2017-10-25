---
title: Solucionar problemas do Storm usando o Azure HDInsight | Microsoft Docs
description: Obtenha respostas para perguntas comuns sobre o uso do Apache Storm com o Azure HDInsight.
keywords: "Azure HDInsight, Storm, perguntas frequentes, guia de solução de problemas, problemas comuns"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.openlocfilehash: 70a3d762431d90acdd6ed2a432a569f34d0ce447
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Solucionar problemas do Storm usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas soluções para trabalhar com cargas de Apache Storm no Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como fazer para acessar a interface do usuário do Storm em um cluster
Você tem duas opções para acessar a interface do usuário do Storm em um navegador:

### <a name="ambari-ui"></a>Interface do usuário do Ambari
1. Vá até o painel do Ambari.
2. Na lista de serviços, selecione **Storm**.
3. No menu **Links Rápidos**, selecione **Interface do Usuário do Storm**.

### <a name="direct-link"></a>Link direto
Você pode acessar a interface do usuário do Storm na seguinte URL:

https://\<nome DNS do cluster\>/stormui

Exemplo:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como fazer para transferir informações de ponto de verificação de spout no hub de eventos Storm de uma topologia para outra

Quando desenvolve topologias que leem os Hubs de Eventos do Azure usando o arquivo .jar do spout do hub de eventos Storm no HDInsight, você precisa implantar uma topologia que tenha o mesmo nome em um novo cluster. No entanto, você precisa reter os dados de ponto de verificação que foram confirmados no ZooKeeper do Apache no cluster antigo.

### <a name="where-checkpoint-data-is-stored"></a>O local em que os dados de ponto de verificação são armazenados
Os dados de ponto de verificação para deslocamentos são armazenados pelo spout do hub de eventos no ZooKeeper em dois caminhos raiz:
- Pontos de verificação de spout não transacionais são armazenados em /eventhubspout.
- Dados de pontos de verificação de spout transacionais são armazenados em /transactional.

### <a name="how-to-restore"></a>Como restaurar
Para fazer com que os scripts e bibliotecas que você usa exportem dados para fora do ZooKeeper e, em seguida, importar os dados de volta para o ZooKeeper com um novo nome, consulte [Exemplos de Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta lib tem arquivos .jar que contêm a implementação para a operação de exportação/importação. A pasta bash tem um script de exemplo que demonstra como exportar dados do servidor do ZooKeeper no cluster antigo e importá-los de volta para o servidor do ZooKeeper no novo cluster.

Execute o script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) nos nós do ZooKeeper para exportar e, em seguida, importar dados. Atualize o script para a versão correta da HDP (Hortonworks Data Platform). (Estamos trabalhando para tornar esses scripts genéricos no HDInsight. Scripts genéricos podem ser executados em qualquer nó no cluster sem modificações pelo usuário.)

O comando de exportação grava os metadados em um caminho de HDFS (Sistema de Arquivos Distribuído) do Apache Hadoop (em um repositório do Armazenamento de Blobs do Azure ou do Azure Data Lake Store) em um local que você definir.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Exportar metadados de deslocamento
1. Use SSH para ir até o cluster do ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser exportado.
2. Execute o seguinte comando (depois de atualizar a cadeia de caracteres de versão do HDP) para exportar dados de deslocamento do ZooKeeper para o caminho do HDFS /stormmetadata/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importar metadados de deslocamento
1. Use SSH para ir até o cluster do ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser exportado.
2. Execute o seguinte comando (depois de atualizar a cadeia de caracteres de versão do HDP) para importar dados de deslocamento do ZooKeeper do caminho do HDFS /stormmetadata/zkdata para o servidor do ZooKeeper no cluster de destino:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Exclua metadados de deslocamento para que as topologias possam começar a processar dados desde o início ou desde um carimbo de data/hora escolhido pelo usuário
1. Use SSH para ir até o cluster do ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser exportado.
2. Execute o comando a seguir (depois de atualizar a cadeia de caracteres de versão do HDP) para excluir todos os dados de deslocamento do ZooKeeper no cluster atual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como fazer para localizar binários do Storm em um cluster
Binários do Storm para a pilha do HDP atual estão em /usr/hdp/current/storm-client. O local é o mesmo para nós de cabeçalho e para nós de trabalho.
 
Pode haver vários binários para versões específicas do HDP em /usr/hdp (por exemplo, /usr/hdp/2.5.0.1233/storm). A pasta /usr/hdp/current/storm-client está vinculada simbolicamente à versão mais recente em execução no cluster.

Para obter mais informações, consulte [Conectar-se a um cluster HDInsight usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como fazer para determinar a topologia de implantação de um cluster do Storm
Primeiro, identifique todos os componentes instalados com o HDInsight Storm. Um cluster do Storm consiste em quatro categorias de nó:

* Nós de gateway
* Nós de cabeçalho
* Nós do ZooKeeper
* Nós de trabalho
 
### <a name="gateway-nodes"></a>Nós de gateway
Um nó de gateway é um serviço de proxy reverso e de gateway que habilita o acesso público a um serviço de gerenciamento do Ambari ativo. Ele também lida com a seleção de líder do Ambari.
 
### <a name="head-nodes"></a>Nós de cabeçalho
Nós de cabeçalho do Storm executam os seguintes serviços:
* Nimbus
* Servidor Ambari
* Servidor de Métricas do Ambari
* Coletor de Métricas do Ambari
 
### <a name="zookeeper-nodes"></a>Nós do ZooKeeper
O HDInsight tem um quorum do ZooKeeper de três nós. O tamanho do quorum é fixo e não é configurável.
 
Serviços do Storm no cluster são configurados para usar o quorum do ZooKeeper automaticamente.
 
### <a name="worker-nodes"></a>Nós de trabalho
Nós de trabalho do Storm executam os seguintes serviços:
* Supervisor
* JVMs (máquinas virtuais Java) de trabalho, para executar topologias
* Agente do Ambari
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Como fazer para localizar binários de spout do hub de eventos Storm para desenvolvimento
 
Para obter mais informações sobre como usar arquivos .jar de spout do hub de eventos do Storm com sua topologia, consulte os seguintes recursos.
 
### <a name="java-based-topology"></a>Topologia baseada em Java
[Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia baseada em C# (Mono em clusters Storm Linux no HDInsight 3.4+)
[Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Binários de spout do hub de eventos Storm mais recentes para clusters Storm Linux no HDInsight 3.5+
Para saber como usar o spout de hub de eventos do Storm mais recente que funciona com clusters Storm Linux do HDInsight 3.5+, consulte o [arquivo Leiame](https://github.com/hdinsight/mvn-repo/blob/master/README.md) do repositório de mvn.
 
### <a name="source-code-examples"></a>Exemplos de código-fonte
Veja [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) de ler e gravar no Hub de Eventos do Azure usando uma topologia do Apache Storm (escrita em Java) em um cluster do Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Como fazer para localizar arquivos de configuração do Storm Log4J nos clusters
 
Para identificar arquivos de configuração do Apache Log4J para serviços do Storm.
 
### <a name="on-head-nodes"></a>Em nós de cabeçalho
A configuração de Log4J Nimbus é lida de /usr/hdp/\<versão de HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Em nós de trabalho
A configuração de Log4J de supervisor é lida de /usr/hdp/\<versão de HDP\>/storm/log4j2/cluster.xml.
 
O arquivo de configuração de Log4J de trabalho é lido de /usr/hdp/\<versão de HDP\>/storm/log4j2/worker.xml.
 
Exemplos: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

