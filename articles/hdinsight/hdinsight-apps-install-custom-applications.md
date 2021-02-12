---
title: Instalar seus aplicativos de Apache Hadoop personalizados no Azure HDInsight
description: Saiba como instalar aplicativos do HDInsight para Apache Hadoop clusters no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 9591551b7d5657803e770bd71b47acb141b40fb6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942691"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicativos personalizados do Apache Hadoop no HDInsight do Azure

Neste artigo, você aprenderá a instalar um aplicativo [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight, que não foi publicado no portal do Azure. O aplicativo que você instalará neste artigo é o [matiz](https://gethue.com/).

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster do HDInsight.  Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria.  

## <a name="prerequisites"></a>Pré-requisitos

Se você quiser instalar aplicativos do HDInsight em um cluster HDInsight existente, precisará ter um cluster HDInsight. Para criar um, confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md). Você também pode instalar aplicativos do HDInsight quando cria um cluster HDInsight.

## <a name="install-hdinsight-applications"></a>Instalar aplicativos do HDInsight

Os aplicativos do HDInsight podem ser instalados em um cluster HDInsight existente ou durante a criação de um cluster. Para definir modelos do Azure Resource Manager, confira [MSDN: instalar um aplicativo do HDInsight](/rest/api/hdinsight/hdinsight-application).

Arquivos necessários para implantar esse aplicativo (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modelo do Resource Manager para a instalação do aplicativo do HDInsight. Confira [MSDN: instalar um aplicativo do HDInsight](/rest/api/hdinsight/hdinsight-application) para desenvolver seu próprio modelo do Resource Manager.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): ação de script a ser chamada pelo modelo do Resource Manager para configurar o nó de borda.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): o arquivo binário do Hue chamado de hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): o arquivo binário do Hue sendo chamado de hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): um aplicativo Web de exemplo (Tomcat) sendo chamado de hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Para instalar o Hue em um cluster HDInsight existente

1. Selecione a imagem a seguir para entrar no Azure e abra o modelo do Resource Manager na portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo do Resource Manager está localizado em [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) .  Para aprender a escrever esse modelo do Resource Manager, confira [MSDN: instalar um aplicativo do HDInsight](/rest/api/hdinsight/hdinsight-application).

1. Selecione o **grupo de recursos** existente que contém o cluster na lista suspensa. É necessário usar o mesmo grupo de recursos que o cluster.

1. Insira o nome do cluster onde você deseja instalar o aplicativo. Esse cluster deve ser um cluster existente.

1. Marque a caixa de seleção para **eu concordo com os termos e condições declarados acima**.

1. Selecione **Comprar**.

Você pode ver o status da instalação no bloco ao painel do portal e na notificação no portal (clique no ícone de sino na parte superior do portal).  Demora cerca de 10 minutos para instalar o aplicativo.

### <a name="to-install-hue-while-creating-a-cluster"></a>Para instalar o Hue durante a criação de um cluster

1. Selecione a imagem a seguir para entrar no Azure e abra o modelo do Resource Manager na portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo do Resource Manager está localizado em [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) .  Para aprender a escrever esse modelo do Resource Manager, confira [MSDN: instalar um aplicativo do HDInsight](/rest/api/hdinsight/hdinsight-application).

2. Siga as instruções para criar o cluster e instalar o Hue. Para saber mais sobre a criação de clusters HDInsight, confira [Criar clusters Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Outros métodos de instalação

Além do portal do Azure, você também pode usar o [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) e a [CLI do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) para chamar modelos do Resource Manager.

## <a name="validate-the-installation"></a>validar a instalação

Você pode verificar o status do aplicativo no portal do Azure para validar a instalação do aplicativo. Além disso, você também pode validar todos os pontos de extremidade HTTP fornecidos conforme o esperado e a página da Web, se houver.

Para **matiz**, você pode usar as seguintes etapas:

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o cluster onde você instalou o aplicativo.
1. No menu **configurações** , selecione **aplicativos**.
1. Selecione **matiz** na lista para exibir as propriedades.  
1. Selecione o link da página da Web para validar o site.

### <a name="azure-cli"></a>CLI do Azure

Substitua e `CLUSTERNAME` `RESOURCEGROUP` pelos valores relevantes e, em seguida, insira os comandos abaixo:

* Para listar todos os aplicativos para o cluster HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Para recuperar as propriedades do aplicativo especificado.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Solucionar problemas de instalação

Você pode verificar o status de instalação do aplicativo da notificação do portal (clique no ícone de sino na parte superior do portal).

Se a instalação de um aplicativo falhar, você poderá ver as mensagens de erro e depurar as informações de três locais:

* Aplicativos do HDInsight: informações de erro geral.

    Abra o cluster no portal e selecione aplicativos em configurações:

    ![erro de instalação do aplicativo aplicativos hdinsight](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Ação de script HDInsight: se a mensagem de erro dos aplicativos do HDInsight indicar uma falha na ação de script, mais detalhes sobre a falha do script serão apresentados no painel de ações de script.

    Selecione Ação de script nas configurações. O histórico de ação de script mostra as mensagens de erro

    ![erro de ação de script aplicativos hdinsight](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Interface do usuário da Web do Apache Ambari: se o script de instalação foi a causa da falha, use a interface do usuário da Web do amAmbari para verificar os logs completos sobre os scripts de instalação.

    Para obter mais informações, consulte [solucionar problemas de ações de script](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Remover aplicativos do HDInsight

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o cluster onde você instalou o aplicativo.
1. No menu **configurações** , selecione **aplicativos**.
1. Clique com o botão direito do mouse no aplicativo que você deseja remover e selecione **excluir**.
1. Clique em **Sim** para confirmar.

### <a name="azure-cli"></a>CLI do Azure

Substitua `NAME` , `CLUSTERNAME` e `RESOURCEGROUP` pelos valores relevantes e, em seguida, insira o comando a seguir:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Próximas etapas

* [MSDN: instalar um aplicativo do HDInsight](/rest/api/hdinsight/hdinsight-application): saiba como desenvolver modelos do Resource Manager para implantar aplicativos do HDInsight.
* [Instalar aplicativos HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo HDInsight em seus clusters.
* [Publicar aplicativos HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar o cluster HDInsight, testar e hospedar aplicativos HDInsight.
