---
title: Arquitetura do Apache Hadoop - Azure HDInsight
description: Descreve Apache Hadoop armazenamento e processamento em clusters HDInsight do Azure.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: f69917b574c0be8fdacf05934b05544a4f491a9c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864186"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Arquitetura do Apache Hadoop no HDInsight

[Apache Hadoop](https://hadoop.apache.org/) inclui dois componentes principais: o [Sistema de Arquivos Distribuídos do Apache Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) que fornece armazenamento, e o [Apache Hadoop ainda Outro Negociador de Recursos (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) que fornece em processamento. Com os recursos de armazenamento e processamento, um cluster torna-se capaz de executar programas [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) para executar o processamento de dados desejado.

> [!NOTE]  
> Normalmente, um HDFS não é implantado no Cluster HDInsight para fornecer armazenamento. Em vez disso, uma camada de interface compatível com HDFS é utilizada pelos componentes do Hadoop. A capacidade de armazenamento real é fornecida pelo Armazenamento do Azure ou pelo Azure Data Lake Storage. Para o Hadoop, os trabalhos MapReduce executados no Cluster HDInsight funcionam como se um HDFS estivesse presente e, portanto, não exigem alterações para dar suporte às necessidades de armazenamento. No Hadoop, no HDInsight, o armazenamento é terceirizado, mas o processamento YARN continua sendo um componente principal. Para obter mais informações, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Este artigo apresenta o YARN e como ele coordena a execução de aplicativos no HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Noções básicas do Apache Hadoop YARN

YARN governa e orquestra o processamento de dados no Hadoop. O YARN possui dois serviços principais que são executados como processos em nós no cluster:

* ResourceManager
* NodeManager

O ResourceManager concede recursos de computação de cluster para aplicativos como trabalhos MapReduce. O ResourceManager concede esses recursos como contêineres, onde cada contêiner consiste de uma alocação de núcleos de CPU e memória RAM. Se você combinou todos os recursos disponíveis em um cluster e distribuiu os núcleos e a memória em blocos, cada bloco de recursos será um contêiner. Cada nó no cluster tem uma capacidade para um determinado número de contêineres, portanto, o cluster tem um limite fixo no número de contêineres disponíveis. A alocação de recursos em um contêiner é configurável.

Quando um aplicativo MapReduce é executado em um cluster, o ResourceManager fornece ao aplicativo os contêineres a executar. O ResourceManager rastreia o status de aplicativos em execução, a capacidade de cluster disponível e rastreia aplicativos conforme conclui e libera seus recursos.

O ResourceManager também executa um processo do servidor Web que fornece uma interface do usuário da Web para monitorar o status dos aplicativos.

Quando um usuário envia um aplicativo MapReduce para ser executado no cluster, o aplicativo é submetido ao ResourceManager. Por sua vez, o ResourceManager aloca um contêiner nos nós NodeManager disponíveis. Os nós NodeManager são onde o aplicativo realmente executa. O primeiro contêiner alocado executa um aplicativo especial chamado ApplicationMaster. Esse ApplicationMaster é responsável pela aquisição de recursos, sob a forma de contêineres subsequentes, necessários para executar o aplicativo enviado. O ApplicationMaster examina os estágios do aplicativo, como o estágio do mapa e o estágio de redução, e os fatores em quantos dados precisam ser processados. Em seguida, o ApplicationMaster solicita (*negocia*) os recursos do ResourceManager em nome do aplicativo. O ResourceManager, por sua vez, concede recursos do NodeManagers no cluster ao ApplicationMaster para que ele seja usado na execução do aplicativo.

O NodeManagers executa as tarefas que compõem o aplicativo e, em seguida, relata seu progresso e status ao ApplicationMaster. O ApplicationMaster, por sua vez, relata o status do aplicativo de volta ao ResourceManager. O ResourceManager retorna quaisquer resultados para o cliente.

## <a name="yarn-on-hdinsight"></a>YARN no HDInsight

Todos os tipos de Cluster HDInsight implantam o YARN. O ResourceManager é implantado para alta disponibilidade com uma instância primária e secundária, que é executada no primeiro e no segundo nós principais no cluster, respectivamente. Apenas uma instância do ResourceManager é ativa por vez. As instâncias do NodeManager são executadas nos nós de trabalho disponíveis no cluster.

:::image type="content" source="./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png" alt-text="Apache YARN no Azure HDInsight" border="false":::

## <a name="soft-delete"></a>Exclusão reversível

Para restaurar um arquivo da sua conta de armazenamento, consulte:

### <a name="azure-storage"></a>Armazenamento do Azure

* [Exclusão reversível para blobs do Armazenamento do Azure ](../storage/blobs/soft-delete-blob-overview.md)
* [Restaurar blob](/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

[Restore-AzDataLakeStoreDeletedItem](/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

[Problemas conhecidos com o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Descarte de lixo

A `fs.trash.interval` Propriedade do **HDFS**  >  **Advanced Core-site** deve permanecer no valor padrão `0` porque você não deve armazenar nenhum dado no sistema de arquivos local. Esse valor não afeta as contas de armazenamento remoto (WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Próximas etapas

* [Usar MapReduce no Apache Hadoop em HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)