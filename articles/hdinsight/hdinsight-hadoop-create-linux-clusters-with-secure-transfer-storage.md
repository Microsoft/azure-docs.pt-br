---
title: Criar um cluster Hadoop com contas de armazenamento para transferência segura no Azure HDInsight
description: Saiba como criar clusters HDInsight com contas de armazenamento do Azure com transferência segura habilitada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: 72e8b1331005db59be572e033bfaaaf5ceeea0b3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433601"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Criar um cluster Apache Hadoop com contas de armazenamento de transferência segura no Azure HDInsight

O recurso [Transferência segura exigida](../storage/common/storage-require-secure-transfer.md) aprimora a segurança de sua conta de Armazenamento do Azure aplicando todas as solicitações à sua conta por meio de uma conexão segura. Esse recurso e o esquema wasbs só têm suporte da versão 3.6 ou mais recente do cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter:

* **Assinatura do Azure**: Para criar uma conta de avaliação gratuita de um mês, acesse [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Uma conta de Armazenamento do Azure com transferência segura habilitada**. Para obter as instruções, consulte [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) e [Exigir transferência segura](../storage/common/storage-require-secure-transfer.md).
* **Um contêiner de Blob na conta de armazenamento**.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Nesta seção, você criará um cluster Hadoop no HDInsight usando um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). O modelo está localizado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Não é necessário ter experiência com o modelo do Resource Manager para acompanhar este artigo. Para outros métodos de criação do cluster e Noções básicas sobre as propriedades usadas neste artigo, consulte [clusters do HDInsight criar](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para entrar no Azure e abra o modelo do Resource Manager no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Siga as instruções para criar o cluster com as seguintes especificações: 

    - Especifique o HDInsight versão 3.6. É necessário ter a versão 3.6 ou mais recente.
    - Especifique uma conta de armazenamento com transferência segura habilitada.
    - Use um nome curto para a conta de armazenamento.
    - A conta de armazenamento e o contêiner de blob devem ser criados com antecedência.

      Para obter as instruções, confira [Criar cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Se você usar ação de script para fornecer seus próprios arquivos de configuração, use wasbs nas seguintes configurações:

- fs.defaultFS (site principal)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Adicionar outras contas de armazenamento

Há várias opções para adicionar outras contas de armazenamento com transferência segura habilitada:

- Modifique o modelo do Azure Resource Manager na última seção.
- Crie um cluster usando o [Portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento vinculada.
- Use ações de script para adicionar outras contas de armazenamento com transferência segura habilitada a um cluster existente do HDInsight. Para saber mais, confira [Adicionar outras contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a criar um cluster HDInsight e habilitar a transferência segura para as contas de armazenamento.

Para saber mais sobre como analisar dados com o HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como usar [Apache Hive](https://hive.apache.org/) com o HDInsight, inclusive como executar consultas do Hive do Visual Studio, consulte [usar o Apache Hive com HDInsight][hdinsight-use-hive].
* Para saber mais sobre [Apache Pig](https://pig.apache.org/), uma linguagem usada para transformar dados, consulte [usar o Apache Pig com o HDInsight][hdinsight-use-pig].
* Para saber mais sobre [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), uma maneira de gravar programas que processam dados no Hadoop, consulte [usar o Apache Hadoop MapReduce com HDInsight][hdinsight-use-mapreduce].
* Para saber mais sobre como usar as Ferramentas do HDInsight para Visual Studio, consulte [Introdução ao uso das ferramentas do Apache Hadoop do Visual Studio para o HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Para saber mais sobre como o HDInsight armazena dados, ou como inserir os dados no HDInsight, consulte os artigos a seguir:

* Para saber mais sobre como o HDInsight usa o Armazenamento do Azure, veja [Usar Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight][hdinsight-upload-data].

Para saber mais sobre como criar ou gerenciar um cluster do HDInsight, consulte os artigos a seguir:

* Para saber mais sobre como gerenciar o cluster HDInsight baseado em Linux, confira [Manage HDInsight clusters using Apache Ambari](hdinsight-hadoop-manage-ambari.md) (Gerenciar clusters HDInsight usando o Apache Ambari).
* Para saber mais sobre as opções que você pode selecionar ao criar um cluster HDInsight, confira [Como criar o HDInsight no Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).
* Se você estiver familiarizado com o Linux, e Apache Hadoop, mas quiser obter informações específicas sobre o Hadoop no HDInsight, consulte [Trabalhar com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Este artigo oferece informações como:

  * URLs para serviços hospedados no cluster, como [Apache Ambari](https://ambari.apache.org/) e [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * O local dos arquivos do [Apache Hadoop](https://hadoop.apache.org/) e exemplos no sistema de arquivos local
  * O uso do Armazenamento do Azure (WASB) em vez de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como armazenamento de dados padrão

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
