---
title: Criar um cluster do Apache Spark no Azure HDInsight | Microsoft Docs
description: "Guia de início rápido sobre como criar um cluster do Apache Spark no HDInsight."
keywords: "início rápido do spark,spark interativo,consulta interativa,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: maxluk
ms.openlocfilehash: e64720efa168a84193dc3f351def0e58979f7f86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Criar um cluster do Apache Spark no Azure HDInsight

Neste artigo, você aprenderá a criar um cluster do Apache Spark no HDInsight do Azure e, em seguida, a executar uma consulta Spark SQL em uma tabela de Hive. Para obter informações sobre o Spark no HDInsight, confira [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md).

   ![Diagrama de início rápido que descreve as etapas para criar um cluster Apache Spark no Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Início rápido do Spark usando o Apache Spark no HDInsight. Etapas ilustradas: criar um cluster; executar consulta interativa do Spark")

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Criar cluster do Azure HDInsight Spark

Nesta seção, você criará um cluster Spark do HDInsight usando um [modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para obter outros métodos de criação de cluster, confira [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para abrir o modelo no portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Insira os valores a seguir:

    ![Criar um cluster Spark do HDInsight usando um modelo do Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Criar um cluster Spark no HDInsight usando um modelo do Azure Resource Manager")

    * **Assinatura**: selecione sua assinatura do Azure para esse cluster.
    * No **Grupo de recursos**: crie um grupo de recursos ou selecione um existente. O grupo de recursos é usado para gerenciar recursos do Azure em seus projetos.
    * **Local**: selecione um local para o grupo de recursos. O modelo usa esse local para criar o cluster, bem como para o armazenamento de cluster padrão.
    * **ClusterName**: insira um nome para o cluster HDInsight que você deseja criar.
    * **Versão do Spark**: selecione **2.0** como a versão que você deseja instalar no cluster.
    * **Nome e senha de logon do cluster**: o nome de logon padrão é admin.
    * **Nome de usuário e senha de SSH**.

   Anote esses valores.  Você precisará deles mais tarde no tutorial.

3. Selecione **Concordo com os termos e condições declarados acima**, selecione **Fixar no painel**e, em seguida, clique em **Comprar**. Você poderá ver um novo bloco intitulado Como enviar a implantação para a implantação do modelo. Demora cerca de 20 minutos para criar o cluster.

Se você tiver um problema com a criação de clusters HDInsight, é possível que você não tenha as permissões corretas para fazer isso. Para saber mais, confira [Requisitos do controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> Este artigo cria um cluster Spark que usa [Blobs do Armazenamento do Azure como o armazenamento de cluster](hdinsight-hadoop-use-blob-storage.md). Você também pode criar um cluster Spark que usa o [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) como o armazenamento padrão. Para obter instruções, confira [Criar um cluster HDInsight com o Repositório Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Executar instruções SQL Spark em uma tabela de Hive

O SQL (Structured Query Language) é a linguagem mais comum e amplamente usada para consultar e definição de dados. Os fundadores do Spark buscaram utilizar esse conhecimento, abrindo a linguagem de consulta de dados bem conhecida para um público mais amplo de analistas que desejam trabalhar com dados residentes no HDFS (Sistema de Arquivos Distribuído) do Hadoop. O Spark SQL é essa oferta. Ele funciona como uma extensão do Apache Spark para processar dados estruturados, usando a sintaxe SQL familiar.

O Spark SQL oferece suporte a SQL e HiveQL como linguagens de consulta. Os recursos incluem a associação em Java, Python e Scala. Com ele, você pode consultar os dados armazenados em vários locais, como bancos de dados externos, arquivos de dados estruturados (exemplo: JSON) e tabelas de Hive.

### <a name="running-spark-sql-on-an-hdinsight-cluster"></a>Execução do SQL do Spark em um cluster HDInsight

Quando você usa um notebook Jupyter configurado para o cluster HDInsight Spark, obtém uma predefinição `sqlContext` que você pode usar para executar consultas do Hive usando o Spark SQL. Nesta seção, você aprenderá a iniciar um bloco de anotações do Jupyter e, em seguida, executar uma consulta básica do Spark SQL em uma tabela de Hive existente (**hivesampletable**) que está disponível em todos os clusters de HDInsight.

1. Abra o [Portal do Azure](https://portal.azure.com/).

2. Se você tiver optado por fixar o cluster ao painel, clique no bloco do cluster no painel para iniciar a folha de cluster.

    Se você não fixar o cluster ao painel, no painel esquerdo, clique em **Clusters HDInsight** e clique no cluster que você criou.

3. Em **Links rápidos**, clique em **Painéis de cluster**e, em seguida, clique em **Anotações do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

   ![Abrir um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Abrir um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark")

   > [!NOTE]
   > Você também pode acessar o bloco de anotações Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um notebook. Clique em **Novo** e em **PySpark**.

   ![Criar um bloco de anotações Jupyter para executar consulta interativa SQL do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Criar um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark")

   Um novo bloco de anotações é criado e aberto com o nome Untitled(Untitled.pynb).

4. Clique no nome do bloco de anotações na parte superior e, se desejar, digite um nome amigável.

    ![Forneça um nome para o bloco de anotações Jupyter para executar a consulta interativa Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Forneça um nome para o bloco de anotações Jupyter para executar a consulta interativa Spark")

5.  Cole o código a seguir em uma célula vazia e pressione **SHIFT + ENTER** para executar o código. No código a seguir, `%%sql` (chamado palavra mágica do SQL) informa ao notebook Jupyter para usar a predefinição `sqlContext` para executar a consulta de Hive. A consulta recupera as primeiras 10 linhas de uma tabela Hive (**hivesampletable**) que está disponível por padrão em todos os clusters de HDInsight.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Consulta de Hive no HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta de Hive no HDInsight Spark")

    Para obter mais informações sobre o `%%sql` mágico e os contextos predefinidos, confira [Jupyter kernels disponíveis para um cluster HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

    > [!NOTE]
    > Toda vez que você executar uma consulta no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Após a conclusão do trabalho, isso será alterado para um círculo vazio.
    >
    >
    
6. A tela deve ser atualizada para mostrar a saída da consulta.

    ![Saída da consulta de Hive no HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Saída da consulta de Hive no HDInsight Spark")

7. Depois de concluir a execução do aplicativo, encerre o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**.

8. Se você planeja conclua as seguintes etapas em um momento posterior, não se esqueça de excluir o cluster HDInsight criado neste artigo. 

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Próxima etapa 

Neste artigo, você aprendeu a criar um cluster HDInsight Spark e executar uma consulta de SQL básica do Spark. Avança para o próximo artigo para saber como usar um cluster HDInsight Spark para executar consultas interativas em dados de exemplo.

> [!div class="nextstepaction"]
>[Executar consultas interativas em um cluster HDInsight Spark](hdinsight-apache-spark-load-data-run-query.md)



