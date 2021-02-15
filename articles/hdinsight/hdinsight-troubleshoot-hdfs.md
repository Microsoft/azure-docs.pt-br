---
title: Solucionar problemas do HDFS no Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o HDFS e o Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 0be7805493e5acc41254c57ca912b5a2ecf02dae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931463"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Solucione problemas de HDFS do Apache Hadoop usando o Azure HDInsight

Conheça os principais problemas e as resoluções ao trabalhar com o Sistema de Arquivos Distribuído do Hadoop (HDFS). Para obter uma lista completa de comandos, consulte o [Guia de comandos do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) e o guia do shell do sistema de [arquivos](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Como fazer para acessar o HDFS local de dentro de um cluster?

### <a name="issue"></a>Problema

Acesse o HDFS local da linha de comando e do código do aplicativo em vez de usar o armazenamento de Blobs do Azure ou o Azure Data Lake Storage de dentro do cluster HDInsight.

### <a name="resolution-steps"></a>Etapas de resolução

1. No prompt de comando, use `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` literalmente, como no seguinte comando:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
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
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Exceção de armazenamento para gravar no blob

### <a name="issue"></a>Problema

Ao usar os `hadoop` `hdfs dfs` comandos ou para gravar arquivos que tenham aproximadamente 12 GB ou mais em um cluster HBase, você pode vir ao longo do seguinte erro:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Causa

os clusters HBase no HDInsight são padronizados para um tamanho de bloco de 256 KB na gravação no armazenamento do Azure. Embora isso funcione para APIs HBase ou APIs REST, isso resultará em um erro ao usar os utilitários de linha de comando `hadoop` ou `hdfs dfs`.

### <a name="resolution"></a>Resolução

use `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Você pode fazer essa modificação com base em cada uso usando o `-D` parâmetro. O seguinte comando é um exemplo de uso desse parâmetro com o comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando o Apache Ambari. As etapas a seguir podem ser usadas para alterar o valor na interface de usuário do Ambari Web:

1. No navegador, acesse a interface de usuário do Ambari Web para seu cluster. A URL é `https://CLUSTERNAME.azurehdinsight.net` , em que `CLUSTERNAME` é o nome do cluster. Quando solicitado, insira o nome de administrador e a senha de administrador para o cluster.
2. No lado esquerdo da tela, escolha **HDFS** e selecione a guia **Configurações**.
3. No campo **Filtrar...** , digite `fs.azure.write.request.size` .
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

    ![Imagem de alteração de valor por meio da interface de usuário do Ambari Web](./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png)

Para saber mais sobre como usar o Ambari, confira [Gerenciar clusters HDInsight interface do usuário Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

O [`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) comando exibe tamanhos de arquivos e diretórios contidos no diretório determinado ou o comprimento de um arquivo, caso seja apenas um arquivo.

A `-s` opção produz um resumo agregado de comprimentos de arquivo que estão sendo exibidos.  
A `-h` opção formata os tamanhos de arquivo.

Exemplo:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>rm

O comando [-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) exclui os arquivos especificados como argumentos.

Exemplo:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]