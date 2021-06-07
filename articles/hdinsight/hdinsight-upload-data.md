---
title: Carregar dados para trabalhos do Apache Hadoop no HDInsight
description: Saiba como carregar e acessar dados para Apache Hadoop trabalhos no HDInsight. Use a CLI clássica do Azure, Gerenciador de Armazenamento do Azure, Azure PowerShell, a linha de comando do Hadoop ou Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931342"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos do Apache Hadoop no HDInsight

O HDInsight fornece um HDFS (sistema de arquivos distribuído Hadoop) no armazenamento do Azure e Azure Data Lake Storage. Esse armazenamento inclui Gen1 e Gen2. O armazenamento do Azure e o Data Lake Storage Gen1 e o Gen2 são projetados como extensões HDFS. Eles permitem que o conjunto completo de componentes no ambiente do Hadoop opere diretamente nos dados gerenciados por ele. O armazenamento do Azure, Data Lake Storage Gen1 e Gen2 são sistemas de arquivos distintos. Os sistemas são otimizados para armazenamento de dados e computações nesses dados. Para obter informações sobre os benefícios de usar o armazenamento do Azure, consulte [usar o armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Consulte também, [use data Lake Storage Gen1 com o hdinsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)e [use o data Lake Storage Gen2 com o hdinsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Pré-requisitos

Observe os seguintes requisitos antes de começar:

* Um cluster Azure HDInsight. Para obter instruções, consulte Introdução [ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Conhecimento dos seguintes artigos:
    * [Usar o armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Usar o Data Lake Storage Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Usar o Data Lake Storage Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Carregar dados no Armazenamento do Azure

### <a name="utilities"></a>Utilitários

A Microsoft fornece os seguintes utilitários para trabalhar com o Armazenamento do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [PowerShell do Azure](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comando do Hadoop](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> O comando do Hadoop só está disponível no cluster HDInsight. O comando apenas permite carregar dados do sistema de arquivos local para o Armazenamento do Microsoft Azure.  

### <a name="hadoop-command-line"></a>Linha de comando do Hadoop

A linha de comando do Hadoop só é útil para armazenar dados no Azure Storage Blob quando os dados já estão presentes no nó de cabeçalho do cluster.

Para usar o comando do Hadoop, primeiro você deve se conectar ao cabeçalho usando [SSH ou](hdinsight-hadoop-linux-use-ssh-unix.md)de saída.

Uma vez conectado, você pode usar a seguinte sintaxe para carregar um arquivo no armazenamento.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivos padrão do HDInsight está no armazenamento do Azure, o/example/data/data.txt está na verdade no armazenamento do Azure. Você também pode fazer referência ao arquivo como:

`wasbs:///example/data/data.txt`

ou

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Para obter uma lista de outros comandos do Hadoop que funcionam com arquivos, consulte [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Em clusters do Apache HBase, o tamanho do bloco padrão usado na gravação de dados é de 256 KB. Embora isso funcione bem com APIs HBase ou APIs REST, o uso dos comandos `hadoop` ou `hdfs dfs` para gravar dados com mais de, aproximadamente, 12 GB resultará em um erro. Para obter mais informações, consulte [exceção de armazenamento para gravação no blob](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Clientes gráficos

Também há vários aplicativos que fornecem uma interface gráfica para trabalhar com o Armazenamento do Azure. A seguinte tabela é uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas do Microsoft Visual Studio para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Gerenciador de Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer para Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Montar o Armazenamento do Azure como uma unidade local

Consulte [Montar o Armazenamento do Azure como uma unidade local](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Carregar usando serviços

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O serviço de Azure Data Factory é um serviço totalmente gerenciado para compor dados: armazenamento, processamento e movimentação de serviços em pipelines de produção de dados simplificados, adaptáveis e confiáveis.

|Tipo de armazenamento|Documentação|
|----|----|
|Armazenamento de Blobs do Azure|[Copiar dados de/para o Armazenamento de Blobs do Azure usando o Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Copiar dados de/para o Azure Data Lake Storage Gen1 usando o Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

O Sqoop é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e o Hadoop. Use-o para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle. Em seguida, no sistema de arquivos distribuído Hadoop (HDFS). Transforme os dados no Hadoop com MapReduce ou Hive e, em seguida, exporte os dados de volta para um RDBMS.

Para obter mais informações, consulte [Usar Sqoop com HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDKs de desenvolvimento

O Armazenamento do Azure também pode ser acessado usando um SDK do Azure com as seguintes linguagens de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [Downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Próximas etapas

Agora que você entende como obter dados no HDInsight, leia os seguintes artigos para aprender a analisar:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Enviar trabalhos do Apache Hadoop de forma programática](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)