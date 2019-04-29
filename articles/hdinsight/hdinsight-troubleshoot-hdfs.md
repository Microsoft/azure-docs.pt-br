---
title: Solucionar problemas do HDFS usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o HDFS e o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0a310eaeb9baf6ed2438b9f824cd6ad7eb492915
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763996"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Solucione problemas de HDFS do Apache Hadoop usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas resoluções ao trabalhar com o conteúdo HDFS (Sistema de Arquivos Distribuído Hadoop) no Apache Ambari.

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Como fazer para acessar o HDFS local de dentro de um cluster?

### <a name="issue"></a>Problema

Acesse o HDFS local da linha de comando e do código do aplicativo em vez de usar o armazenamento de Blobs do Azure ou o Azure Data Lake Storage de dentro do cluster HDInsight.   

### <a name="resolution-steps"></a>Etapas de resolução

1. No prompt de comando, use `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` literalmente, como no seguinte comando:

    ```apache
    hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. No código-fonte, use o URI `hdfs://mycluster/` literalmente, como no seguinte exemplo de aplicativo:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Execute o arquivo .jar compilado (por exemplo, um arquivo chamado `java-unit-tests-1.0.jar`) no cluster HDInsight com o seguinte comando:

    ```apache
    hdiuser@hn0-spark2:~$ hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```


## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Como fazer para forçar a desabilitação do modo de segurança do HDFS em um cluster?

### <a name="issue"></a>Problema

O HDFS local está travado no modo de segurança no cluster HDInsight.   

### <a name="detailed-description"></a>Descrição detalhada

A falha ocorre quando você executa o seguinte comando HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Você vê o seguinte erro quando executa o comando:

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

1. Obtenha o status do HDFS no cluster HDInsight usando os seguintes comandos:

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

2. Verifique a integridade do HDFS no cluster HDInsight usando os seguintes comandos:

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

### <a name="see-also"></a>Veja também
[Solucionar problemas usando o Azure HDInsight](hdinsight-troubleshoot-guide.md)
