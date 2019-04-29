---
title: Dimensionar tamanhos de cluster – Azure HDInsight
description: Dimensione um cluster HDInsight para sua carga de trabalho.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: ashish
ms.openlocfilehash: a172024e4662e647b39fe999f1be3cfcef04b5ce
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763609"
---
# <a name="scale-hdinsight-clusters"></a>Dimensionar clusters HDInsight

O HDInsight proporciona elasticidade, oferecendo a opção de escalar e reduzir verticalmente o número de nós de trabalho em seus clusters. Isso permite que você reduza um cluster depois do horário comercial ou nos finais de semana e o expanda durante picos de demanda corporativa.

Por exemplo, se você tem algum processamento em lotes que ocorre uma vez por dia ou uma vez por mês, o cluster HDInsight pode ser escalonado verticalmente alguns minutos antes desse evento agendado para que haja memória e capacidade de computação de CPU suficientes.  Posteriormente, depois que o processamento for concluído e uso reduzir novamente, você poderá reduzir verticalmente o cluster HDInsight, obtendo menos nós de trabalho.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="utilities-to-scale-clusters"></a>Utilitários para dimensionar clusters

A Microsoft fornece os seguintes utilitários para dimensionar clusters:

|Utilitário | DESCRIÇÃO|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Conjunto AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<nome do Cluster > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Conjunto AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<nome do Cluster > - TargetInstanceCount \<NewSize >|
|[CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)|[hdinsight AZ redimensionar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) – grupo de recursos \<grupo de recursos > – nome \<nome do Cluster >-contagem de instância de destino \<NewSize >|
|[CLI clássica do Azure](hdinsight-administer-use-command-line.md)|redimensionamento do cluster hdinsight do Azure \<clusterName > \<contagem de instâncias de destino >|
|[Portal do Azure](https://portal.azure.com)|Abra o painel do cluster HDInsight, selecione **tamanho do Cluster** no menu à esquerda, em seguida, no painel de tamanho do Cluster, digite o número de nós de trabalho e selecione Salvar.|  

![Dimensionar Cluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Usando qualquer um desses métodos, você pode aumentar ou reduzir verticalmente seu cluster HDInsight em apenas alguns minutos.

> [!IMPORTANT]  
> * A CLI do Azure clássica é preterida e só deve ser usada com o modelo de implantação clássico. Para todas as outras implantações, use o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * O módulo PowerShell AzureRM foi preterido.  Use o [módulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) sempre que possível.

## <a name="scaling-impacts-on-running-jobs"></a>Impactos do dimensionamento nos trabalhos em execução

Ao **adicionar** nós ao cluster HDInsight em execução, os trabalhos pendentes ou em execução não serão afetados. Além disso, novos trabalhos podem ser enviados com segurança enquanto o processo de dimensionamento está em execução. Se as operações de dimensionamento falharem por qualquer motivo, a falha será sutilmente tratada, mantendo o cluster em um estado funcional.

No entanto, se você estiver reduzindo verticalmente o cluster por meio da **remoção** de nós, todos os trabalhos pendentes ou em execução falharão quando operação de dimensionamento for concluída. Essa falha ocorre devido ao reinício de alguns dos serviços durante o processo.

Para resolver esse problema, aguarde a conclusão dos trabalhos antes de reduzir verticalmente o cluster, encerre os trabalhos manualmente ou envie novamente os trabalhos após a conclusão da operação de dimensionamento.

Para ver uma lista de trabalhos pendentes e em execução, use a interface do usuário ResourceManager do YARN, seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Da esquerda, navegue até **todos os serviços** > **Analytics** > **Clusters de HDInsight**e, em seguida, selecione seu cluster.
3. No modo de exibição principal, navegue até **painéis do Cluster** > **Ambari base**. Insira suas credenciais de logon do cluster.
4. Na UI do Ambari, selecione **YARN** na lista de serviços no menu à esquerda.  
5. Na página do YARN, selecione **Links rápidos** e passe o mouse sobre o nó principal ativo, em seguida, selecione **UI ResourceManager**.

    ![Interface do usuário do ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Você pode acessar diretamente a interface do usuário do ResourceManager com `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Você vê uma lista de trabalhos, com os respectivos estados atuais. A captura de tela exibe um trabalho em execução:

![Aplicativos da interface do usuário do ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para eliminar manualmente o aplicativo que está em execução, execute o seguinte comando no shell SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo: 

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-apache-hbase-cluster"></a>Rebalanceamento de um cluster do Apache HBase

Os servidores de região são balanceados automaticamente em alguns minutos após o término da operação de dimensionamento. Para balancear manualmente servidores de região, use as seguintes etapas:

1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie o shell do HBase:

        hbase shell

3. Use o seguinte comando para balancear manualmente os servidores de região:

        balancer

## <a name="scale-down-implications"></a>Implicações da redução vertical

Conforme mencionado anteriormente, todos os trabalhos pendentes ou em execução são encerrados na conclusão de uma operação de redução vertical. No entanto, há outras possíveis implicações que pode ocorrer com a redução vertical.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>Nó de nome do HDInsight permanece no modo de segurança após a redução vertical

Se você reduzir o cluster ao mínimo de um nó de trabalho, Apache HDFS pode ficar preso no modo de segurança quando nós de trabalho são reinicializados devido à aplicação de patches ou imediatamente após a operação de dimensionamento.

A principal causa disso é que o Hive usa alguns arquivos `scratchdir` e, por padrão, espera três réplicas de cada bloco, mas haverá apenas uma réplica possível se você reduzir verticalmente para o mínimo de um nó de um trabalho. Consequentemente, os arquivos no `scratchdir` se tornam *sub-replicados*. Isso poderia fazer com que o HDFS permanecesse em modo de segurança quando os serviços fosse reiniciados após a operação de dimensionamento.

Quando ocorre uma tentativa de reduzir verticalmente, o HDInsight baseia as interfaces de gerenciamento do Apache Ambari para primeiro desativar os nós de trabalho adicionais indesejados, o qual replicam seus blocos do HDFS para outros nós de trabalho online, e, em seguida, dimensionar com segurança o cluster para baixo. O HDFS entra em um modo de segurança durante a janela de manutenção e deveria sair quando o dimensionamento fosse concluído. É nesse ponto que o HDFS pode ficar preso no modo de segurança.

O HDFS está configurado com uma configuração `dfs.replication` igual a 3. Assim, os blocos dos arquivos temporários são sub-replicados sempre que houver menos de três nós de trabalho online, devido à indisponibilidade das três cópias esperadas de cada bloco de arquivo.

Você pode executar um comando para retirar o HDFS do modo de segurança. Por exemplo, se você sabe que o único motivo pelo qual o modo de segurança está ativo é porque os arquivos temporários estão sub-replicados, você pode sair do modo de segurança com tranquilidade. Isso porque os arquivos sub-replicados são arquivos temporários de rascunho do Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Depois de sair do modo de segurança, você pode remover manualmente os arquivos temporários ou aguardar que o Hive limpe-os automaticamente.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo quando o modo de segurança está ativado

* H070 Não é possível abrir a sessão do Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Não é possível criar o diretório** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. **Nó de nome está em modo de segurança**. Os blocos relatados 75 precisam de 12 blocos adicionais para alcançar o limite 0.9900 de 87 blocos no total. O número de datanodes ativos 10 alcançou o número mínimo de 0. O modo de segurança será desativado automaticamente quando os limites forem alcançados.

* H100 Não é possível enviar a instrução mostrar bancos de dados: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: A conexão a hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] falhou: **Conexão recusada**

* H020 Não foi possível estabelecer uma conexão com hn0-hdisrv.servername.bx.internal.cloudapp.net:10001: org.apache.thrift.transport.TTransportException: Não foi possível criar a conexão http para http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: A conexão com hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] falhou: Conexão recusada: org.apache.thrift.transport.TTransportException: Não foi possível criar a conexão http para http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: A conexão com hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] falhou: **Conexão recusada**

* Dos logs do Hive: WARN [main]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) – Erro ao iniciar HiveServer2 na 21ª tentativa, nova tentativa em 60 segundos java.lang.RuntimeException: Erro ao aplicar política de autorização à configuração do hive: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Não é possível criar o diretório** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Nó de nome está em modo de segurança**.
    Os blocos relatados 0 precisam de 9 blocos adicionais para alcançar o limite 0.9900 de 9 blocos no total.
    O número de datanodes ativos 10 alcançou o número mínimo de 0. **O modo de segurança será desativado automaticamente quando os limites forem alcançados**.
    em org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Você pode examinar os logs de nó de nome na pasta `/var/log/hadoop/hdfs/`, próximos ao horário em que o cluster foi dimensionado, para ver quando ele entrou em modo de segurança. Os arquivos de log são nomeados `Hadoop-hdfs-namenode-hn0-clustername.*`.

A causa raiz dos erros anteriores é que o Hive depende de arquivos temporários do HDFS durante a execução de consultas. Quando o HDFS entra em modo de segurança, o Hive não consegue executar consultas porque não pode gravar no HDFS. Os arquivos temporários do HDFS ficam localizados na unidade local, montada para as VMs de cada nó de trabalho e replicados entre os outros nós de trabalho em, no mínimo, três réplicas.

O parâmetro `hive.exec.scratchdir` no Hive é configurado em `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Exibir a integridade e o estado do seu sistema de arquivos do HDFS

Você pode exibir um relatório de status de cada nó de nome para verificar se os nós estão no modo de segurança. Para exibir o relatório, use SSH para se conectar a cada nó de cabeçalho e execute o seguinte comando:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Modo de seguro desativado](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]  
> A opção `-D` é necessária porque o sistema de arquivos padrão no HDInsight é o Armazenamento do Microsoft Azure ou o Azure Data Lake Storage. `-D` especifica que os comandos são executados no sistema de arquivos do HDFS local.

Em seguida, você pode exibir um relatório que mostra os detalhes do estado do HDFS:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Esse comando resulta no seguinte, em um cluster íntegro em que todos os blocos estão replicados de acordo com o grau esperado:

![Modo de seguro desativado](./media/hdinsight-scaling-best-practices/report.png)

O HDFS é compatível com o comando `fsck` para verificar se há inconsistências com vários arquivos, como ausência de blocos em um arquivo ou blocos sub-replicados. Para executar o comando `fsck` nos arquivos `scratchdir` (disco temporário de rascunho):

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Quando executado em um sistema de arquivos íntegro do HDFS, sem blocos sub-replicados, você verá uma saída semelhante à seguinte:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

Por outro lado, quando o comando `fsck` é executado em um sistema de arquivos do HDFS com alguns blocos sub-replicados, a saída será semelhante à seguinte:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Você também pode exibir o status do HDFS na interface do usuário do Ambari, ao selecionar o serviço **HDFS** à esquerda ou com `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Status do HDFS no Ambari](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Você também pode ver um ou mais erros críticos em NameNodes ativos ou em espera. Para exibir a integridade dos blocos NameNode, selecione o link de NameNode ao lado do alerta.

![Integridade de blocos NameNode](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Para limpar os arquivos temporários, o qual removem os erros de replicação de bloco, o SSH em cada nó de cabeçalho e execute o seguinte comando:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]  
> Este comando poderá interromper o Hive se ainda houver alguns trabalhos em execução.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Como impedir que o HDInsight fique preso no modo de segurança devido a blocos sub-replicados

Há várias maneiras de impedir que o HDInsight permaneça no modo de segurança:

* Pare todos os trabalhos do Hive antes de reduzir verticalmente o HDInsight. Como alternativa, agende o processo de redução vertical para evitar conflito com trabalhos do Hive em execução.
* Limpe manualmente os arquivos do diretório `tmp` temporários do Hive no HDFS antes de reduzir verticalmente.
* Reduza verticalmente o HDInsight somente até o mínimo de três nós de trabalho. Evite chegar até somente um nó de trabalho.
* Execute o comando para sair do modo de segurança, se necessário.

As seções a seguir descrevem essas opções.

#### <a name="stop-all-hive-jobs"></a>Interromper todos os trabalhos do Hive

Interrompa todos os trabalhos do Hive antes de reduzir verticalmente a um nó de trabalho. Se sua carga de trabalho é agendada, execute a redução vertical após a conclusão do trabalho do Hive.

Isso ajuda a minimizar o número de arquivos temporários na pasta tmp (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpar manualmente os arquivos temporários do Hive

Se o Hive deixou arquivos temporários na pasta, você pode limpar manualmente esses arquivos antes de reduzir verticalmente, evitando o modo de segurança.

1. Interrompa os serviços do Hive e verifique se todas as consultas e os trabalhos foram concluídos.

2. Liste o conteúdo do diretório `hdfs://mycluster/tmp/hive/` para ver se ele contém algum arquivo:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Aqui está um exemplo de saída quando há arquivos:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Se souber que Hive terminou de usar esses arquivos, você poderá removê-los. Verifique se o Hive não tem nenhuma consulta em execução examinando a página de interface do usuário do Yarn ResourceManager.

    Linha de comando de exemplo para remover arquivos do HDFS:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Dimensionar o HDInsight para três nós de trabalho

Se ficar preso no modo de segurança é um problema persistente e as etapas anteriores não são opções adequadas, talvez seja melhor evitar o problema, reduzindo verticalmente somente até três nós de trabalho. Isso pode não ser o ideal, devido a restrições de custos, em comparação com a redução vertical a um nó. No entanto, com apenas um nó de trabalho, o HDFS não pode garantir que três réplicas dos dados estejam disponíveis para o cluster.

#### <a name="run-the-command-to-leave-safe-mode"></a>Executar o comando para sair do modo de segurança

A opção final é observar as raras ocasiões em que o HDFS entra em modo de segurança e, então, executar o comando para sair do modo de segurança. Uma vez que você determinou que o motivo pelo qual o HDFS entrou no modo de segurança deve-se aos arquivos sub-replicados do Hive, execute o seguinte comando para sair do modo de segurança:

* HDInsight no Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight no Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Dimensionar clusters](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
