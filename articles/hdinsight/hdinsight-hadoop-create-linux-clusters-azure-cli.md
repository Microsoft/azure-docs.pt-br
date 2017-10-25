---
title: "Criar clusters Hadoop usando a linha de comando – Azure HDInsight | Microsoft Docs"
description: Saiba como criar clusters do HDInsight usando a CLI do Azure 1.0 de plataforma cruzada.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: larryfr
ms.openlocfilehash: 791edd4e56c7957458d49f6f3bd87b67e96db7a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar os clusters do HDInsight usando a CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

As etapas deste documento são um passo a passo para a criação de um cluster HDInsight 3.5 usando a CLI do Azure 1.0.

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **CLI do Azure**. As etapas neste documento foram testadas pela última vez com a versão 0.10.14 da CLI do Azure.

    > [!IMPORTANT]
    > As etapas neste documento não funcionam com a CLI do Azure 2.0. A CLI do Azure 2.0 não dá suporte à criação de um cluster HDInsight.

## <a name="log-in-to-your-azure-subscription"></a>Entre na sua assinatura do Azure

Siga as etapas documentadas em [Conectar a uma assinatura do Azure por meio da CLI do Azure (Interface de Linha de Comando do Azure)](../xplat-cli-connect.md) e conecte à sua assinatura usando o método de **logon** .

## <a name="create-a-cluster"></a>Criar um cluster

As etapas a seguir devem ser executadas de uma linha de comando, como o PowerShell ou o Bash.

1. Use o seguinte comando para fazer logon em sua assinatura do Azure:

        azure login

    Você receberá uma solicitação para fornecer seu nome e senha. Se você tiver várias assinaturas do Azure, use `azure account set <subscriptionname>` para definir a assinatura que será usada pelos comandos da CLI do Azure.

2. Alterne para modo Gerenciador de Recursos do Azure usando o seguinte comando:

        azure config mode arm

3. Crie um grupos de recursos. Esse grupo de recursos contém o cluster HDInsight e a conta de armazenamento associada.

        azure group create groupname location

    * Substitua `groupname` por um nome exclusivo para o grupo.

    * Substitua `location` pela região geográfica na qual você deseja criar um grupo.

       Para obter uma lista de locais válidos, use o comando `azure location list` e, então, utilize um dos locais da coluna `Name`.

4. Criar uma conta de armazenamento. Essa conta de armazenamento é usada como o armazenamento padrão do cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Substitua `groupname` pelo nome do grupo criado na etapa anterior.

    * Substitua `location` pelo mesmo local usado na etapa anterior.

    * Substitua `storagename` por um nome exclusivo para a conta de armazenamento.

        > [!NOTE]
        > Para saber mais sobre os parâmetros usados nesse comando, use `azure storage account create -h` para exibir a ajuda deste comando.

5. Recupere a chave usada para acessar a conta de armazenamento.

        azure storage account keys list -g groupname storagename

    * Substitua `groupname` pelo nome do grupo de recursos.
    * Substitua `storagename` pelo nome da conta de armazenamento.

     Nos dados retornados, salve o valor `key` para `key1`.

6. Crie um cluster HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Substitua `groupname` pelo nome do grupo de recursos.

    * Substitua `Hadoop` pelo tipo de cluster que você deseja criar. Por exemplo, `Hadoop`, `HBase`, `Kafka`, `Spark` ou `Storm`.

     > [!IMPORTANT]
     > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster.

    * Substitua `location` pelo mesmo local usado nas etapas anteriores.

    * Substitua `storagename` pelo nome da conta de armazenamento.

    * Substitua `storagekey` pela chave obtida na etapa anterior.

    * Como parâmetro `--defaultStorageContainer` , use o mesmo nome que você está usando para o cluster.

    * Substitua `admin` e `httppassword` pelo nome e pela senha que você deseja usar quando acessar o cluster por meio de HTTPS.

    * Substitua `sshuser` e `sshuserpassword` pelo nome de usuário e senha que você deseja usar quando acessar o cluster usando SSH

    > [!IMPORTANT]
    > Este exemplo cria um cluster com duas anotações de trabalho. Você também pode alterar o número de nós de trabalho após a criação do cluster realizando operações de colocação em escala. Se você planeja usar mais de 32 nós de trabalho, será necessário selecionar um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM. Você pode definir o tamanho do nó principal usando o parâmetro `--headNodeSize` durante a criação do cluster.
    >
    > Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Pode levar vários minutos para que o processo de criação de cluster seja concluído. Geralmente, cerca de 15 minutos.

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster HDInsight usando a CLI do Azure, use o seguinte para aprender a trabalhar com o seu cluster:

### <a name="hadoop-clusters"></a>Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
