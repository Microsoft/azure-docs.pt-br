---
title: Carregar dados para trabalhos do Hadoop no HDInsight | Microsoft Docs
description: Saiba como carregar e acessar os dados de trabalhos do Hadoop no HDInsight usando o Azure CLI, Azure Storage Explorer, Azure PowerShell, a linha de comando do Hadoop ou o Sqoop.
keywords: hadoop de etl, inserindo dados no hadoop, carregar dados no hadoop
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 5c862dbf46a30dd6811faeea447d559dca493991
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos do Hadoop no HDInsight

O Azure HDInsight fornece um HDFS (sistema de arquivos distribuído) completo do Hadoop no Armazenamento do Azure e no Azure Data Lake Store. O Armazenamento do Azure e o Data Lake Store foram projetados como uma extensão do HDFS para fornecer uma experiência contínua para os clientes. Eles habilitam o conjunto completo de componentes no ecossistema do Hadoop para operar diretamente nos dados gerenciados por ele. O Armazenamento do Azure e o Data Lake Store são sistemas de arquivos distintos otimizados para armazenamento de dados e computação nesses dados. Para obter informações sobre os benefícios do uso do Armazenamento do Azure, consulte [Usar o Armazenamento do Azure com o HDInsight][hdinsight-storage] e [Usar o Data Lake Store com o HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Pré-requisitos

Observe os seguintes requisitos antes de começar:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conhecimento dos dois seguintes artigos:

    - [Usar o Armazenamento do Azure com HDInsight][hdinsight-storage]
    - [Usar o Data Lake Store com o HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Carregar dados no Armazenamento do Azure

### <a name="command-line-utilities"></a>Utilitários de linha de comando
A Microsoft fornece os seguintes utilitários para trabalhar com o Armazenamento do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interface de Linha de Comando do Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Comando do Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Embora a CLI do Azure, o Azure PowerShell e o AzCopy possam ser usados fora do Azure, o comando do Hadoop só está disponível no cluster HDInsight. Além disso, o comando apenas permite carregar dados do sistema de arquivos local para o Armazenamento do Azure.
>
>

#### <a id="xplatcli"></a>Azure CLI
O Azure CLI é uma ferramenta de plataforma cruzada que permite que você gerencie os serviços do Azure. Use as seguintes etapas para carregar dados no Armazenamento do Azure:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalar e configurar a CLI do Azure para Mac, Linux e Windows](../cli-install-nodejs.md).
2. Abra um prompt de comando, bash ou outro shell e use os dados a seguir para autenticar a sua assinatura do Azure.

    ```cli
    azure login
    ```

    Quando solicitado, insira o nome de usuário e senha para a sua assinatura.
3. Use o seguinte comando para listar sites para as contas de armazenamento da sua assinatura:

    ```cli
    azure storage account list
    ```

4. Selecione a conta de armazenamento que contém o blob com que você deseja trabalhar e use o seguinte comando para recuperar a chave para esta conta:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Esse comando retorna as chaves **Primária** e **Secundária**. Copie o valor de chave **Primária** , já que ele será usado nas próximas etapas.
5. Use o seguinte comando para recuperar uma lista de contêineres de blob na conta de armazenamento:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Use os seguintes comandos para carregar e baixar arquivos para o blob:

   * Para carregar um arquivo:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Para baixar um arquivo:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Se você sempre trabalha com a mesma conta de armazenamento, defina as seguintes variáveis de ambiente, em vez de especificar a conta e a chave para cada comando:
>
> * **AZURE\_STORAGE\_ACCOUNT**: o nome da conta de armazenamento
> * **AZURE\_STORAGE\_ACCESS\_KEY**: a chave da conta de armazenamento
>
>

#### <a id="powershell"></a>PowerShell do Azure
O Azure PowerShell é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para saber mais sobre como configurar sua estação de trabalho para executar o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Para carregar um arquivo local no Armazenamento do Azure**

1. Abra o console do PowerShell do Azure conforme instruído em [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).
2. Defina os valores das cinco primeiras variáveis no script a seguir:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Cole o script no console do Azure PowerShell para executá-lo para copiar o arquivo.

Por exemplo, os scripts do PowerShell criados para funcionar com o HDInsight, consulte [Ferramentas HDInsight](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
O AzCopy é uma ferramenta de linha de comando destinada a simplificar a tarefa de transferir dados de e para uma conta de Armazenamento do Azure. Você pode usá-lo como uma ferramenta independente ou incorporar essa ferramenta em um aplicativo existente. [Baixar o AzCopy][azure-azcopy-download].

A sintaxe do AzCopy é:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Para saber mais, consulte [AzCopy – Carregando/baixando arquivos para Blobs do Azure][azure-azcopy].

A versão prévia do AzCopy no Linux está disponível.  Consulte [Anunciando a versão prévia do AzCopy no Linux](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Linha de comando do Hadoop
A linha de comando do Hadoop só é útil para armazenar dados no Azure Storage Blob quando os dados já estão presentes no nó de cabeçalho do cluster.

Para usar o comando Hadoop, primeiro você deve se conectar ao nó de cabeçalho usando um dos seguintes métodos:

* **HDInsight baseado em Windows**: [conecte-se usando a área de trabalho remota](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight baseado em Linux**: conecte-se usando [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez conectado, você pode usar a seguinte sintaxe para carregar um arquivo no armazenamento.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivos padrão do HDInsight está no Armazenamento do Azure, /example/data.txt está, na verdade, no Armazenamento do Azure. Você também pode fazer referência ao arquivo como:

    wasb:///example/data/data.txt

ou o

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obter uma lista dos outros comandos Hadoop que trabalham com os arquivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> Em clusters HBase, o tamanho do bloco padrão usado na gravação de dados é de 256 KB. Embora isso funcione bem com APIs HBase ou APIs REST, o uso dos comandos `hadoop` ou `hdfs dfs` para gravar dados com mais de, aproximadamente, 12 GB resultará em um erro. Para obter mais informações, consulte a seção [Exceção de armazenamento para gravar no blob](#storageexception) neste artigo.
>
>

### <a name="graphical-clients"></a>Clientes gráficos
Também há vários aplicativos que fornecem uma interface gráfica para trabalhar com o Armazenamento do Azure. A seguinte tabela é uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas do Microsoft Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Ferramentas do Visual Studio para HDInsight
Para saber mais, veja [Navegar nos recursos vinculados](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

#### <a id="storageexplorer"></a>Gerenciador de Armazenamento do Azure
*Gerenciador de Armazenamento do Azure* é uma ferramenta útil para inspecionar e alterar os dados nos blobs. É uma ferramenta de software livre que pode ser baixada em [http://storageexplorer.com/](http://storageexplorer.com/). O código-fonte está disponível também neste link.

Para usar a ferramenta, conheça sua chave e seu nome da conta de armazenamento do Azure. Para ver instruções sobre como obter essas informações, consulte a seção “Como exibir, copiar e regenerar chaves de acesso de armazenamento” em [Criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storage-account].

1. Execute o Azure Storage Explorer. Se esta for a primeira vez que você executou o Gerenciador de Armazenamento, deverá inserir o **_Nome da conta de armazenamento** e a **Chave de conta de armazenamento**. Caso o tenha executado antes, use botão **Adicionar** para adicionar um novo nome e chave de conta de armazenamento.

    Insira o nome e a chave da conta de armazenamento usada pelo cluster HDInsight e, depois, selecione **SALVAR E ABRIR**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. Na lista de contêineres, clique no nome do contêiner que está associado ao cluster HDInsight. Por padrão, esse é o nome do cluster HDInsight, mas pode ser diferente se você inseriu um nome específico ao criar o cluster.
3. Na barra de ferramentas, selecione o ícone de upload.

    ![Barra de ferramentas com ícone de upload destacado](./media/hdinsight-upload-data/toolbar.png)
4. Especifique um arquivo para carregar e **Abrir**. Quando solicitado, selecione **Carregar** para carregar o arquivo para a raiz do contêiner de armazenamento. Se você desejar carregar o arquivo em um caminho específico, digite o caminho no campo **Destino** e selecione **Carregar**.

    ![Caixa de diálogo de upload do arquivo](./media/hdinsight-upload-data/fileupload.png)

    Depois que o arquivo terminar de carregar, você pode usá-lo por meio dos trabalhos no cluster HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Montar o Armazenamento do Azure como uma unidade local
Consulte [Montar o Armazenamento do Azure como uma unidade local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Carregar usando serviços
#### <a name="azure-data-factory"></a>Fábrica de dados do Azure
A Fábrica de Dados do Azure é um serviço completamente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificada, escalonável e confiável.

O Azure Data Factory pode ser usado para mover dados para o Armazenamento do Azure ou para criar pipelines de dados que usam diretamente os recursos do HDInsight como o Hive e o Pig.

Para obter mais informações, consulte a [Documentação do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
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

### <a name="troubleshooting"></a>Solucionar problemas
#### <a id="storageexception"></a>Exceção de armazenamento para gravar no blob
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

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando Ambari. As etapas a seguir podem ser usadas para alterar o valor na interface de usuário do Ambari Web:

1. No navegador, acesse a interface de usuário do Ambari Web para seu cluster. Isto é, https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do seu cluster.

    Quando solicitado, insira o nome de administrador e a senha de administrador para o cluster.
2. No lado esquerdo da tela, escolha **HDFS** e selecione a guia **Configurações**.
3. No campo **Filtrar...**, insira `fs.azure.write.request.size`. Isso exibirá o campo e o valor atual no meio da página.
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

![Imagem de alteração de valor por meio da interface de usuário do Ambari Web](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para saber mais sobre como usar o Ambari, confira [Gerenciar clusters HDInsight interface de usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você compreende como obter dados no HDInsight, leia os seguintes artigos para aprender a executar uma análise:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Enviar trabalhos Hadoop de forma programática][hdinsight-submit-jobs]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
