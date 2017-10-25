---
title: "Usar nós de borda vazios em clusters Hadoop no HDInsight – Azure | Microsoft Docs"
description: "Como adicionar um nó de borda vazio a um cluster HDInsight que pode ser usado como um cliente e então testar/hospedar seus aplicativos de HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: e21dabcc6999b1f1047d334e782f723d0c03c2cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Usar nós de borda vazios em clusters Hadoop no HDInsight

Saiba como adicionar um nó de borda vazio a um cluster HDInsight. Um nó de borda vazio é uma máquina virtual do Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós de cabeçalho, mas sem serviços do Hadoop em execução. Você pode usar o nó de borda para acessar o cluster, testar e hospedar seus aplicativos clientes. 

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

O tamanho de máquina virtual do nó de borda deve atender aos requisitos de tamanho de VM do nó de trabalho do cluster HDInsight. Para obter os tamanhos recomendados de VM do nó de trabalho, consulte [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Depois de criar um nó de borda, você pode conectar-se a ele usando SSH e executar as ferramentas de cliente para acessar o cluster Hadoop no HDInsight.

> [!WARNING] 
> O uso de um nó de borda vazio com HDInsight está atualmente em versão prévia. Componentes personalizados que são instalados no nó de borda recebem suporte comercialmente cabível da Microsoft. Isso pode resultar na resolução de problemas encontrados. Ou você pode receber referências de recursos da comunidade para obter assistência adicional. A seguir estão alguns dos sites mais ativos para obter ajuda da comunidade:
>
> * [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com).
>
> Se você estiver usando uma tecnologia do Apache, você poderá encontrar sites de projetos de assistência por meio do Apache em [http://apache.org](http://apache.org), tais como o site do [Hadoop](http://hadoop.apache.org/).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de borda a um cluster existente
Nesta seção, você pode usar um modelo do Resource Manager para adicionar um nó de borda a um cluster HDInsight existente.  O modelo do Resource Manager pode ser encontrado no [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/). O modelo do Resource Manager chama uma ação de script localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação.  Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Se você ainda não tem um, crie um cluster HDInsight.  Veja [Tutorial do Hadoop: Introdução ao uso do Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: selecione uma assinatura do Azure usada para criar esse cluster.
   * **Grupo de Recursos**: selecione o grupo de recursos usado para o cluster HDInsight existente.
   * **Localização**: Selecione a localização do cluster HDInsight existente.
   * **Nome do Cluster**: insira o nome de um cluster HDInsight existente.
   * **Tamanho do Nó de Borda**: Selecione um dos tamanhos de VM. O tamanho de VM deve atender aos requisitos de tamanho de VM do nó de trabalho. Para obter os tamanhos recomendados de VM do nó de trabalho, consulte [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Prefixo do Nó de Borda**: O valor padrão é **novo**.  Usando o valor padrão, o nome do nó de borda é **new-edgenode**.  Você pode personalizar o prefixo no portal. Você também pode personalizar o nome completo no modelo.

4. Selecione **Concordo com os termos e condições declarados acima** e clique em **Comprar** para criar um nó de borda.

>[!IMPORTANT]
> Certifique-se de selecionar o grupo de recursos do Azure para o cluster HDInsight existente.  Caso contrário, você receberá a mensagem de erro "Não é possível executar a operação solicitada no recurso aninhado. Recurso pai '&lt;NomeDoCluster>' não encontrado".

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de borda ao criar um cluster
Nesta seção, você pode usar um modelo do Resource Manager para criar um cluster HDInsight com um nó de borda.  O modelo do Resource Manager pode ser encontrado em [Galeria de Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). O modelo do Resource Manager chama um script de ação localizado em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação.  Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Se você ainda não tem um, crie um cluster HDInsight.  Veja [Introdução ao uso do Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: selecione uma assinatura do Azure usada para criar esse cluster.
   * **Grupo de Recursos**: Criar um novo grupo de recursos usado para o cluster.
   * **Local**: selecione um local para o grupo de recursos.
   * **Nome do Cluster**: insira um nome para o novo cluster a ser criado.
   * **Nome de Usuário de Logon do Cluster** : insira o nome de usuário HTTP do Hadoop.  O nome padrão é **admin**.
   * **Senha de Logon do Cluster**: digite a senha do usuário HTTP do Hadoop.
   * **Nome de Usuário Ssh**: digite o nome de usuário SSH. O nome padrão é **sshuser**.
   * **Senha do Ssh**: digite a senha de usuário do SSH.
   * **Instalar ação do script**: manter o valor padrão para percorrer este tutorial.
     
     Algumas propriedades foram embutidas no código do modelo: tipo de Cluster, contagem de nós de trabalho do Cluster, tamanho do nó de borda e nome do nó de borda.
4. Selecione **Concordo com os termos e condições declarados acima** e clique em **Comprar** para criar um cluster com nó de borda.

## <a name="access-an-edge-node"></a>Acessar um nó de borda
O ponto de extremidade SSH do nó de borda é &lt;EdgeNodeName>.&lt;ClusterName>-ssh.azurehdinsight.net:22.  Por exemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de borda aparece como um aplicativo no portal do Azure.  O portal fornece as informações para acessar o nó de borda usando o SSH.

**Para verificar o ponto de extremidade SSH do nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos** na folha do cluster. Você verá o nó de borda.  O nome padrão é **new-edgenode**.
4. Clique no nó de borda. Você deverá ver o ponto de extremidade SSH.

**Para usar o Hive no nó de borda**

1. Use SSH para conectar-se ao nó de borda. Para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois que você tiver se conectado ao nó de borda usando o SSH, use o seguinte comando para abrir o console do Hive:
   
        hive
3. Execute o seguinte comando para mostrar as tabelas de Hive no cluster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Excluir um nó de borda
Você pode excluir um nó de borda no portal do Azure.

**Para acessar um nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos** na folha do cluster. Você verá uma lista de nós de borda.  
4. Clique no nó de borda que deseja excluir e clique em **Excluir**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como adicionar um nó de borda e como acessar o nó de borda. Para saber mais, consulte os seguintes artigos:

* [Instalar aplicativos do HDInsight](hdinsight-apps-install-applications.md): saiba como instalar um aplicativo do HDInsight em seus clusters.
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.

