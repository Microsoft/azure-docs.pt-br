---
title: Criar Azure HDInsight-Azure Data Lake Storage Gen2-Portal
description: Saiba como usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight usando o Portal.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: d54c005ffb17571be172b5716723febb742253a3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945392"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Criar um cluster com Data Lake Storage Gen2 usando o portal do Azure

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá como criar clusters Azure HDInsight baseados no Linux usando o portal. Detalhes adicionais estão disponíveis em [Criar clusters HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para criar um cluster HDInsight que usa Data Lake Storage Gen2 para armazenamento, siga estas etapas para configurar uma conta de armazenamento que tenha um namespace hierárquico.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma identidade gerenciada atribuída pelo usuário, se ainda não tiver uma.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No canto superior esquerdo, **crie um recurso**.
1. Na caixa de pesquisa, digite **usuário atribuído** e clique em **identidade gerenciada atribuída pelo usuário**.
1. Clique em **Criar**.
1. Insira um nome para sua identidade gerenciada, selecione a assinatura, o grupo de recursos e o local corretos.
1. Clique em **Criar**.

Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](hdinsight-managed-identities.md).

![Criar uma identidade gerenciada atribuída ao usuário](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Criar uma conta de armazenamento para usar com Data Lake Storage Gen2

Crie uma conta de armazenamento para usar com Azure Data Lake Storage Gen2.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No canto superior esquerdo, **crie um recurso**.
1. Na caixa de pesquisa, digite **armazenamento** e clique em **conta de armazenamento**.
1. Clique em **Criar**.
1. Na tela **criar conta de armazenamento** :
    1. Selecione a assinatura e o grupo de recursos corretos.
    1. Insira um nome para sua conta de armazenamento com Data Lake Storage Gen2.
    1. Clique na guia **avançado** .
    1. Clique em **habilitado** ao lado de **namespace hierárquico** em **Data Lake Storage Gen2**.
    1. Clique em **Revisar + Criar**.
    1. Clique em **Criar**

Para obter mais informações sobre outras opções durante a criação da conta de armazenamento, consulte [início rápido: criar uma conta de armazenamento para Azure data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

![Captura de tela mostrando a criação da conta de armazenamento no portal do Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Configurar permissões para a identidade gerenciada no Data Lake Storage Gen2

Atribua a identidade gerenciada à função de **proprietário de dados do blob de armazenamento** na conta de armazenamento.

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.
1. Selecione sua conta de armazenamento e, em seguida, selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para a conta. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    ![Captura de tela mostrando as configurações de controle de acesso de armazenamento](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Selecione o botão **+ Adicionar atribuição de função** para adicionar uma nova função.
1. Na janela **Adicionar atribuição de função** , selecione a função **proprietário de dados do blob de armazenamento** . Em seguida, selecione a assinatura que tem a conta de armazenamento e a identidade gerenciada. Em seguida, pesquise para localizar a identidade gerenciada atribuída pelo usuário que você criou anteriormente. Por fim, selecione a identidade gerenciada e ela será listada em **Membros selecionados**.

    ![Captura de tela mostrando como atribuir uma função do Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selecione **Salvar**. A identidade atribuída pelo usuário que você selecionou agora está listada na função selecionada.
1. Ao concluir a configuração inicial, você poderá criar um cluster usando o portal. O cluster deve estar na mesma região do Azure da conta de armazenamento. Na guia **armazenamento** do menu de criação de cluster, selecione as seguintes opções:

    * Para **tipo de armazenamento primário**, selecione **Azure data Lake Storage Gen2**.
    * Em **conta de armazenamento principal**, pesquise e selecione a conta de armazenamento recém-criada com o armazenamento data Lake Storage Gen2.

    * Em **identidade**, selecione a identidade gerenciada atribuída pelo usuário criada recentemente.

        ![Configurações do armazenamento para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Para adicionar uma conta de armazenamento secundária com Data Lake Storage Gen2, no nível da conta de armazenamento, basta atribuir a identidade gerenciada criada anteriormente ao novo Data Lake Storage Gen2 que você deseja adicionar. É recomendável que a adição de uma conta de armazenamento secundária com Data Lake Storage Gen2 por meio da folha "contas de armazenamento adicionais" no HDInsight não seja suportada.
    > * Você pode habilitar o RA-GRS ou o RA-ZRS na conta de armazenamento de BLOBs do Azure que o HDInsight usa. No entanto, não há suporte para a criação de um cluster em relação ao ponto de extremidade de RA-GRS ou RA-ZRS.

## <a name="delete-the-cluster"></a>Excluir o cluster

Consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um cluster HDInsight. Saiba agora como trabalhar com o cluster.

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark

* [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)
* [Criar um aplicativo autônomo usando Scala](spark/apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
