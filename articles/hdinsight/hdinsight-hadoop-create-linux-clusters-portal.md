---
title: "Criar clusters Hadoop usando um navegador da Web – Azure HDInsight | Microsoft Docs"
description: "Aprenda a criar clusters Hadoop, HBase, Storm ou Spark em Linux para HDInsight usando um navegador da Web e o portal de visualização do Azure."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: c9a6040f7115917219143cd4bc8d0e3ac87370cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá a criar clusters HDInsight baseados em Linux usando o portal.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um navegador da Web**. O Portal do Azure usa HTML5 e Javascript e pode não funcionar corretamente em navegadores mais antigos.

## <a name="create-clusters"></a>Criar clusters
O portal do Azure expõe a maioria das propriedades do cluster. Usando um modelo do Azure Resource Manager, é possível ocultar muitos detalhes. Para obter mais informações, veja [Criar clusters Hadoop baseados em Linux no HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **+**, em **Inteligência + Análise** e clique em **HDInsight**.
   
    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "Criar um novo cluster no portal do Azure")

3. Na folha **HDInsight**, clique em **Personalizado (tamanho, configurações, aplicativos)**, clique em **Noções básicas** e, em seguida, insira as informações a seguir.

    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "Criar um novo cluster no portal do Azure")

    * Insira o **Nome do Cluster**: esse nome deve ser globalmente exclusivo.

    * No menu suspenso **Assinatura**, selecione a assinatura do Azure que será usada para o cluster.

    * Clique em **Tipo de cluster** e, em seguida, selecione:
   
        * **Tipo de Cluster**: se não souber o que escolher, selecione **Hadoop**. É o tipo de cluster mais popular.
     
            > [!IMPORTANT]
            > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster. 
            > 
            > 
        
        * **Sistema Operacional**: selecione **Linux**.
        
        * **Versão**: use a versão padrão se não souber o que escolher. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).
        * **Camada de Cluster**: o Azure HDInsight fornece as ofertas de nuvem de Big Data em duas categorias: camada Standard e camada Premium. Para obter mais informações, consulte [Camadas de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).

    * Para **Nome de Usuário de Logon no Cluster** e **Senha de Logon no Cluster**, forneça o nome de usuário e a senha para o usuário administrador.

    * Insira um **nome de usuário SSH** e, se você quiser que a senha SSH seja igual à senha de administrador que você especificou anteriormente, selecione a caixa de seleção **Usar a mesma senha do logon do cluster**. Se não quiser, forneça uma **SENHA** ou uma **CHAVE PÚBLICA**, que será usada para autenticar o usuário SSH. Usar uma chave pública é a abordagem recomendada. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.
   
        Para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Para **Grupo de recursos**, especifique se deseja criar um novo grupo de recursos ou usar um existente.

    * Especifique um **local** de data center em que o cluster será criado.

    * Clique em **Avançar**.

4. Na folha **Armazenamento**, especifique se deseja o WASB (Armazenamento do Azure) ou Data Lake Store como seu armazenamento padrão. Consulte a tabela abaixo para obter mais informações.

    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "Criar um novo cluster no portal do Azure")

    | Armazenamento                                      | Descrição |
    |----------------------------------------------|-------------|
    | **Blobs de armazenamento do Azure como armazenamento padrão**   | <ul><li>Para o **tipo de armazenamento primário**, selecione **Armazenamento do Azure**. Depois disso, para **Método de seleção**, você poderá escolher **Minhas assinaturas** se desejar especificar uma conta de armazenamento que faça parte de sua assinatura do Azure e, em seguida, selecionar a conta de armazenamento. Caso contrário, clique em **Chave de acesso** e forneça as informações da conta de armazenamento que você deseja escolher de fora de sua assinatura do Azure.</li><li>Para **contêiner padrão**, você pode optar por usar o nome do contêiner padrão sugerido pelo Portal ou especificar seu próprio nome.</li><li>Se você estiver usando o WASB como o armazenamento padrão, você poderá (opcionalmente) clicar em **Contas de Armazenamento Adicionais** para especificar contas de armazenamento adicionais para associar ao cluster. Na folha **Chaves de Armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento** e, em seguida, você poderá fornecer uma conta de armazenamento de suas assinaturas do Azure ou de outras assinaturas (fornecendo a tecla de acesso da conta de armazenamento).</li><li>Se você estiver usando o WASB como armazenamento padrão, você poderá clicar em (opcionalmente) em **Acesso ao Data Lake Store** para especificar o Azure Data Lake Store como armazenamento adicional. Para obter mais informações, consulte [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).</li></ul> |
    | **Azure Data Lake Store como armazenamento padrão** | Para **Tipo de armazenamento principal**, selecione **Data Lake Store** e, em seguida, consulte o artigo [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) para obter instruções. |
    | **Metastores externas**                      | Opcionalmente, você pode especificar um Banco de Dados SQL para salvar metadados de Hive e Oozie associados ao cluster. Para **Selecionar um Banco de Dados SQL para Hive**, selecione um Banco de Dados SQL e então forneça o nome de usuário e a senha para ele. Repita essas etapas para metadados do Oozie.<br><br>Há algumas considerações ao usar o Banco de Dados SQL do Azure para metastores. <ul><li>O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.</li><li>Ao criar um metastore, não use um nome de banco de dados que contém a traços ou hifens, pois isso pode causar falha no processo de criação de cluster.</li></ul>                                                                                                                                                                       |

    Clique em **Avançar**. 

    > [!WARNING]
    > Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

5. Opcionalmente, clique em **Aplicativos** para instalar aplicativos que funcionam com clusters HDInsight. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria. Para saber mais, veja [HDInsight instalar aplicativos](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Clique em **Tamanho do cluster** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.
   
    ![Nó folha de camadas de preços](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "especificar o número de nós de cluster")
   
   > [!IMPORTANT]
   > Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
   > 
   > Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Clique em **Avançar** para salvar a configuração de preços do nó.

7. Clique em **Configurações avançadas** para definir outras configurações opcionais como usar **Ações de Script** para personalizar um cluster para instalar componentes personalizados ou ingressar em uma **Rede Virtual**. Consulte a tabela abaixo para obter mais informações.

    ![Nó folha de camadas de preços](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "especificar o número de nós de cluster")

    | Opção | Descrição |
    |--------|-------------|
    | **Ações de Script** | Use essa opção se você quiser usar um script personalizado para personalizar um cluster enquanto o mesmo estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Rede Virtual** | Selecione uma rede virtual do Azure e a sub-rede se você desejar colocar o cluster em uma rede virtual. Para obter informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, veja [Estender as funcionalidades do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md). |

    Clique em **Avançar**.

8. Na folha **Resumo**, verifique as informações que você inseriu anteriormente e, em seguida, clique em **Criar**.

    ![Nó folha de camadas de preços](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "especificar o número de nós de cluster")
    
    > [!NOTE]
    > Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.
    > 
    > 
12. Quando o processo de criação for concluído, clique no bloco do cluster no quadro inicial para iniciar a folha de cluster. A folha do cluster fornece as informações a seguir.
    
    ![Folha de cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "propriedades do Cluster")
    
    Use o descrito a seguir para entender os ícones na parte superior desta folha.
    
    * A guia **Visão geral** fornece todas as informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.
    * **Painel** direciona você para o portal do Ambari associado ao cluster.
    * **Secure Shell**: informações necessárias para acessar o cluster usando o SSH.
    * **Dimensionar cluster** permite aumentar o número de nós de trabalho associados ao cluster.
    * **Excluir**: exclui o cluster HDInsight.
    

## <a name="customize-clusters"></a>Personalizar clusters
* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Próximas etapas
Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster:

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

### <a name="spark-clusters"></a>Clusters do Spark
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

