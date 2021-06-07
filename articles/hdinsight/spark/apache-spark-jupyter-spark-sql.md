---
title: 'Início Rápido: Criar um cluster Apache Spark usando um modelo – Azure HDInsight'
description: Este guia de início rápido mostra como usar o modelo do Resource Manager para criar um cluster Apache Spark no Azure HDInsight e executar uma consultas SQL do Spark.
ms.date: 03/13/2020
ms.topic: quickstart
ms.service: hdinsight
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6d3f8c3313d9e6be38acdbecb683899ea8dd5376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538020"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Início Rápido: Criar um cluster do Apache Spark no Azure HDInsight usando um modelo do Resource Manager

Neste guia de início rápido, você usará um modelo do Azure Resource Manager para criar um cluster do [Apache Spark](./apache-spark-overview.md) no Azure HDInsight. Em seguida, você criará um arquivo do Jupyter Notebook e o usará para executar consultas Spark SQL em tabelas do Apache Hive. O Azure HDInsight é um serviço de análise de software livre gerenciado e de amplo espectro para empresas. A estrutura Apache Spark para HDInsight permite análises rápidas de dados e computação de cluster usando o processamento na memória. O Jupyter Notebook permite que você interaja com os seus dados, combine o código com texto Markdown e realizar visualizações simples.

Se você estiver usando vários clusters juntos, desejará criar uma rede virtual e, se estiver usando um cluster do Spark, também desejará usar o Hive Warehouse Connector. Para obter mais informações, confira [Planejar uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) e [Integrar o Apache Spark e o Apache Hive com o Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): crie uma Conta de Armazenamento do Azure.
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): crie um cluster HDInsight.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione o botão **Implantar no Azure** abaixo para entrar no Azure e abrir o modelo do Resource Manager.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Digite ou selecione os valores a seguir:

    |Propriedade |Descrição |
    |---|---|
    |Subscription|Na lista suspensa, selecione a assinatura do Azure usada para o cluster.|
    |Resource group|Na lista suspensa, selecione o grupo de recursos existente ou selecione **Criar**.|
    |Location|O valor será preenchido automaticamente com o local usado para o grupo de recursos.|
    |Nome do cluster|Insira um nome global exclusivo. Para este modelo, use apenas letras minúsculas e números.|
    |Nome de usuário de logon do cluster|Forneça o nome de usuário, o padrão é **admin**.|
    |Senha de logon do cluster|Forneça uma senha. A senha precisa ter no mínimo 10 caracteres e precisa conter pelo menos um dígito, uma letra maiúscula, uma minúscula e um caractere não alfanumérico (exceto pelos caracteres ' " ` ). |
    |Nome de Usuário SSH|Forneça o nome de usuário, o padrão é **sshuser**|
    |Senha SSH|Forneça a senha.|

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png " alt-text="Criar um cluster Spark no HDInsight usando um modelo do Azure Resource Manager" border="true":::

1. Examine os **TERMOS E CONDIÇÕES**. Em seguida, selecione **Concordo com os termos e condições declarados acima** e **Comprar**. Você receberá uma notificação de que a implantação está em andamento. Demora cerca de 20 minutos para criar um cluster.

Se você tiver um problema com a criação de clusters HDInsight, talvez não tenha as permissões corretas para fazer isso. Para saber mais, confira [Requisitos do controle de acesso](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Depois que o cluster for criado, você receberá uma notificação de **Implantação bem-sucedida** com um link **Ir para o recurso**. A página do Grupo de recursos listará o novo cluster HDInsight e o armazenamento padrão associado a ele. Cada cluster tem uma dependência do [Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md), do [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md) ou do [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md). Ela é conhecida como a conta de armazenamento padrão. O cluster HDInsight e sua conta de armazenamento padrão devem estar colocados na mesma região do Azure. A exclusão dos clusters não exclui a dependência da conta de armazenamento. Ela é conhecida como a conta de armazenamento padrão. O cluster do HDInsight e sua conta de armazenamento padrão devem estar colocados na mesma região do Azure. A exclusão dos clusters não exclui a conta de armazenamento.

## <a name="create-a-jupyter-notebook-file"></a>Criar um arquivo do Jupyter Notebook

O [Jupyter Notebook](https://jupyter.org/) é um ambiente de notebook interativo que oferece suporte a várias linguagens de programação. Use um arquivo do Jupyter Notebook para interagir com os seus dados, combinar o código com texto Markdown e realizar visualizações simples.

1. Abra o [Portal do Azure](https://portal.azure.com).

2. Selecione **Clusters HDInsight** e, em seguida, selecione o cluster que você criou.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png" alt-text="Abrir o cluster HDInsight no portal do Azure" border="true":::

3. No portal, na seção **Painéis de cluster**, selecione **Jupyter Notebook**. Em caso de solicitação, insira as credenciais de logon do cluster.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Abrir o Jupyter Notebook para executar consulta SQL interativa do Spark" border="true":::

4. Selecione **Novo** > **PySpark** para criar um notebook.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Criar um arquivo do Jupyter Notebook para executar consultas interativas Spark SQL" border="true":::

   Um novo bloco de anotações é criado e aberto com o nome Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Executar instruções SQL do Apache Spark

O SQL (Structured Query Language) é a linguagem mais comum e amplamente usada para consultar e transformar dados. O Spark SQL funciona como uma extensão do Apache Spark para processar dados estruturados, usando a sintaxe SQL familiar.

1. Verifique se o kernel está pronto. O kernel estará pronto quando você vir um círculo vazio ao lado do nome do kernel no notebook. Círculo sólido indica que o kernel está ocupado.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="Status do Kernel" border="true"::: alt-text="Kernel status" border="true":::

    Quando você inicia o notebook pela primeira vez, o kernel executa algumas tarefas em segundo plano. Aguarde a leitura do kernel.

1. Cole o código a seguir em uma célula vazia e pressione **SHIFT + ENTER** para executar o código. O comando lista as tabelas de Hive no cluster:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Ao usar um arquivo do Jupyter Notebook com o cluster HDInsight, você obtém uma sessão `spark` predefinida que poderá usar para executar consultas do Hive por meio do Spark SQL. `%%sql` informa ao Notebook Jupyter para usar a sessão de predefinição `spark` para executar a consulta Hive. A consulta recupera as primeiras 10 linhas de uma tabela Hive (**hivesampletable**) que vem com todos os clusters HDInsight por padrão. Na primeira vez que você enviar a consulta, o Jupyter criará o aplicativo Spark para o notebook. O processo leva aproximadamente 30 segundos para ser concluído. Depois que o aplicativo Spark estiver pronto, a consulta será executada em aproximadamente um segundo e produzirá os resultados. A saída se parece com isso:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png " alt-text="Consulta do Apache Hive no HDInsight" border="true":::y in HDInsight" border="true":::

    Toda vez que você executar uma consulta no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito.

1. Execute outra consulta para ver os dados em `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A tela deve ser atualizada para mostrar a saída da consulta.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png " alt-text="A saída de consulta do Hive no HDInsight" border="true":::Insight" border="true":::

1. No menu **Arquivo** do notebook, selecione **Fechar e Interromper**. O desligamento do notebook libera os recursos do cluster, incluindo o aplicativo Spark.

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir o início rápido, poderá ser conveniente excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

No portal do Azure, navegue até seu cluster e selecione **Excluir**.

:::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png " alt-text="Excluir um cluster do HDInsight no portal do Azure" border="true":::sight cluster" border="true":::

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um cluster Apache Spark no HDInsight e executar uma consulta SQL básica do Spark. Avance para o próximo tutorial para saber como usar um cluster HDInsight para executar consultas interativas em dados de exemplo.

> [!div class="nextstepaction"]
> [Executar consultas interativas no Apache Spark](./apache-spark-load-data-run-query.md)
