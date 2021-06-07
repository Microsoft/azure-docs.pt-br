---
title: Solucionar problemas do Storm usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre o uso do Apache Storm com o Azure HDInsight.
keywords: Azure HDInsight, Storm, perguntas frequentes, guia de solução de problemas, problemas comuns
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: c81084c77b355a5d60c72564c58a98e08da14312
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946302"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Solucionar problemas do Apache Storm usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas resoluções para trabalhar com cargas de [Apache Storm](https://storm.apache.org/) no [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como fazer para acessar a interface do usuário do Storm em um cluster?

Você tem duas opções para acessar a interface do usuário do Storm em um navegador:

### <a name="apache-ambari-ui"></a>Interface do usuário do Apache Ambari

1. Vá até o painel do Ambari.
2. Na lista de serviços, selecione **Storm**.
3. No menu **Links Rápidos**, selecione **Interface do Usuário do Storm**.

### <a name="direct-link"></a>Link direto

Você pode acessar a interface do usuário do Storm na seguinte URL:

`https://<cluster DNS name>/stormui`

Exemplo: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como fazer para transferir informações de ponto de verificação de spout no hub de eventos Storm de uma topologia para outra?

Quando desenvolve topologias que leem os Hubs de Eventos do Azure usando o arquivo .jar do spout do hub de eventos Storm no HDInsight, você precisa implantar uma topologia que tenha o mesmo nome em um novo cluster. No entanto, você deve reter os dados do ponto de verificação que foram confirmados para [Apache ZooKeeper](https://zookeeper.apache.org/) no cluster antigo.

### <a name="where-checkpoint-data-is-stored"></a>O local em que os dados de ponto de verificação são armazenados

Os dados de ponto de verificação para deslocamentos são armazenados pelo spout do hub de eventos no ZooKeeper em dois caminhos raiz:

- Pontos de verificação de Spout não transacionais são armazenados em `/eventhubspout` .

- Os dados transacionais do ponto de verificação Spout são armazenados em `/transactional` .

### <a name="how-to-restore"></a>Como restaurar

Para fazer com que os scripts e bibliotecas que você usa exportem dados para fora do ZooKeeper e, em seguida, importar os dados de volta para o ZooKeeper com um novo nome, consulte [Exemplos de Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta lib tem arquivos .jar que contêm a implementação para a operação de exportação/importação. A pasta bash tem um script de exemplo que demonstra como exportar dados do servidor do ZooKeeper no cluster antigo e importá-los de volta para o servidor do ZooKeeper no novo cluster.

Execute o script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) nos nós do ZooKeeper para exportar e, em seguida, importar dados. Atualize o script para a versão correta da HDP (Hortonworks Data Platform). (Estamos trabalhando para tornar esses scripts genéricos no HDInsight. Scripts genéricos podem ser executados em qualquer nó no cluster sem modificações pelo usuário.)

O comando de exportação grava os metadados em um caminho de HDFS (Sistema de Arquivos Distribuído) do Apache Hadoop (no repositório do Azure Blob Storage ou Azure Data Lake Storage) em um local que você definir.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Exportar metadados de deslocamento

1. Use SSH para ir até o cluster do ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser exportado.
2. Execute o comando a seguir (depois de atualizar a cadeia de caracteres de versão do HDP) para exportar os dados de deslocamento ZooKeeper para o `/stormmetadta/zkdata` caminho HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importar metadados de deslocamento

1. Use SSH para ir até o cluster do ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser importado.
2. Execute o comando a seguir (depois de atualizar a cadeia de caracteres de versão do HDP) para importar os dados de deslocamento ZooKeeper do caminho HDFS `/stormmetadata/zkdata` para o servidor ZooKeeper no cluster de destino:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Exclua metadados de deslocamento para que as topologias possam começar a processar dados desde o início ou desde um carimbo de data/hora escolhido pelo usuário

1. Use SSH para ir até o cluster do ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser excluído.
2. Execute o comando a seguir (depois de atualizar a cadeia de caracteres de versão do HDP) para excluir todos os dados de deslocamento do ZooKeeper no cluster atual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como fazer para localizar binários do Storm em um cluster?

Os binários do Storm para a pilha HDP atual estão em `/usr/hdp/current/storm-client` . O local é o mesmo para nós de cabeçalho e para nós de trabalho.

Pode haver vários binários para versões específicas do HDP em/usr/HDP (por exemplo, `/usr/hdp/2.5.0.1233/storm` ). A `/usr/hdp/current/storm-client` pasta é symlinked para a versão mais recente que está em execução no cluster.

Para obter mais informações, consulte [Conectar um cluster HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) e [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como fazer para determinar a topologia de implantação de um cluster do Storm?

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

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Como fazer para localizar binários de spout do hub de eventos Storm para desenvolvimento?

Para obter mais informações sobre como usar arquivos .jar de spout do hub de eventos do Storm com sua topologia, consulte os seguintes recursos.

### <a name="java-based-topology"></a>Topologia baseada em Java

[Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia baseada em C# (Mono em clusters Storm Linux no HDInsight 3.4+)

[Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Os mais recentes binários do spout do hub de eventos do Apache Storm para clusters do HDInsight 3.5+ Linux Storm

Para saber como usar o Spout do hub de eventos Storm mais recente que funciona com clusters Storm do HDInsight 3.5 + Linux, consulte o [arquivo Leiame do MVN-repositório](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Exemplos de código-fonte

Veja [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) de ler e gravar no Hub de Eventos do Azure usando uma topologia do Apache Storm (escrita em Java) em um cluster do Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Como fazer para localizar arquivos de configuração do Storm Log4J 2 em clusters?

Para identificar os arquivos de configuração do [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para os serviços do Storm.

### <a name="on-head-nodes"></a>Em nós de cabeçalho

A configuração de Nimbus Log4J é lida de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

### <a name="on-worker-nodes"></a>Em nós de trabalho

A configuração de Log4J do supervisor é lida de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

O arquivo de configuração do Log4J de trabalho é lido de `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` .

Disso `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Não é uma exceção de líder

Ao enviar uma topologia, o usuário pode receber uma mensagem de erro semelhante a: `Topology submission exception, cause not a leader, the current leader is NimbusInfo` .

Para resolver, o usuário pode precisar arquivar um tíquete para que os nós sejam reiniciados/reinicializados. Para obter mais informações, confira [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).