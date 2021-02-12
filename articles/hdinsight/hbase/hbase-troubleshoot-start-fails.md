---
title: O Apache HBase Master não é iniciado no Azure HDInsight
description: O Apache HBase Master (HMaster) não é iniciado no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: c30077d0d8f359e93745b53755f9dae998073d4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936899"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>O Apache HBase Master (HMaster) não é iniciado no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Cenário: falha de renomeação atômica

### <a name="issue"></a>Problema

Arquivos inesperados identificados durante o processo de inicialização.

### <a name="cause"></a>Causa

Durante o processo de inicialização, o HMaster executa muitas etapas de inicialização, incluindo a movimentação de dados da pasta Scratch (. tmp) para a pasta de dados. O HMaster também examina a pasta logs write-ahead (WAL) para ver se há servidores de região sem resposta.

HMaster faz um comando de lista básica nas pastas WAL. Se, a qualquer momento, o HMaster detectar um arquivo inesperado em qualquer uma dessas pastas, ele lançará uma exceção e não iniciará.

### <a name="resolution"></a>Resolução

Verifique a pilha de chamadas e tente determinar qual pasta pode estar causando o problema (por exemplo, pode ser a pasta WAL ou a pasta. tmp). Em seguida, no Cloud Explorer ou usando comandos do HDFS, tente localizar o arquivo problemático. Normalmente, esse é um `*-renamePending.json` arquivo. (O `*-renamePending.json` arquivo é um arquivo de diário que é usado para implementar a operação de renomeação atômica no driver WASB. Devido a bugs nessa implementação, esses arquivos podem ser deixados após a falha do processo e assim por diante.) Force-exclui esse arquivo no Cloud Explorer ou usando comandos do HDFS.

Às vezes, também pode haver um arquivo temporário chamado algo como `$$$.$$$` neste local. Você deve usar o comando `ls` de HDFS para ver esse arquivo; não é possível ver o arquivo no Cloud Explorer. Para excluir este arquivo, use o comando `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` de HDFS.

Após a execução desses comandos, o HMaster deve iniciar imediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Cenário: nenhum endereço de servidor listado

### <a name="issue"></a>Problema

Você pode ver uma mensagem que indica que a `hbase: meta` tabela não está online. A execução `hbck` pode informar que `hbase: meta table replicaId 0 is not found on any region.` nos logs do HMaster, você pode ver a mensagem: `No server address listed in hbase: meta for region hbase: backup <region name>` .  

### <a name="cause"></a>Causa

HMaster não pôde inicializar após a reinicialização do HBase.

### <a name="resolution"></a>Resolução

1. No shell do HBase, insira os comandos a seguir (altere os valores reais conforme aplicável):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Exclua a `hbase: namespace` entrada. Essa entrada pode ser o mesmo erro que está sendo relatado quando a `hbase: namespace` tabela é verificada.

1. Reinicie o HMaster ativo da interface do usuário do Ambari para exibir o HBase em estado de execução.

1. No shell do HBase, execute o seguinte comando para mostrar todas as tabelas offline:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Cenário: Java. IO. IOException: TimedOut

### <a name="issue"></a>Problema

O HMaster expira com uma exceção fatal semelhante a: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` .

### <a name="cause"></a>Causa

Você pode enfrentar esse problema se tiver muitas tabelas e regiões que não foram liberadas ao reiniciar os serviços do HMaster. O tempo limite é um defeito conhecido com HMaster. Tarefas de inicialização de cluster geral podem levar muito tempo. O HMaster será desligado se a tabela de namespace ainda não tiver sido atribuída. As tarefas de inicialização demoradas ocorrem onde há grande quantidade de dados não liberados e um tempo limite de cinco minutos não é suficiente.

### <a name="resolution"></a>Resolução

1. Na interface do usuário do Apache Ambari, vá para configurações do **HBase**  >  . No arquivo personalizado `hbase-site.xml` , adicione a seguinte configuração:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Reinicie os serviços necessários (HMaster e possivelmente outros serviços do HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Cenário: reinicializações de servidor de região frequentes

### <a name="issue"></a>Problema

Nós reiniciam periodicamente. Nos logs do servidor de região, você pode ver entradas semelhantes a:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Pausa longa do `regionserver` GC de JVM. A pausa causará uma `regionserver` falta de resposta e não será capaz de enviar uma pulsação de coração para HMaster dentro do 40s de tempo limite de sessão ZK. O HMaster acreditará `regionserver` estar inativo e anulará o `regionserver` e será reiniciado.

### <a name="resolution"></a>Resolução

Altere o tempo limite da sessão Zookeeper, não apenas `hbase-site` configurando, `zookeeper.session.timeout` mas também `zoo.cfg` a configuração Zookeeper, `maxSessionTimeout` precisa ser alterada.

1. Acesse a interface do usuário do Ambari, vá para **> de configuração do HBase – configurações de >**, na seção tempos limite, altere o valor do tempo limite da sessão Zookeeper.

1. Acesse a interface do usuário do Ambari, acesse **Zookeeper-> configurações-> personalizado** `zoo.cfg` , adicione/altere a configuração a seguir. Verifique se o valor é o mesmo que o HBase `zookeeper.session.timeout` .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Reinicie os serviços necessários.

---

## <a name="scenario-log-splitting-failure"></a>Cenário: falha de divisão de log

### <a name="issue"></a>Problema

Falha do HMasters ao surgir em um cluster HBase.

### <a name="cause"></a>Causa

Configurações do HDFS e do HBase configuradas incorretamente para uma conta de armazenamento secundária.

### <a name="resolution"></a>Resolução

Defina HBase. rootdir: wasb://@.blob.core.windows.net/hbase e reinicie os serviços em Ambari.

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).