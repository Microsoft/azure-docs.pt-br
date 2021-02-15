---
title: Dimensionar tamanhos de cluster – Azure HDInsight
description: Dimensionar um cluster Apache Hadoop de forma elástica para corresponder à sua carga de trabalho no Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 3524b5d2274c52aa94fa1c3420fb0d3245d9b730
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932058"
---
# <a name="scale-azure-hdinsight-clusters"></a>Dimensionar clusters do Azure HDInsight

O HDInsight fornece elasticidade com opções para escalar verticalmente e reduzir o número de nós de trabalho em seus clusters. Essa elasticidade permite que você reduza um cluster após horas ou em fins de semana. E expandi-lo durante as demandas de negócios de pico.

Escale verticalmente o cluster antes do processamento periódico em lote para que o cluster tenha recursos adequados.  Após a conclusão do processamento, e o uso ficar inativo, reduza verticalmente o cluster HDInsight para menos nós de trabalho.

Você pode dimensionar um cluster manualmente usando um dos métodos descritos abaixo. Você também pode usar opções de [dimensionamento automático](hdinsight-autoscale-clusters.md) para aumentar e reduzir automaticamente em resposta a determinadas métricas.

> [!NOTE]  
> Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades.

## <a name="utilities-to-scale-clusters"></a>Utilitários para dimensionar clusters

A Microsoft fornece os seguintes utilitários para dimensionar clusters:

|Utilitário | Descrição|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[AzureRM do PowerShell](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[CLI do Azure](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[CLI clássica do Azure](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure portal](https://portal.azure.com)|Abra o painel do cluster HDInsight, selecione **tamanho do cluster** no menu à esquerda e, no painel tamanho do cluster, digite o número de nós de trabalho e selecione salvar.|  

![Opção portal do Azure dimensionar cluster](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Usando qualquer um desses métodos, você pode aumentar ou reduzir verticalmente seu cluster HDInsight em apenas alguns minutos.

> [!IMPORTANT]  
> * A CLI clássica do Azure foi preterida e só deve ser usada com o modelo de implantação clássico. Para todas as outras implantações, use o [CLI do Azure](/cli/azure/).
> * O módulo AzureRM do PowerShell foi preterido.  Use o [módulo AZ](/powershell/azure/new-azureps-module-az) sempre que possível.

## <a name="impact-of-scaling-operations"></a>Impacto das operações de dimensionamento

Quando você **adiciona** nós ao cluster do HDInsight em execução (Scale up), os trabalhos não serão afetados. Novos trabalhos podem ser enviados com segurança enquanto o processo de colocação em escala está em execução. Se a operação de dimensionamento falhar, a falha deixará o cluster em um estado funcional.

Se você **remover** nós (reduzir verticalmente), os trabalhos pendentes ou em execução falharão quando a operação de dimensionamento for concluída. Essa falha ocorre devido a alguns dos serviços reiniciando durante o processo de dimensionamento. O cluster pode ficar preso no modo de segurança durante uma operação de dimensionamento manual.

O impacto da alteração do número de nós de dados varia em cada tipo de cluster com suporte do HDInsight:

* Apache Hadoop

    Você pode aumentar diretamente o número de nós de trabalho em um cluster Hadoop em execução sem afetar nenhum trabalho. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. As falhas em uma operação de dimensionamento são normalmente manipuladas. O cluster sempre é deixado em um estado funcional.

    Quando um cluster Hadoop é reduzido com menos nós de dados, alguns serviços são reiniciados. Esse comportamento faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.

* HBase no Apache

    Você pode adicionar ou remover nós diretamente em seu cluster HBase enquanto ele está em execução. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode balancear manualmente os servidores regionais. Faça logon no cluster cabeçalho e execute os seguintes comandos:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre como usar o shell do HBase, consulte [Introdução a um exemplo do Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Você pode adicionar ou remover nós de dados diretamente enquanto o Storm está em execução. No entanto, após uma conclusão bem-sucedida da operação de dimensionamento, você precisará reequilibrar a topologia. O rebalanceamento permite que a topologia reajuste [as configurações de paralelismo](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) com base no novo número de nós no cluster. Para rebalancear topologias em execução, use uma das seguintes opções:

  * Interface da Web Storm

    Use as etapas a seguir para redistribuir uma topologia usando a interface do usuário do Storm.

    1. Abra o `https://CLUSTERNAME.azurehdinsight.net/stormui` no navegador da Web, em que `CLUSTERNAME` é o nome do cluster Storm. Se solicitado, insira o nome de administrador (admin) do cluster HDInsight e a senha que você especificou ao criar o cluster.

    1. Selecione a topologia que você quer rebalancear e selecione o botão **Rebalancear** . Insira o atraso antes que a operação de rebalanceamento seja concluída.

        ![Redistribuir escala do Storm do HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

  * Ferramenta CLI (interface de linha de comando)

    Conecte-se ao servidor e use o seguinte comando para redistribuir uma topologia:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Você também pode especificar parâmetros para substituir as dicas de paralelismo fornecidas originalmente pela topologia. Por exemplo, o código abaixo reconfigura a `mytopology` topologia para 5 processos de trabalho, 3 executores para o componente Blue-Spout e 10 executores para o componente de raio amarelo.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Você deverá redistribuir as réplicas de partições após as operações de dimensionamento. Para obter mais informações, consulte o documento [Alta disponibilidade de dados com o Apache Kafka no HDInsight](./kafka/apache-kafka-high-availability.md).

* Apache Hive LLAP

    Após o dimensionamento para os `N` nós de trabalho, o HDInsight definirá automaticamente as configurações a seguir e reiniciará o hive.

  * Total de consultas simultâneas máximas: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Número de nós usados pelo LLAP do hive: `num_llap_nodes  = N`
  * Número de nó (s) para executar o daemon LLAP do hive: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Como reduzir com segurança um cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Reduzir verticalmente um cluster com trabalhos em execução

Para evitar que seus trabalhos em execução falhem durante uma operação de redução, você pode experimentar três coisas:

1. Aguarde até que os trabalhos sejam concluídos antes de reduzir horizontalmente o cluster.
1. Encerre manualmente os trabalhos.
1. Reenvie os trabalhos após a operação de dimensionamento ser concluída.

Para ver uma lista de trabalhos pendentes e em execução, você pode usar a interface do usuário do YARN **Resource Manager**, seguindo estas etapas:

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster.  O cluster abre em uma nova página do portal.
2. Na exibição principal, navegue até **painéis de cluster**  >  **Ambari página inicial**. Insira suas credenciais de cluster.
3. Na interface do usuário do amAmbari, selecione **yarn** na lista de serviços no menu à esquerda.  
4. Na página YARN, selecione **links rápidos** e passe o mouse sobre o nó principal ativo e, em seguida, selecione **interface do usuário do Gerenciador de recursos**.

    ![Interface do usuário do Apache Ambari Quick links Resource Manager](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Você pode acessar diretamente a interface do usuário do Resource Manager com `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Você vê uma lista de trabalhos, com os respectivos estados atuais. Na captura de tela, há um trabalho em execução no momento:

![Aplicativos de interface do usuário do Resource Manager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para eliminar manualmente o aplicativo que está em execução, execute o seguinte comando no shell SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Travamento no modo de segurança

Quando você reduz verticalmente um cluster, o HDInsight usa as interfaces de gerenciamento do Apache Ambari para primeiro encerrar os nós de trabalho adicionais. Os nós replicam seus blocos HDFS para outros nós de trabalho online. Depois disso, o HDInsight dimensiona com segurança o cluster para baixo. O HDFS entra em modo de segurança durante a operação de dimensionamento. O HDFS deve sair quando o dimensionamento for concluído. Em alguns casos, no entanto, o HDFS fica preso no modo de segurança durante uma operação de dimensionamento devido à replicação do bloco de arquivos.

Por padrão, o HDFS é configurado com uma `dfs.replication` configuração de 1, que controla quantas cópias de cada bloco de arquivo estão disponíveis. Cada cópia de um bloco de arquivos é armazenada em um nó diferente do cluster.

Quando o número esperado de cópias de bloco não estiver disponível, o HDFS entrará no modo de segurança e o Ambari gerará alertas. O HDFS pode entrar no modo de segurança para uma operação de dimensionamento. O cluster poderá ficar preso no modo de segurança se o número necessário de nós não for detectado para replicação.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo quando o modo de segurança está ativado

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Você pode examinar os logs de nó de nome na pasta `/var/log/hadoop/hdfs/`, próximos ao horário em que o cluster foi dimensionado, para ver quando ele entrou em modo de segurança. Os arquivos de log são nomeados `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

A causa raiz era que o hive depende de arquivos temporários no HDFS durante a execução de consultas. Quando o HDFS entra no modo de segurança, o hive não pode executar consultas porque ele não pode gravar no HDFS. Os arquivos temporários no HDFS estão localizados na unidade local montada para as VMs de nó de trabalho individuais. Os arquivos são replicados entre outros nós de trabalho em três réplicas, no mínimo.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Como impedir que o HDInsight fique preso no modo de segurança

Há várias maneiras de impedir que o HDInsight permaneça no modo de segurança:

* Pare todos os trabalhos do Hive antes de reduzir verticalmente o HDInsight. Como alternativa, agende o processo de redução vertical para evitar conflito com trabalhos do Hive em execução.
* Limpe manualmente os arquivos do diretório `tmp` temporários do Hive no HDFS antes de reduzir verticalmente.
* Reduza verticalmente o HDInsight somente até o mínimo de três nós de trabalho. Evite chegar até somente um nó de trabalho.
* Execute o comando para sair do modo de segurança, se necessário.

As seções a seguir descrevem essas opções.

#### <a name="stop-all-hive-jobs"></a>Interromper todos os trabalhos do Hive

Interrompa todos os trabalhos do Hive antes de reduzir verticalmente a um nó de trabalho. Se sua carga de trabalho é agendada, execute a redução vertical após a conclusão do trabalho do Hive.

Parar os trabalhos do hive antes do dimensionamento, ajuda a minimizar o número de arquivos transitórios na pasta tmp (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpar manualmente os arquivos temporários do Hive

Se o Hive deixou arquivos temporários na pasta, você pode limpar manualmente esses arquivos antes de reduzir verticalmente, evitando o modo de segurança.

1. Verifique qual local está sendo usado para arquivos temporários do hive examinando a `hive.exec.scratchdir` propriedade de configuração. Esse parâmetro é definido em `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Interrompa os serviços do Hive e verifique se todas as consultas e os trabalhos foram concluídos.

1. Liste o conteúdo do diretório temporário encontrado acima, `hdfs://mycluster/tmp/hive/` para ver se ele contém algum arquivo:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Aqui está um exemplo de saída quando há arquivos:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Se souber que Hive terminou de usar esses arquivos, você poderá removê-los. Certifique-se de que o hive não tenha nenhuma consulta em execução examinando a página da interface do usuário do yarn Resource Manager.

    Linha de comando de exemplo para remover arquivos do HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Dimensionar o HDInsight para três ou mais nós de trabalho

Se seus clusters ficarem presos no modo de segurança com frequência ao reduzir verticalmente para menos de três nós de trabalho, mantenha pelo menos três nós de trabalho.

Ter três nós de trabalho é mais dispendioso do que escalar verticalmente para apenas um nó de trabalho. No entanto, essa ação impedirá que o cluster fique preso no modo de segurança.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Dimensionar o HDInsight para um nó de trabalho

Mesmo quando o cluster é reduzido para um nó, o nó de trabalho 0 ainda será sobrevivente. O nó de trabalho 0 nunca pode ser encerrado.

#### <a name="run-the-command-to-leave-safe-mode"></a>Executar o comando para sair do modo de segurança

A opção final é executar o comando sair do modo de segurança. Se o HDFS entrou no modo de segurança por causa do arquivo de Hive em replicação, execute o seguinte comando para sair do modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Reduzir verticalmente um cluster do Apache HBase

Os servidores de região são balanceados automaticamente em alguns minutos após a conclusão de uma operação de dimensionamento. Para balancear manualmente os servidores de região, conclua as seguintes etapas:

1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie o shell do HBase:

    ```bash
    hbase shell
    ```

3. Use o seguinte comando para balancear manualmente os servidores de região:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Próximas etapas

* [Dimensionar automaticamente os clusters do Azure HDInsight](hdinsight-autoscale-clusters.md)

Para obter informações específicas sobre como dimensionar o cluster HDInsight, consulte:

* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerenciar clusters de Apache Hadoop no HDInsight usando CLI do Azure](hdinsight-administer-use-command-line.md#scale-clusters)