---
title: Acessar Apache Hadoop logs de aplicativo do YARN-Azure HDInsight
description: Saiba como acessar os logs do aplicativo YARN em um cluster HDInsight (Apache Hadoop) baseado em Linux usando a linha de comando e um navegador da web.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 9558e6c5ddd58b1d5fd70da03187caef50d1275d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865563"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Acessar logs do aplicativo Apache Hadoop YARN no HDInsight baseado em Linux

Saiba como acessar os logs para os aplicativos [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Ainda Outro Negociador de Recursos) em um cluster do Apache Hadoop no Azure HDInsight.

## <a name="what-is-apache-yarn"></a>O que é o Apache YARN?

O YARN dá suporte a vários modelos de programação (Apache Hadoop MapReduce sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um *`ResourceManager`* mecanismo global (RM), *NodeManagers* (NMS) por meio do nó de trabalho, e o AMS ( *Gerenciador* por aplicativo). O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade de trabalho básica feita por um aplicativo YARN. Todo o trabalho feito dentro do contexto de um contêiner é feito no nó de trabalho único no qual o contêiner foi fornecido. Consulte [Hadoop: Writing yarn Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)ou [Apache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) para referência adicional.

Para dimensionar o cluster para dar suporte a uma taxa de transferência de processamento maior, você pode usar o [dimensionamento automático](hdinsight-autoscale-clusters.md) ou [dimensionar seus clusters manualmente usando alguns idiomas diferentes](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>Servidor de linha do tempo do YARN

O [Servidor de Linha de Tempo do Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos

O YARN Timeline Server inclui o seguinte tipo de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre as tentativas feitas para concluir o aplicativo
* Os contêineres usados por uma determinada tentativa de aplicativo

## <a name="yarn-applications-and-logs"></a>Aplicativos e logs YARN

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma boa estrutura para coletar, agregar e armazenar logs de aplicativos com a agregação de logs.

O recurso de agregação de logs permite acessar logs de aplicativos mais determinista. Ele agrega logs em todos os contêineres em um nó de trabalho e as armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre são agregados a um único arquivo. Portanto, há apenas um log por nó de trabalho usado pelo seu aplicativo. A Agregação de Log é habilitada por padrão em clusters HDInsight versão 3.0 e superior. Logs agregados estão localizados no armazenamento padrão do cluster. O caminho a seguir é o caminho do HDFS para os logs:

```
/app-logs/<user>/logs/<applicationId>
```

No caminho, `user` é o nome do usuário que iniciou o aplicativo. O `applicationId` é o identificador exclusivo atribuído a um aplicativo pelo RM do YARN.

Os logs agregados não são diretamente legíveis, pois são gravados em um formato TFile, binário indexado pelo contêiner. Use os logs do YARN `ResourceManager` ou as ferramentas da CLI para exibir esses logs como texto sem formatação para aplicativos ou contêineres de interesse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Yarn logs em um cluster ESP

Duas configurações devem ser adicionadas ao personalizado `mapred-site` no Ambari.

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Na interface do usuário do amAmbari, navegue até **MapReduce2**  >  **configurações**  >  **avançadas**  >  **personalizadas mapred-site**.

1. Adicione *um* dos seguintes conjuntos de propriedades:

    **Conjunto 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Conjunto 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Salve as alterações e reinicie todos os serviços afetados.

## <a name="yarn-cli-tools"></a>Ferramentas CLI do YARN

1. Use o [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Liste todas as IDs de aplicativo dos aplicativos Yarn em execução no momento com o seguinte comando:

    ```bash
    yarn top
    ```

    Observe a ID do aplicativo da `APPLICATIONID` coluna cujos logs devem ser baixados.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. É possível exibir esses logs como texto sem formatação, executando um dos seguintes comandos:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Especifique as informações &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> e &lt;worker-node-address> ao executar esses comandos.

### <a name="other-sample-commands"></a>Outros comandos de exemplo

1. Baixe os logs de contêineres do yarn para todos os mestres de aplicativo com o comando a seguir. Esta etapa criará o arquivo de log chamado `amlogs.txt` em formato de texto.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Baixe os logs do contêiner do Yarn apenas para o mestre aplicativo mais recente com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Baixe os logs do contêiner do YARN para os dois primeiros mestres de aplicativo com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Baixe todos os logs do contêiner do Yarn com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Baixe o log do contêiner yarn para um contêiner específico com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>`ResourceManager`interface do usuário do AMYARN

A `ResourceManager` interface do usuário do yarn é executada no cabeçalho do cluster. Ele é acessado por meio da interface do usuário do amAmbari Web. Execute as etapas a seguir para exibir os logs do YARN:

1. No navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME com o nome do cluster HDInsight.

2. Na lista de serviços à esquerda, selecione **yarn**.

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png" alt-text="Serviço yarn do Apache Ambari selecionado":::

3. Na lista suspensa **links rápidos** , selecione um dos nós de cabeçalho do cluster e, em seguida, selecione **`ResourceManager Log`** .

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png" alt-text="Links rápidos do Apache Ambari yarn":::

    Você verá uma lista de links para logs do YARN.

## <a name="next-steps"></a>Próximas etapas

* [Arquitetura do Apache Hadoop no HDInsight](hdinsight-hadoop-architecture.md)
* [Solucione problemas de YARN do Apache Hadoop usando o Azure HDInsight](hdinsight-troubleshoot-yarn.md)
