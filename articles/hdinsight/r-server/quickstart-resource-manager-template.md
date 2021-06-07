---
title: 'Início Rápido: Criar um cluster do ML Services usando um modelo – Azure HDInsight'
description: Este guia de início rápido mostra como usar o modelo do Resource Manager para criar um cluster do ML Services no Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 4b4196a503bdc0fd455c5d731e11e5c099832c8e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869525"
---
# <a name="quickstart-create-ml-services-cluster-in-azure-hdinsight-using-arm-template"></a>Início Rápido: Criar um cluster dos Serviços de ML no Azure HDInsight usando um modelo do Resource Manager

Neste guia de início rápido, você usará um modelo do Azure Resource Manager para criar um cluster dos [Serviços de ML](./r-server-overview.md) no Azure HDInsight. O Microsoft Machine Learning Server está disponível como uma opção de implantação quando você cria clusters HDInsight no Azure. O tipo de cluster que fornece essa opção é chamado ML Services. Essa funcionalidade fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-rserver/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): crie uma Conta de Armazenamento do Azure.
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): crie um cluster HDInsight.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione o botão **Implantar no Azure** abaixo para entrar no Azure e abrir o modelo do Resource Manager.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

1. Digite ou selecione os valores a seguir:

    |Propriedade |Descrição |
    |---|---|
    |Subscription|Na lista suspensa, selecione a assinatura do Azure usada para o cluster.|
    |Resource group|Na lista suspensa, selecione o grupo de recursos existente ou selecione **Criar**.|
    |Location|O valor será preenchido automaticamente com o local usado para o grupo de recursos.|
    |Nome do cluster|Insira um nome global exclusivo. Para este modelo, use apenas letras minúsculas e números.|
    |Nome de usuário de logon do cluster|Forneça o nome de usuário, o padrão é **admin**.|
    |Senha de logon do cluster|Forneça uma senha. A senha precisa ter no mínimo 10 caracteres e precisa conter pelo menos um dígito, uma letra maiúscula, uma minúscula e um caractere não alfanumérico (exceto pelos caracteres ' " ` ). |
    |Nome de Usuário SSH|Forneça o nome de usuário, o padrão é sshuser|
    |Senha SSH|Forneça a senha.|

    :::image type="content" source="./media/quickstart-resource-manager-template/resource-manager-template-rserver.png" alt-text="Implantar HBase do modelo do Azure Resource Manager" border="true":::

1. Examine os **TERMOS E CONDIÇÕES**. Em seguida, selecione **Concordo com os termos e condições declarados acima** e **Comprar**. Você receberá uma notificação de que a implantação está em andamento. Demora cerca de 20 minutos para criar um cluster.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Depois que o cluster for criado, você receberá uma notificação de **Implantação bem-sucedida** com um link **Ir para o recurso**. A página do Grupo de recursos listará o novo cluster HDInsight e o armazenamento padrão associado a ele. Cada cluster tem uma dependência na conta do [Armazenamento de Blobs do Azure](../hdinsight-hadoop-use-blob-storage.md), do [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md) ou do [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md). Ela é conhecida como a conta de armazenamento padrão. O cluster do HDInsight e sua conta de armazenamento padrão devem estar colocados na mesma região do Azure. A exclusão dos clusters não exclui a conta de armazenamento.

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir o início rápido, poderá ser conveniente excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

No portal do Azure, navegue até seu cluster e selecione **Excluir**.

[Excluir HBase do modelo do Resource Manager](./media/quickstart-resource-manager-template/azure-portal-delete-rserver.png)

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprenderá a criar um cluster dos Serviços de ML no HDInsight usando um modelo do Resource Manager. No próximo artigo, você aprenderá a executar um script R com o RStudio Server que demonstra o uso do Spark para cálculos de R distribuídos.

> [!div class="nextstepaction"]
> [Executar um script de R em um cluster de serviços de ML no Azure HDInsight do usando o RStudio Server](./machine-learning-services-quickstart-job-rstudio.md)
