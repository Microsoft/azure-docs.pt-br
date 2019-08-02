---
title: Usar nós de borda vazios em clusters do Apache Hadoop no HDInsight - Azure
description: Como adicionar um nó de borda vazio a um cluster HDInsight que pode ser usado como um cliente e então testar/hospedar seus aplicativos de HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: 41313c7d484df5568fbf729b683a0e9df5325abc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433745"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Usar nós de borda vazios em clusters do Apache Hadoop no HDInsight

Saiba como adicionar um nó de borda vazio a um cluster HDInsight. Um nó de borda vazio é uma máquina virtual do Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós de cabeçalho, mas sem serviços do [Apache Hadoop](https://hadoop.apache.org/) em execução. Você pode usar o nó de borda para acessar o cluster, testar e hospedar seus aplicativos clientes. 

Você pode adicionar um nó de borda vazio a um cluster HDInsight existente ou a um novo cluster quando ele é criado. A adição de um nó de borda vazio é feita usando o modelo Azure Resource Manager.  O exemplo abaixo demonstra como isso é feito usando um modelo:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Conforme mostrado no exemplo, você pode, opcionalmente, chamar uma [ação do script](hdinsight-hadoop-customize-cluster-linux.md) para executar configurações adicionais, como a instalação do [Apache Hue](hdinsight-hadoop-hue-linux.md) no nó de borda. O script de ação de script deve ser publicamente acessível na Web.  Por exemplo, se o script for armazenado no armazenamento do Azure, use contêineres públicos ou blobs públicos.

O tamanho de máquina virtual do nó de borda deve atender aos requisitos de tamanho de VM do nó de trabalho do cluster HDInsight. Para obter os tamanhos recomendados de VM do nó de trabalho, consulte [Criar clusters Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Depois de criar um nó de borda, você pode conectar-se a ele usando SSH e executar as ferramentas de cliente para acessar o cluster Hadoop no HDInsight.

> [!WARNING]   
> Componentes personalizados que são instalados no nó de borda recebem suporte comercialmente cabível da Microsoft. Isso pode resultar na resolução de problemas encontrados. Ou você pode receber referências de recursos da comunidade para obter assistência adicional. A seguir estão alguns dos sites mais ativos para obter ajuda da comunidade:
>
> * [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Se você estiver usando uma tecnologia Apache, poderá encontrar ajuda nos sites do projeto Apache em [https://apache.org](https://apache.org), como o site do [Apache Hadoop](https://hadoop.apache.org/).

> [!IMPORTANT]
> As imagens do Ubuntu ficam disponíveis para a criação do novo cluster HDInsight dentro de 3 meses depois de publicado. A partir de janeiro de 2019, os clusters em execução (incluindo nó de borda) **não** contam com patch automaticamente. Os clientes devem usar ações de script ou outros mecanismos para aplicar o patch de um cluster em execução.  Para obter mais informações, confira [Aplicação de patch do SO para HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de borda a um cluster existente
Nesta seção, você pode usar um modelo do Resource Manager para adicionar um nó de borda a um cluster HDInsight existente.  O modelo do Resource Manager pode ser encontrado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). O modelo do Resource Manager chama uma ação de script localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação.  Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: escolha uma assinatura do Azure usada para criar esse cluster.
   * **Grupo de recursos**: escolha o grupo de recursos usado no cluster HDInsight existente.
   * **Localização**: escolha a localização do cluster HDInsight existente.
   * **Nome do cluster**: insira o nome de um cluster HDInsight existente.
   * **Tamanho de nó de cabeçalho**: escolha um dos tamanhos de VM. O tamanho de VM deve atender aos requisitos de tamanho de VM do nó de trabalho. Para obter os tamanhos recomendados de VM do nó de trabalho, consulte [Criar clusters Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Prefixo do nó de cabeçalho**: O valor padrão é **new**.  Usando o valor padrão, o nome do nó de borda é **new-edgenode**.  Você pode personalizar o prefixo no portal. Você também pode personalizar o nome completo no modelo.

4. Selecione **Concordo com os termos e condições declarados acima** e clique em **Comprar** para criar um nó de borda.

>[!IMPORTANT]  
> Certifique-se de selecionar o grupo de recursos do Azure para o cluster HDInsight existente.  Caso contrário, você receberá a mensagem de erro "Não é possível executar a operação solicitada no recurso aninhado. Recurso pai '&lt;NomeDoCluster>' não encontrado".

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de borda ao criar um cluster
Nesta seção, você pode usar um modelo do Resource Manager para criar um cluster HDInsight com um nó de borda.  O modelo do Resource Manager pode ser encontrado na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). O modelo do Resource Manager chama uma ação de script localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação.  Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para criar um cluster HDInsight com um nó de borda**

1. Se você ainda não tem um, crie um cluster HDInsight.  Veja [Introdução ao uso do Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: escolha uma assinatura do Azure usada para criar esse cluster.
   * **Grupo de recursos**: crie um novo grupo de recursos usado para o cluster.
   * **Localização**: Selecione um local para o grupo de recursos.
   * **Nome do cluster**: insira um nome para o novo cluster que será criado.
   * **Nome de Usuário de Logon do Cluster**: insira o nome de usuário HTTP do Hadoop.  O nome padrão é **admin**.
   * **Senha de Logon do Cluster**: insira a senha do usuário HTTP do Hadoop.
   * **Nome de Usuário SSH**: insira o nome de usuário SSH. O nome padrão é **sshuser**.
   * **Senha SSH**: insira a senha de usuário SSH.
   * **Instalar a ação do script**: Mantenha o valor padrão para prosseguir com este artigo.
     
     Algumas propriedades foram inseridas no código do modelo: tipo de Cluster, contagem de nós de trabalho do Cluster, tamanho do nó de borda e nome do nó de borda.
4. Selecione **Concordo com os termos e condições declarados acima** e clique em **Comprar** para criar um cluster com nó de borda.

## <a name="add-multiple-edge-nodes"></a>Adicionar vários nós de borda

Você pode adicionar vários nós de borda para um cluster HDInsight.  A configuração de vários nós de borda só pode ser feita usando Modelos do Azure Resource Manager.  Confira o exemplo de modelo no início deste artigo.  Você precisa atualizar o **targetInstanceCount** para refletir o número de nós de borda que você gostaria de criar.

## <a name="access-an-edge-node"></a>Acessar um nó de borda
O ponto de extremidade SSH do nó de borda é &lt;EdgeNodeName>.&lt;ClusterName>-ssh.azurehdinsight.net:22.  Por exemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de borda aparece como um aplicativo no portal do Azure.  O portal fornece as informações para acessar o nó de borda usando o SSH.

**Para verificar o ponto de extremidade SSH do nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos**. Você verá o nó de borda.  O nome padrão é **new-edgenode**.
4. Clique no nó de borda. Você deverá ver o ponto de extremidade SSH.

**Para usar o Hive no nó de borda**

1. Use SSH para conectar-se ao nó de borda. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois que você tiver se conectado ao nó de borda usando o SSH, use o seguinte comando para abrir o console do Hive:
   
        hive
3. Execute o seguinte comando para mostrar as tabelas de Hive no cluster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Excluir um nó de borda
Você pode excluir um nó de borda no portal do Azure.

**Para acessar um nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos**. Você verá uma lista de nós de borda.  
4. Clique no nó de borda que deseja excluir e clique em **Excluir**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como adicionar um nó de borda e como acessar o nó de borda. Para saber mais, consulte os seguintes artigos:

* [Instalar aplicativos HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo HDInsight em seus clusters.
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: Instalar um aplicativo HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.

