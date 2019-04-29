---
title: Carregar dados para trabalhos do Apache Hadoop no HDInsight
description: Aprenda como carregar e acessar dados para trabalhos do Apache Hadoop no HDInsight usando a CLI clássica do Azure, o Gerenciador de Armazenamento do Microsoft Azure, o Azure PowerShell, a linha de comando do Hadoop ou o Sqoop.
keywords: hadoop de etl, inserindo dados no hadoop, carregar dados no hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 3283c885956c5b43171c6287dc00efa9a82db28e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763917"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos do Apache Hadoop no HDInsight

O HDInsight do Azure fornece um sistema completo de arquivos distribuídos do Hadoop (HDFS) sobre o Armazenamento do Microsoft Azure e o Armazenamento do Azure Data Lake (Gen1 e Gen2). O Armazenamento do Microsoft Azure e o Data Lake Storage Gen1 e Gen2 são projetados como extensões do HDFS para fornecer uma experiência perfeita aos clientes. Eles habilitam o conjunto completo de componentes no ecossistema do Hadoop para operar diretamente nos dados gerenciados por ele. Armazenamento do Microsoft Azure, Data Lake Storage Gen1 e Gen2 são sistemas de arquivos distintos que são otimizados para armazenamento de dados e cálculos nesses dados. Para saber mais sobre os benefícios de usar o Armazenamento do Microsoft Azure, confira [Usar o Armazenamento do Microsoft Azure com o HDInsight][hdinsight-storage], [Usar o Data Lake Storage Gen1 com o HDInsight](hdinsight-hadoop-use-data-lake-store.md) e [Usar o Data Lake Storage Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Pré-requisitos

Observe os seguintes requisitos antes de começar:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conhecimento dos seguintes artigos:

    - [Usar o Armazenamento do Azure com HDInsight][hdinsight-storage]
    - [Usar o Data Lake Storage Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Usar o Data Lake Storage Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Carregar dados no Armazenamento do Azure

## <a name="utilities"></a>Utilidades
A Microsoft fornece os seguintes utilitários para trabalhar com o Armazenamento do Azure:

| Ferramenta | Linux | OS X |  Windows |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [PowerShell do Azure](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comando do Hadoop](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> O comando do Hadoop só está disponível no cluster HDInsight. O comando apenas permite carregar dados do sistema de arquivos local para o Armazenamento do Microsoft Azure.  


## <a id="commandline"></a>Linha de comando do Hadoop
A linha de comando do Hadoop só é útil para armazenar dados no Azure Storage Blob quando os dados já estão presentes no nó de cabeçalho do cluster.

Para usar o comando do Hadoop, você deve primeiro se conectar ao nó principal usando [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez conectado, você pode usar a seguinte sintaxe para carregar um arquivo no armazenamento.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivos padrão do HDInsight está no Armazenamento do Azure, /example/data.txt está, na verdade, no Armazenamento do Azure. Você também pode fazer referência ao arquivo como:

    wasb:///example/data/data.txt

ou o

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obter uma lista de outros comandos de Hadoop que trabalham com arquivos, consulte [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Em clusters do Apache HBase, o tamanho do bloco padrão usado na gravação de dados é de 256 KB. Embora isso funcione bem com APIs HBase ou APIs REST, o uso dos comandos `hadoop` ou `hdfs dfs` para gravar dados com mais de, aproximadamente, 12 GB resultará em um erro. Para obter mais informações, consulte a seção [Exceção de armazenamento para gravar no blob](#storageexception) neste artigo.

## <a name="graphical-clients"></a>Clientes gráficos
Também há vários aplicativos que fornecem uma interface gráfica para trabalhar com o Armazenamento do Azure. A seguinte tabela é uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X |  Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas do Microsoft Visual Studio para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Gerenciador de Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer para Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Montar o Armazenamento do Azure como uma unidade local
Consulte [Montar o Armazenamento do Azure como uma unidade local](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Carregar usando serviços
### <a name="azure-data-factory"></a>Fábrica de dados do Azure
A Fábrica de Dados do Azure é um serviço completamente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificada, escalonável e confiável.

|Tipo de armazenamento|Documentação|
|----|----|
|Armazenamento de Blobs do Azure|[Copiar dados de/para o Armazenamento de Blobs do Azure usando o Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Copiar dados de/para o Azure Data Lake Storage Gen1 usando o Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
O Sqoop é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e o Hadoop. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com o MapReduce ou o Hive e, em seguida, exportar os dados de volta para um RDBMS.

Para obter mais informações, consulte [Usar Sqoop com HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>SDKs de desenvolvimento
O Armazenamento do Azure também pode ser acessado usando um SDK do Azure com as seguintes linguagens de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [Downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>solução de problemas
### <a id="storageexception"></a>Exceção de armazenamento para gravar no blob
**Sintomas**: ao usar os comandos `hadoop` ou `hdfs dfs` para gravar arquivos que têm aproximadamente 12 GB ou mais em um cluster HBase, você pode encontrar o seguinte erro:

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

**Causa**: os clusters HBase no HDInsight são padronizados para um tamanho de bloco de 256 KB na gravação no armazenamento do Azure. Embora isso funcione para APIs HBase ou APIs REST, isso resultará em um erro ao usar os utilitários de linha de comando `hadoop` ou `hdfs dfs`.

**Resolução**: use `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Isso pode ser feito a cada uso com o parâmetro `-D`. O seguinte comando é um exemplo de uso desse parâmetro com o comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando o Apache Ambari. As etapas a seguir podem ser usadas para alterar o valor na interface de usuário do Ambari Web:

1. No navegador, acesse a interface de usuário do Ambari Web para seu cluster. Isto é https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster.

    Quando solicitado, insira o nome de administrador e a senha de administrador para o cluster.
2. No lado esquerdo da tela, escolha **HDFS** e selecione a guia **Configurações**.
3. No campo **Filtrar...**, insira `fs.azure.write.request.size`. Isso exibirá o campo e o valor atual no meio da página.
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

![Imagem de alteração de valor por meio da interface de usuário do Ambari Web](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para saber mais sobre como usar o Ambari, confira [Gerenciar clusters HDInsight interface do usuário Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você compreende como obter dados no HDInsight, leia os seguintes artigos para aprender a executar uma análise:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Enviar trabalhos do Apache Hadoop de forma programática][hdinsight-submit-jobs]
* [Usar o Apache Hive com HDInsight][hdinsight-use-hive]
* [Usar Apache Pig com o HDInsight][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
