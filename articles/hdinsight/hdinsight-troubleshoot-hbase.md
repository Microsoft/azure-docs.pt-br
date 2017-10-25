---
title: Solucionar problemas do HBase usando o Azure HDInsight | Microsoft Docs
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o HBase e o Azure HDInsight.
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: 15412c3853a2b8436c5e96034c9a92a2a1094662
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Solucionar problemas do HBase usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas soluções ao trabalhar com cargas de Apache HBase no Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Como fazer para executar relatórios de comando hbck com várias regiões não atribuídas

Uma mensagem de erro comum que pode ser executada ao usar o comando `hbase hbck` é: "várias regiões sem atribuição ou espaços na cadeia de regiões."

Na interface do usuário do HBase Master você pode exibir o número de regiões desbalanceadas em todos os servidores da região. Depois, você pode executar o comando `hbase hbck` para ver os espaços na cadeia de regiões.

Os espaços podem ser causados pelas regiões offline, portanto, corrija as atribuições primeiro. 

Execute estas etapas para levar as regiões não atribuídas de volta a um estado normal:

1. Entre no cluster do HDInsight HBase usando SSH.
2. Para conectar-se com o shell do ZooKeeper, execute o comando `hbase zkcli`.
3. Execute o comando `rmr /hbase/regions-in-transition` ou o comando `rmr /hbase-unsecure/regions-in-transition`.
4. Para sair do shell do `hbase zkcli`, use o comando `exit`.
5. Abra a interface do usuário do Apache Ambari e reinicie o serviço Active HBase Master.
6. Execute o comando `hbase hbck` novamente (sem opções). Verifique a saída desse comando para garantir a atribuição de todas as regiões.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Como fazer para corrigir problemas de tempo limite usando comandos hbck para atribuições de região

### <a name="issue"></a>Problema

Uma possível causa de problemas do tempo limite, quando você usa o comando `hbck`, é que várias regiões se encontram no estado "em transição" por um longo tempo. Você pode ver essas regiões como offline na interface do usuário do HBase Master. Devido ao grande número de regiões tentando fazer a transição, o HBase Master pode atingir o tempo limite e não conseguir colocar essas regiões no estado online novamente.

### <a name="resolution-steps"></a>Etapas de resolução

1. Entre no cluster do HDInsight HBase usando SSH.
2. Para conectar-se com o shell do ZooKeeper, execute o comando `hbase zkcli`.
3. Execute o comando `rmr /hbase/regions-in-transition` ou `rmr /hbase-unsecure/regions-in-transition`.
4. Para sair do shell do `hbase zkcli`, use o comando `exit`.
5. Na interface do usuário do Ambari, reinicie o serviço Active HBase Master.
6. Execute o comando `hbase hbck -fixAssignments` novamente.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Como forçar a desabilitação do modo de segurança de HDFS em um cluster

### <a name="issue"></a>Problema

O HDFS (Sistema de Arquivos Distribuído do Hadoop) local está travado no modo de segurança no cluster HDInsight.

### <a name="detailed-description"></a>Descrição detalhada

Esse erro pode ser causado por uma falha na execução do seguinte comando do HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Este é o erro que você pode receber ao tentar executar o comando:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Causa provável

O cluster HDInsight foi reduzido para pouquíssimos nós. O número de nós está abaixo ou próximo ao fator de replicação do HDFS.

### <a name="resolution-steps"></a>Etapas de resolução 

1. Obtenha o status do HDFS no cluster HDInsight executando os seguintes comandos:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Também é possível verificar a integridade do HDFS no cluster HDInsight usando os seguintes comandos:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Se você identificar que não há blocos ausentes, corrompidos ou sub-replicados, ou que esses blocos podem ser ignorados, execute o seguinte comando para colocar o nó de nome fora do modo de segurança:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Como corrigir problemas de conectividade de JDBC ou SQLLine com o Apache Phoenix

### <a name="resolution-steps"></a>Etapas de resolução

Para conectar-se ao Phoenix, você deve fornecer o endereço IP de um nó ativo do ZooKeeper. Certifique-se de que o serviço ZooKeeper com o qual sqlline.py está tentando se conectar esteja em execução.
1. Entre no cluster do HDInsight usando SSH.
2. Digite o seguinte comando:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Você pode obter o endereço IP do nó ativo do ZooKeeper na interface do usuário do Ambari. Acesse **HBase** > **Links Rápidos** > **ZK\* (Ativo)** > **Informações do Zookeeper**. 

3. Se o sqlline.py conectar-se ao Phoenix e não exceder o tempo limite, execute o comando a seguir para validar a disponibilidade e a integridade do Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Se esse comando funcionar, não haverá nenhum problema. O endereço IP fornecido pelo usuário pode estar incorreto. No entanto, se o comando pausar por um longo período e depois exibir o erro abaixo, passe para a etapa 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Execute os comandos a seguir em um nó principal (hn0) para diagnosticar a condição da tabela Phoenix SYSTEM.CATALOG:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   O comando deverá retornar o seguinte erro: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Na interface do usuário do Ambari, complete as etapas a seguir para reiniciar o serviço HMaster em todos os nós do ZooKeeper:

    1. Na seção **Resumo** do HBase, acesse **HBase** > **Active HBase Master**. 
    2. Na seção **Componentes**, reinicie o serviço HBase Master.
    3. Repita essas etapas para todos os serviços **Standby HBase Master** restantes. 

Pode levar até cinco minutos para o serviço HBase Master se estabilizar e concluir o processo de recuperação. Após alguns minutos, repita os comandos sqlline.py para confirmar que a tabela SYSTEM.CATALOG está ativa e pode ser consultada. 

Quando a tabela SYSTEM.CATALOG voltar ao normal, o problema de conectividade com o Phoenix deverá ser resolvido automaticamente.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>O que faz com o servidor mestre falhar ao iniciar

### <a name="error"></a>Erro 

Uma falha de renomeação atômica ocorre.

### <a name="detailed-description"></a>Descrição detalhada

Durante o processo de inicialização, o HMaster realiza várias etapas de inicialização. Isso inclui a movimentação de dados da pasta de rascunho (.tmp) para a pasta de dados. O HMaster também procura na pasta de logs write-ahead (WALs) para ver se há quaisquer servidores de região sem resposta, e assim por diante. 

Durante a inicialização, o HMaster executa um comando `list` básico nessas pastas. Se, a qualquer momento, o HMaster detectar um arquivo inesperado em qualquer uma dessas pastas, ele lançará uma exceção e não iniciará.  

### <a name="probable-cause"></a>Causa provável

Nos logs do servidor de região, tente identificar a linha do tempo da criação do arquivo e, em seguida, veja se houve uma falha no processo no momento da criação do arquivo. (Contate o suporte da HBase para ajudar com isso). Isso nos ajuda a fornecer mecanismos mais robustos, para que você evite esse bug e garanta desligamentos de processo normais.

### <a name="resolution-steps"></a>Etapas de resolução

Verifique a pilha de chamadas e tente determinar qual pasta pode estar causando o problema (por exemplo, pode ser a pasta WALs ou a pasta .tmp). Em seguida, no Cloud Explorer ou usando comandos do HDFS, tente localizar o arquivo problemático. Geralmente, é um arquivo \*-renamePending.json. (\*-renamePending.json é um arquivo de diário usado para implementar a operação de renomeação atômica no driver WASB. Devido a erros nessa implementação, esses arquivos podem ser abandonados após falhas de processo, e assim por diante.) Force a exclusão desse arquivo no Cloud Explorer ou usando os comandos do HDFS. 

Às vezes, pode haver um arquivo temporário com um nome parecido com *$$$.$$$* nesse local. Você deve usar o comando `ls` de HDFS para ver esse arquivo; não é possível ver o arquivo no Cloud Explorer. Para excluir este arquivo, use o comando `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` de HDFS.  

Após a execução desses comandos, o HMaster deve iniciar imediatamente. 

### <a name="error"></a>Erro

Nenhum endereço de servidor está listado em *hbase: meta* para a região xxx.

### <a name="detailed-description"></a>Descrição detalhada

Talvez você veja uma mensagem no cluster do Linux que indica que a tabela *hbase: meta* não está online. A execução de `hbck` pode relatar que "replicaId 0 da tabela hbase: meta não foi encontrado em nenhuma região". Talvez o problema seja a incapacidade de inicializar o HMaster após a reinicialização do HBase. Nos logs do HMaster, a seguinte mensagem poderá ser exibida: "Nenhum endereço de servidor listado no hbase: meta para a região hbase: backup up da \<nome da região\>  

### <a name="resolution-steps"></a>Etapas de resolução

1. No shell do HBase, insira os comandos a seguir (altere os valores reais conforme aplicável):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Exclua a entrada *hbase: namespace*. Essa entrada pode ser o mesmo erro que está sendo relatado quando a tabela *hbase: namespace* é verificada.

3. Para levar o HBase para um estado de execução, na interface do usuário do Ambari, reinicie o serviço Active HMaster.  

4. No shell do HBase, execute o seguinte comando para mostrar todas as tabelas offline:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Leitura adicional

[Não é possível processar a tabela do HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Erro

O HMaster atinge o tempo limite com uma exceção fatal parecida com "java.io.IOException: Timedout 300000ms aguardando a tabela de namespace ser atribuída".

### <a name="detailed-description"></a>Descrição detalhada

Você pode enfrentar esse problema se tiver muitas tabelas e regiões que não foram liberadas ao reiniciar os serviços do HMaster. A reinicialização pode falhar, e você verá a mensagem de erro anterior.  

### <a name="probable-cause"></a>Causa provável

Este é um problema conhecido com o serviço HMaster. Tarefas de inicialização de cluster geral podem levar muito tempo. O HMaster desliga porque a tabela de namespace ainda não foi atribuída. Isso ocorre somente em cenários nos quais não há uma grande quantidade de dados não liberados, e um tempo limite de cinco minutos não é suficiente.
  
### <a name="resolution-steps"></a>Etapas de resolução

1. Na interface do usuário do Ambari, acesse **HBase** > **Configurações**. No arquivo hbase-site.xml personalizado, adicione a seguinte configuração: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Reinicie os serviços necessários (HMaster e possivelmente outros serviços do HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>O que causa uma falha de reinicialização em um servidor de região

### <a name="issue"></a>Problema

É possível impedir uma falha de reinicialização em um servidor de região seguindo estas práticas recomendadas. Recomendamos pausar a atividade com uso intenso de carga de trabalho ao planejar reiniciar servidores de região do HBase. Se um aplicativo continuar a se conectar a servidores da região quando o desligamento estiver em andamento, a operação de reinicialização do servidor de região será desacelerada em vários minutos. Além disso, convém primeiro liberar todas as tabelas. Para obter uma referência sobre como liberar tabelas, confira [HDInsight HBase: como melhorar o tempo de reinicialização do cluster HBase liberando tabelas](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Se você iniciar a operação de reinicialização em servidores de região do HBase na interface do usuário do Ambari, verá imediatamente que os servidores de região foram desativados, mas eles não reiniciam imediatamente. 

Veja a seguir o que está acontecendo em segundo plano: 

1. O agente Ambari envia uma solicitação de parada para o servidor de região.
2. O agente do Ambari aguarda 30 segundos para o servidor de região desligar normalmente. 
3. Se o seu aplicativo continuar a se conectar com o servidor de região, o servidor não desligará imediatamente. O tempo limite de 30 segundos expira antes do desligamento. 
4. Após 30 segundos, o agente do Ambari envia um comando (`kill -9`) de encerramento forçado ao servidor de região. Você pode ver isso no log do agente do ambari (no diretório /var/log/ do nó de trabalho respectivo):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
Devido a esse desligamento inesperado, a porta associada ao processo talvez não seja liberada, embora o processo de servidor da região seja interrompido. Essa situação pode levar a uma AddressBindException quando o servidor de região estiver iniciando, como mostram os logs abaixo. É possível verificar isso em region-server.log no diretório /var/log/hbase, nos nós de trabalho em que a inicialização do servidor de região falha. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Etapas de resolução

1. Tente reduzir a carga nos servidores de região do HBase antes de iniciar uma reinicialização. 
2. Como alternativa (se a etapa 1 não ajudar), tente reiniciar manualmente os servidores de região nos nós de trabalho usando os comandos a seguir:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

