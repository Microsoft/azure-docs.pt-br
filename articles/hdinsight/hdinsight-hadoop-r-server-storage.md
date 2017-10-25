---
title: "Soluções de Armazenamento do Azure para R Server no HDInsight – Azure | Microsoft Docs"
description: "Conheça as diferentes opções de armazenamento disponíveis para usuários com o Servidor R no HDInsight"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: aef9c15636ccaecce07d4fa218a40ed26ebad9df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>Soluções de Armazenamento do Azure para R Server no HDInsight

O Microsoft R Server no HDInsight tem uma variedade de soluções de armazenamento para manter os dados, código ou objetos que contêm resultados da análise. Elas incluem as seguintes opções:

- [Blob do Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)
- [Armazenamento de Arquivos do Azure](https://azure.microsoft.com/services/storage/files/)

Você também tem a opção de acessar várias contas de armazenamento ou contêineres do Azure com o cluster HDI. O Armazenamento de Arquivos do Azure é uma opção de armazenamento de dados conveniente para uso no nó de borda, que permite a você montar um compartilhamento de arquivos do Armazenamento do Azure para, por exemplo, o sistema de arquivos do Linux. Mas os compartilhamentos de arquivos do Azure podem ser montados e usados por qualquer sistema que tenha um SO com suporte, como Windows ou Linux. 

Ao criar um cluster Hadoop no HDInsight, você especifica uma conta de **Armazenamento do Azure** ou um **Data Lake Store**. Um contêiner de armazenamento específico dessa conta mantém o sistema de arquivos do cluster que você cria (por exemplo, o Sistema de Arquivos Distribuído Hadoop). Para obter mais informações e diretrizes, consulte:

- [Usar o Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md)
- [Usar o Data Lake Store com clusters Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). 

Para obter mais informações sobre as soluções de Armazenamento do Azure, consulte: [Introdução ao Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md). 

Para obter diretrizes sobre como selecionar a opção de armazenamento mais apropriada para usar em seu cenário, consulte [Decidindo quando usar Blobs do Azure, Arquivos do Azure ou Discos de Dados do Azure](../storage/common/storage-decide-blobs-files-disks.md) 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>Usar várias contas de Armazenamento de Blobs do Azure com o R Server

Se necessário, você pode acessar várias contas de armazenamento ou contêineres do Azure com o cluster HDI. Para isso, você precisa especificar as contas de armazenamento adicionais a interface do usuário ao criar o cluster e, depois, seguir estas etapas para usá-las no R Server.

> [!WARNING]
> Para fins de desempenho, o cluster HDInsight é criado no mesmo datacenter que a conta de armazenamento primária especificada. Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight.

1. Crie um cluster HDInsight com um nome de conta de armazenamento de **storage1** e um contêiner padrão chamado **container1**.
2. Especifique uma conta de armazenamento adicional chamada **storage2**.  
3. Copie o arquivo mycsv.csv para o diretório /share e execute a análise nesse arquivo.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. No código R, defina o nó do nome como **default** e defina o diretório e arquivo a serem processados.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Todas as referências de diretório e arquivo apontam para a conta de armazenamento wasb://container1@storage1.blob.core.windows.net. Essa é a **conta de armazenamento padrão** associada ao cluster do HDInsight.

Agora, suponha que você queira processar um arquivo chamado mySpecial.csv, localizado no diretório /private de **container2** em **storage2**.

No código R, aponte a referência de nó de nome para a conta de armazenamento **storage2** .


    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
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
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Todas as referências de diretório e arquivo agora apontam para a conta de armazenamento wasb://container2@storage2.blob.core.windows.net. Esse é o **Nome de Nó** que você especificou.

Você precisa configurar o diretório /user/RevoShare/<SSH username> no **storage2** da seguinte maneira:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>Usar um Azure Data Lake Store com o R Server

Para usar repositórios Data Lake com sua conta do HDInsight, você precisa dar ao cluster acesso a cada repositório do Azure Data Lake que deseja usar. Para obter instruções sobre como usar o Portal do Azure pra criar um cluster HDInsight com uma conta do Azure Data Lake Store como armazenamento padrão ou um repositório adicional, veja [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Você usa o repositório no script R de forma semelhante a como você fez com uma conta de armazenamento do Azure secundária, conforme descrito no procedimento anterior.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Adicionar acesso de cluster aos repositórios Azure Data Lake
Você pode acessar um repositório Data Lake usando uma Entidade de Serviço do Azure AD (Azure Active Directory) que está associada ao cluster do HDInsight.

Para adicionar uma Entidade de Serviço do Azure AD:

1. Ao criar o cluster do HDInsight, selecione **Identidade do Cluster AAD** na guia **Fonte de Dados**.

2. Na caixa de diálogo **Identidade de Cluster AAD**, em **Selecionar Entidade de Serviço do AD**, selecione **Criar novo**.

Depois de dar um nome à Entidade de Serviço e criar uma senha para ela, clique em **Gerenciar Acesso de ADLS** para associar a Entidade de Serviço aos seus repositórios Data Lake Store.

Também é possível adicionar o acesso do cluster a um ou mais Data Lake Stores após a criação do cluster. Abra a entrada do Portal do Azure para um Data Lake Store e vá para **Data Explorer > Acessar > Adicionar**. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>Como acessar o Data Lake Store do R Server

Após obter acesso a um repositório Data Lake, você pode usar o repositório no Servidor R no HDInsight da forma como faria com uma conta de armazenamento do Azure secundária. A única diferença é que o prefixo **wasb://** se altera para **adl://** como segue:


    # Point to the ADL store (e.g. ADLtest)
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
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


Os comandos a seguir são usados para configurar a conta de armazenamento Data Lake com o diretório RevoShare e adicionar o arquivo .csv de exemplo do exemplo anterior:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>Usar o Armazenamento de Arquivos do Azure com o R Server

Também há uma opção de armazenamento de dados conveniente para uso no nó de borda, chamada [Arquivos do Azure]((https://azure.microsoft.com/services/storage/files/). Esse recurso permite montar um compartilhamento de arquivos do Armazenamento do Azure para o sistema de arquivos do Linux. Essa opção pode ser útil para armazenar arquivos de dados, scripts R e objetos de resultado que podem ser necessários posteriormente, especialmente quando fizer sentido usar o sistema de arquivos nativo no nó de borda em vez do HDFS. 

Uma grande vantagem dos Arquivos do Azure é que os compartilhamentos de arquivos podem ser montados e usados por qualquer sistema que tenha um sistema operacional com suporte, como Windows ou Linux. Por exemplo, ele pode ser usado por outro cluster do HDInsight que você ou alguém em sua equipe tenha, por uma VM do Azure ou até mesmo por um sistema local. Para obter mais informações, confira:

- [Como utilizar o Armazenamento de Arquivos do Azure com Linux](../storage/files/storage-how-to-use-files-linux.md)
- [Como utilizar o Armazenamento de Arquivos do Azure no Windows](../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Próximas etapas

Agora que você entende as opções de armazenamento do Azure, use os links a seguir para descobrir maneiras de realizar tarefas de ciência de dados com o R Server no HDInsight.

* [Visão geral do Servidor R no HDInsight](hdinsight-hadoop-r-server-overview.md)
* [Get started with R server on Hadoop (Introdução ao servidor R no Hadoop)](hdinsight-hadoop-r-server-get-started.md)
* [Adicionar Servidor do RStudio ao HDInsight (se não instalado durante a criação de cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opções de contexto de computação para o Servidor R no HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

