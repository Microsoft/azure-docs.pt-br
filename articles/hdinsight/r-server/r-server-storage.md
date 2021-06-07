---
title: Soluções de armazenamento do Azure para serviços de ML no HDInsight – Azure
description: Conheça as diferentes opções de armazenamento disponíveis com ML Services no HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943906"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Soluções de armazenamento do Azure para serviços de ML no Azure HDInsight

Os serviços de ML no HDInsight podem usar diferentes soluções de armazenamento para manter dados, código ou objetos que contêm resultados da análise. Essas soluções incluem as seguintes opções:

- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/)

Você também tem a opção de acessar várias contas de armazenamento ou contêineres do Azure com o cluster HDInsight. O armazenamento de arquivos do Azure é uma opção de armazenamento de dados conveniente para uso no nó de borda que permite montar um compartilhamento de arquivos do armazenamento do Azure para, por exemplo, o sistema de arquivos do Linux. Mas os compartilhamentos de arquivos do Azure podem ser montados e usados por qualquer sistema que tenha um sistema operacional com suporte, como Windows ou Linux.

Ao criar um cluster Apache Hadoop no HDInsight, você especifica uma conta de **armazenamento de BLOBs do Azure** ou **Data Lake Storage Gen1**. Um contêiner de armazenamento específico dessa conta mantém o sistema de arquivos do cluster que você cria (por exemplo, o Sistema de Arquivos Distribuído Hadoop). Para obter mais informações e diretrizes, consulte:

- [Usar o armazenamento de Blobs do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Usar Data Lake Storage Gen1 com clusters HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Usar várias contas de Armazenamento de Blobs do Azure com o cluster do ML Services

Se você especificou mais de uma conta de armazenamento ao criar o cluster do ML Services, as instruções a seguir explicam como usar uma conta secundária para acessar dados e operações em um cluster do ML Services. Suponha as contas de armazenamento e contêiner a seguir: **storage1** e um contêiner padrão chamado **container1**, e **storage2** com **container2**.

> [!WARNING]  
> Para fins de desempenho, o cluster HDInsight é criado no mesmo datacenter que a conta de armazenamento primária especificada. Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Usar o armazenamento padrão com o ML Services no HDInsight

1. Usando um cliente SSH, conecte-se ao nó de borda do cluster. Para saber mais sobre como usar o SSH com clusters HDInsight, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copie um arquivo de exemplo, mysamplefile.csv, para o /diretório de compartilhamento.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Alterne para o R Studio ou outro console R e grave o código R para definir o nome do nó para **padrão** e o local do arquivo que você deseja acessar.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Todas as referências de diretório e arquivo apontam para a conta de armazenamento `wasbs://container1@storage1.blob.core.windows.net`. Essa é a **conta de armazenamento padrão** associada ao cluster do HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Usar o armazenamento adicional com o ML Services no HDInsight

Agora, suponha que você queira processar um arquivo chamado mysamplefile1.csv, localizado no /diretório particular do **container2** no **storage2**.

No código R, aponte a referência de nó de nome para a conta de armazenamento **storage2** .

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Todas as referências de diretório e arquivo agora apontam para a conta de armazenamento `wasbs://container2@storage2.blob.core.windows.net`. Esse é o **Nome de Nó** que você especificou.

Configure o `/user/RevoShare/<SSH username>` diretório em **storage2** da seguinte maneira:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Usar Azure Data Lake Storage Gen1 com cluster de serviços ML

Para usar Data Lake Storage Gen1 com o cluster HDInsight, você precisa dar acesso ao cluster para cada Azure Data Lake Storage Gen1 que você deseja usar. Para obter instruções sobre como usar o portal do Azure para criar um cluster HDInsight com um Azure Data Lake Storage Gen1 como o armazenamento padrão ou como armazenamento adicional, consulte [criar um cluster hdinsight com data Lake Storage Gen1 usando portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Em seguida, você usa o armazenamento no script R da mesma forma que fez com uma conta de armazenamento do Azure secundária, conforme descrito no procedimento anterior.

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Adicionar acesso de cluster ao seu Azure Data Lake Storage Gen1

Você acessa Data Lake Storage Gen1 usando uma entidade de serviço do Azure Active Directory (Azure AD) que está associada ao cluster HDInsight.

1. Ao criar o cluster HDInsight, selecione a **identidade do Azure AD do cluster** na guia **fonte de dados** .

2. Na caixa de diálogo **identidade do AD do Azure do cluster** , em **selecionar entidade de serviço do AD**, selecione **criar novo**.

Após fornecer um nome à Entidade de Serviço e criar uma senha para ela, clique em **Gerenciar Acesso de ADLS** para associar a Entidade de Serviço ao Data Lake Storage.

Também é possível adicionar acesso de cluster a uma ou mais contas de Gen1 de armazenamento de Data Lake após a criação do cluster. Abra a entrada de portal do Azure para uma Data Lake Storage Gen1 e vá para **Data Explorer > acessar > adicionar**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Como acessar o Data Lake Storage Gen1 pelos Serviços ML no HDInsight

Depois de ter concedido acesso ao Data Lake Storage Gen1, você poderá usar o armazenamento no cluster de serviços ML no HDInsight da forma como faria com uma conta de armazenamento do Azure secundária. A única diferença é que o prefixo **wasbs://** muda para **ADL://** da seguinte maneira:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Os comandos a seguir são usados para configurar o Data Lake Storage Gen1 com o diretório RevoShare e adicionar o arquivo. csv de exemplo do exemplo anterior:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Usar o Armazenamento de Arquivos do Azure com ML Services no HDInsight

Também há uma opção de armazenamento de dados conveniente para uso no nó de borda chamado [arquivos do Azure](https://azure.microsoft.com/services/storage/files/). Esse recurso permite montar um compartilhamento de arquivos do Armazenamento do Azure para o sistema de arquivos do Linux. Essa opção pode ser útil para armazenar arquivos de dados, scripts R e objetos de resultado que podem ser necessários posteriormente, especialmente quando fizer sentido usar o sistema de arquivos nativo no nó de borda em vez do HDFS.

Uma grande vantagem dos Arquivos do Azure é que os compartilhamentos de arquivos podem ser montados e usados por qualquer sistema que tenha um sistema operacional com suporte, como Windows ou Linux. Por exemplo, ele pode ser usado por outro cluster do HDInsight que você ou alguém em sua equipe tenha, por uma VM do Azure ou até mesmo por um sistema local. Para obter mais informações, consulte:

- [Como usar o armazenamento de arquivos do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Como utilizar o Armazenamento de Arquivos do Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do cluster do ML Services no HDInsight](r-server-overview.md)
- [Opções de contexto de computação para cluster do ML Services no HDInsight](r-server-compute-contexts.md)
- [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
