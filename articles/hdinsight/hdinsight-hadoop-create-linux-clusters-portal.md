---
title: Criar clusters Apache Hadoop usando um navegador da Web, Azure HDInsight
description: Saiba como criar clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no Linux para HDInsight, usando um navegador da Web e a Versão Prévia do Portal do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: d4593cd3602c5324e9cffd5eff8564c09aceaf46
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406285"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá como criar clusters Azure HDInsight baseados no Linux usando o portal.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma assinatura do Azure**. Consulte [Como obter a avaliação gratuita do Azure para testar o Hadoop no HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um navegador da Web**. O portal do Azure usa HTML5 e JavaScript. É possível que não funcione corretamente em navegadores da Web mais antigos.

## <a name="create-clusters"></a>Criar clusters
O portal do Azure expõe a maioria das propriedades do cluster. Usando modelos do Azure Resource Manager, é possível ocultar muitos detalhes. Para obter mais informações, consulte [Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **+ Criar um recurso**.

1.  Em **Azure Marketplace**, selecione **Análises**.

1.  Na opção **Em destaque**, selecione **HDInsight**.
   
    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Criar um novo cluster no portal do Azure")

1. Na página **HDInsight**, selecione **Personalizado (tamanho, configurações, aplicativos)** .

1. Selecione **1 Básico**. Em seguida, insira as informações a seguir.

    ![Definir configurações básicas](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Criar um novo cluster no portal do Azure")

    * Insira o **Nome do Cluster**. Esse nome deve ser globalmente exclusivo.

    * Na lista suspensa **Assinatura**, selecione a assinatura do Azure usada para o cluster.

    * Selecione **Tipo de cluster**. Em seguida, selecione o tipo de cluster que você quer criar. Os exemplos são Hadoop e Apache Spark. O **Sistema operacional** será **Linux**. Em seguida, selecione uma versão do tipo de cluster. Use a versão padrão se não souber o que escolher. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > Os clusters HDInsight são fornecidos em vários tipos. Eles correspondem à carga de trabalho ou tecnologia para a qual o cluster está sintonizado. Não há nenhum método com suporte para criar um cluster que combine vários tipos. Exemplos são Storm e HBase em um cluster.
        
    * Para **Nome de Usuário de Logon no Cluster** e **Senha de Logon no Cluster**, forneça o nome de usuário e a senha para o usuário administrador.

    * Insira um **Nome de Usuário SSH**. Se você quiser usar a mesma senha SSH, como a senha do administrador que você especificou anteriormente, marque a caixa de seleção **Usar a mesma senha como logon do cluster**. Caso contrário, forneça uma **SENHA** ou **CHAVE PÚBLICA** para autenticar o usuário SSH. Uma chave pública é a abordagem recomendada. Escolha **Selecionar** na parte inferior para salvar a configuração das credenciais.
   
        Para obter mais informações, consulte [Conectar HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Para **Grupo de recursos**, especifique se deseja criar um novo grupo de recursos ou usar um existente.

    * Especifique uma **localização** do datacenter onde o cluster será criado.

    * Selecione **Próximo** para ir para a próxima página.

4. Em **2 Segurança + rede**, é possível conectar o cluster a uma rede virtual, usando o menu suspenso fornecido. Selecione uma rede virtual do Azure e a sub-rede se você desejar colocar o cluster em uma rede virtual. Para obter informações sobre como usar o HDInsight com uma rede virtual, consulte [planejar uma implantação de rede virtual para clusters do Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). O artigo inclui requisitos de configuração específicos para a rede virtual. 

    Se você quiser usar o **Enterprise Security Package**, siga estas instruções: [Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selecione **Próximo** para ir para a próxima página.


5. Em **3 Armazenamento**, especifique se prefere o Armazenamento do Azure ou o Azure Data Lake Storage como o armazenamento padrão. Para obter mais informações, confira a tabela a seguir.

     ![Definir configurações de armazenamento](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Criar um novo cluster no portal do Azure")

     | Armazenamento                                      | Descrição |
     |----------------------------------------------|-------------|
     | **Blobs de Armazenamento do Azure como o armazenamento padrão**   | <ul><li>Para o **tipo de armazenamento primário**, selecione **Armazenamento do Azure**. Para **Método de seleção**, escolha **Minhas assinaturas**, se você quiser especificar uma conta de armazenamento que faz parte da sua assinatura do Azure. Em seguida, selecione a conta de armazenamento. Caso contrário, selecione **Chave de acesso**. Em seguida, forneça as informações da conta de armazenamento que você quer escolher fora da sua assinatura do Azure.</li><li>Para **Contêiner padrão**, escolha o nome do contêiner padrão sugerido pelo portal ou especifique o seu próprio.</li><li>Se o Armazenamento de Blobs do Azure for o armazenamento padrão, também será possível selecionar **Contas de Armazenamento Adicionais** para especificar contas de armazenamento adicionais para associar ao cluster. Para **Chaves de Armazenamento do Azure**, selecione **Adicionar uma chave de armazenamento**. Em seguida, você poderá fornecer uma conta de armazenamento a partir das suas assinaturas do Azure, ou de outras assinaturas. Forneça a chave de acesso à conta de armazenamento.</li><li>Se o armazenamento de Blobs for o armazenamento padrão, também será possível selecionar **Acesso ao Data Lake Storage** para especificar o Azure Data Lake Storage como armazenamento adicional. Para obter mais informações, confira [Início Rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage como o armazenamento padrão** | Para **Tipo de armazenamento primário**, selecione **Azure Data Lake Storage Gen1** ou **Azure Data Lake Storage Gen2**. Em seguida, consulte o artigo [Início rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) para saber mais. |
     | **Metastores externas**                      | Como opção, especifique um banco de dados SQL para salvar metadados do Apache Hive e do Apache Oozie associados ao cluster. Para **Selecionar um banco de dados SQL para Hive**, selecione um banco de dados SQL. Em seguida, forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas para metadados do Oozie.<br><br>Algumas considerações sobre o uso do banco de dados SQL do Azure para metastores são as seguintes: <ul><li>O banco de dados SQL do Azure usado para o metastore deve permitir conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No lado direito do painel do banco de dados SQL do Azure, selecione o nome do servidor. Esse servidor é aquele no qual a instância do banco de dados SQL é executada. Após exibir o servidor, selecione **Configurar**. Em seguida, para **Serviços do Azure**, selecione **Sim**. Em seguida, selecione **Salvar**.</li><li>Ao criar um metastore, não use traços nem hifens para nomear um banco de dados. Esses caracteres podem causar falha no processo de criação do cluster.</li></ul> |

     > [!WARNING]  
     > Não há suporte para o uso de uma conta de armazenamento adicional em uma localização diferente do cluster HDInsight.

     Selecione **Próximo** para ir para a próxima página.


6. Em **4 Aplicativos (opcional)** , selecione os aplicativos desejados. Você, a Microsoft ou os fornecedores independentes de software (ISVs)  podem desenvolver esses aplicativos. Para obter mais informações, consulte [Instalar aplicativos durante a criação do cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selecione **Próximo** para ir para a próxima página.


6. **5 Tamanho do cluster** mostra informações sobre os nós usados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado de executar o cluster também é mostrado.
   
    ![Especificar tipos de preço de nós](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Especificar número de nós de cluster")
   
   > [!IMPORTANT]  
   > Se você planeja mais de 32 nós de trabalho, selecione um tamanho de nó principal com pelo menos oito núcleos e 14 GB de RAM. Planeje os nós na criação de cluster ou, dimensionando o cluster após a criação. 
   > 
   > Para obter mais informações sobre tamanhos de nós e custos associados, consulte [Preços do Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Selecione **Próximo** para ir para a próxima página.

8. Em **6 Ações de script**, você pode personalizar um cluster para instalar componentes personalizados. Essa opção funcionará se você quiser usar um script personalizado para personalizar um cluster, enquanto o cluster estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

   Selecione **Próximo** para ir para a próxima página.

9. Em **7 Resumo**, verifique as informações inseridas anteriormente. Em seguida, selecione **Criar**.

     ![Confirmar configurações](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Especificar número de nós de cluster")
    
    > [!NOTE]  
    > Levará algum tempo para que o cluster seja criado, geralmente, cerca de 20 minutos. Monitore as **Notificações** para conferir o processo de provisionamento.

10. Após o término do processo de criação, selecione **Ir para Recurso** na notificação **Implantação com êxito**. A janela do cluster fornece as informações a seguir.
    
    ![Interface de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriedades do cluster")
    
    Os ícones na janela são explicados da seguinte forma:
    
    * A guia **Visão Geral** fornece todas as informações essenciais sobre o cluster. Exemplos são o nome, o grupo de recursos ao qual ele pertence, a localização, o sistema operacional e a URL do painel do cluster.
    * **Painel** direciona você para o portal do Ambari associado ao cluster.
    * **Secure Shell** fornece informações necessárias para acessar o cluster usando SSH.
    * Usando **Dimensionar cluster**, é possível aumentar o número de nós de trabalho associados ao cluster.
    * **Excluir** exclui o cluster HDInsight.
    

## <a name="customize-clusters"></a>Personalizar clusters
* [Personalizar clusters HDInsight usando Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Próximas etapas
Você criou com êxito um cluster HDInsight. Saiba agora como trabalhar com o cluster.

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase
* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters do Apache Storm
* [Desenvolver topologias em Java para o Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes de Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark
* [Criar um aplicativo independente usando o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

