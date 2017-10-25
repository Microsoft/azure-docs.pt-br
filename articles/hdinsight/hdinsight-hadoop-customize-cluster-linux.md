---
title: "Personalizar os clusters HDInsight usando ações de script – Azure | Microsoft Docs"
description: "Adicione componentes personalizados ao clusters HDInsight baseados em Linux usando Ações de Script. As Ações de Script são scripts Bash que podem ser usadas para personalizar a configuração do cluster ou adicionar outros serviços e utilitários, como Hue, Solr ou R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: larryfr
ms.openlocfilehash: 0c5d00b6cb9f68a1a0e474f81c969eb1b5654c67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>Personalizar clusters HDInsight baseados em Linux usando a Ação de Script

O HDInsight fornece uma opção de configuração chamada **Ação de Script** que chama os scripts personalizados que personalizam o cluster. Esses scripts são usados para instalar componentes adicionais e alterar definições de configuração. Ações de script podem ser usadas durante ou após a criação do cluster.

> [!IMPORTANT]
> A capacidade de usar as ações de script em um cluster já em execução só está disponível para os clusters HDInsight baseados em Linux.
>
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ações de script também podem ser publicadas no Azure Marketplace como um aplicativo do HDInsight. Alguns dos exemplos neste documento mostram como você pode instalar um aplicativo do HDInsight usando comandos de ação de script do PowerShell e o SDK do .NET. Para obter mais informações sobre aplicativos do HDInsight, consulte [Publicar aplicativos do HDInsight no Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Permissões

Se você estiver usando um cluster de HDInsight do domínio, há duas permissões Ambari necessárias ao usar ações de script com o cluster:

* **AMBARI. EXECUTAR\_PERSONALIZADO\_COMANDO**: função de administrador de Ambari tem essa permissão por padrão.
* **CLUSTER. EXECUTAR\_PERSONALIZADO\_COMANDO**: administrador de Cluster HDInsight e administrador de Ambari têm essa permissão por padrão.

Para obter mais informações sobre como trabalhar com permissões com o HDInsight de domínio, consulte [gerenciar clusters de HDInsight de domínio](hdinsight-domain-joined-manage.md).

## <a name="access-control"></a>Controle de acesso

Se você não for o proprietário/administrador da sua assinatura do Azure, sua conta deverá ter pelo menos acesso de **Colaborador** ao grupo de recursos que contém o cluster HDInsight.

Além disso, se você está criando um cluster HDInsight, alguém com no mínimo acesso de **Colaborador** à assinatura do Azure deve ter registrado anteriormente o provedor para HDInsight. O registro do provedor acontece quando um usuário com acesso de Colaborador à assinatura cria um recurso nela pela primeira vez. Isso também pode ser feito sem criar um recurso, [registrando um provedor com o uso de REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para saber mais sobre como trabalhar com o gerenciamento de acesso, confira os seguintes documentos:

* [Introdução ao gerenciamento de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md)
* [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>Noções básicas sobre Ações de Script

Uma Ação de Script é simplesmente um script Bash para o qual você fornecer um URI e parâmetros. O script é executado em nós no cluster HDInsight. A seguir, as características e os recursos das ações de script.

* Deve estar armazenado em um URI que pode ser acessado do cluster HDInsight. Estes são os possíveis locais de armazenamento:

    * Uma conta de **Azure Data Lake Store** acessível pelo cluster HDInsight. Para obter mais informações sobre o uso do Azure Data Lake Store com o HDInsight, veja [Criar um cluster HDInsight com o Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Ao usar um script armazenado no Data Lake Store, o formato de URI é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > A entidade de serviço que HDInsight usa para acessar o Data Lake Store deve ter acesso de leitura para o script.

    * Um blob em uma **conta do Armazenamento do Azure** que é a conta de armazenamento principal ou adicional para o cluster HDInsight. O HDInsight recebe acesso a ambos esses tipos de contas de armazenamento durante a criação do cluster.

    * Um serviço de compartilhamento de arquivos público como o Blob do Azure, o GitHub, o OneDrive, o Dropbox, etc.

        Para URIs de exemplo, consulte a seção [Scripts de exemplo de Ação de Script](#example-script-action-scripts).

        > [!WARNING]
        > O HDInsight suporta apenas contas do Azure Storage de __Uso geral__. Atualmente ele não dá suporte ao tipo de conta __Armazenamento de blobs__.

* Podem ser restritos à **execução somente em determinados tipos de nó**, por exemplo, nos nós de cabeçalho ou nos nós de trabalho.

  > [!NOTE]
  > Quando usado com HDInsight Premium, você pode especificar que o script deve ser usado no nó de borda.

* Pode ser **persistente** ou **ad hoc**.

    Os scripts **persistentes** são aplicados aos nós de trabalho adicionados ao cluster depois que o script é executado. Por exemplo, ao escalar verticalmente o cluster.

    Um script persistente também pode aplicar alterações a outro tipo de nó, como um nó de cabeçalho.

  > [!IMPORTANT]
  > As ações de script persistentes devem ter um nome exclusivo.

    Scripts **Ad hoc** não são persistentes. Eles não são aplicados a nós de trabalho adicionados ao cluster após o script ter sido executado. Você pode promover posteriormente um script ad hoc a um script persistente ou rebaixar um script persistente a um script ad hoc.

  > [!IMPORTANT]
  > As ações de script usadas durante a criação do cluster são mantidas automaticamente.
  >
  > Os scripts que falham não são persistentes, mesmo que você indique especificamente que eles devem ser.

* Podem aceitar **parâmetros** usados pelo script durante a execução.
* São executados com os **privilégios no nível da raiz** nos nós do cluster.
* Podem ser usados por meio do **Portal do Azure**, do **Azure PowerShell**, da **CLI do Azure** ou do **SDK do .NET do HDInsight**

O cluster mantém um histórico de todos os scripts que foram executados. O histórico é útil quando você precisa localizar a ID de um script para operações de promoção ou rebaixamento.

> [!IMPORTANT]
> Não há nenhuma forma automática de desfazer as alterações feitas por uma ação de script. Reverta manualmente as alterações ou forneça um script que as reverta.


### <a name="script-action-in-the-cluster-creation-process"></a>Ação de Script no processo de criação do cluster

As ações de script usadas durante a criação do cluster são ligeiramente diferentes das ações de script executadas em um cluster existente:

* O script é **automaticamente persistente**.
* Uma **falha** no script pode fazer com que o processo de criação do cluster falhe.

O diagrama a seguir ilustra quando a Ação de Script é executada durante o processo de criação:

![Personalização do cluster HDInsight e estágios durante a criação de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está sendo configurado. Nesse estágio, o script é executado em paralelo com todos os nós especificados no cluster e é executado com os privilégios de raiz nos nós.

> [!NOTE]
> Como o script é executado com o privilégio de nível raiz nos nós do cluster, você pode executar operações como parar e iniciar serviços, incluindo os serviços relacionados ao Hadoop. Se você parar quaisquer serviços, você deve garantir que os serviços Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes do script terminar a execução. Esses serviços são necessários para determinar com êxito a integridade e o estado do cluster enquanto ele está sendo criado.


Durante a criação do cluster, você pode usar várias ações de script simultaneamente. Esses scripts são invocados na ordem em que eles foram especificados.

> [!IMPORTANT]
> Ações de script devem ser concluídas em 60 minutos ou atingirão o tempo limite. Durante o provisionamento do cluster, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos, como tempo de CPU ou largura de banda rede, pode fazer com que o script leve mais tempo para ser concluído comparado ao seu tempo de conclusão no ambiente de desenvolvimento.
>
> Para minimizar o tempo necessário para executar o script, evite tarefas como baixar e compilar aplicativos da origem. Pré-compile aplicativos e armazene o binário no Armazenamento do Azure.


### <a name="script-action-on-a-running-cluster"></a>Ação de script em um cluster em execução

Ao contrário das ações de script usadas durante a criação do cluster, uma falha em um script executado em um cluster já em execução não faz com o cluster mude automaticamente para um estado de falha. Quando um script é concluído, o cluster deve retornar a um estado "em execução".

> [!IMPORTANT]
> Mesmo que o cluster apresente um estado "em execução", o script com falha pode ter partes desfeitas. Por exemplo, um script poderia excluir arquivos necessários para o cluster.
>
> As ações de script são executadas com privilégios de raiz, portanto, você deve assegurar que entendeu o que faz um script antes de aplicá-lo ao seu cluster.

Ao aplicar um script a um cluster, o estado do cluster muda de **Em execução** para **Aceito**, depois para **Configuração do HDInsight** e, por fim, de volta para **Executando** para os scripts bem-sucedidos. O status do script é registrado no histórico de ações do script e você pode usar essas informações para determinar se o script teve êxito ou falhou. Por exemplo, o cmdlet `Get-AzureRmHDInsightScriptActionHistory` do PowerShell pode ser usado para exibir o status de um script. Isso retorna informações semelhantes ao seguinte texto:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Se você alterou a senha de usuário (admin) do cluster depois que o cluster foi criado, ações de script executadas em relação a esse cluster podem falhar. Se você tiver ações de script persistente direcionadas para nós de trabalho, esses scripts poderão falhar quando você dimensionar o cluster.

## <a name="example-script-action-scripts"></a>Scripts de exemplo de Ação de Script

Scripts de ação de script podem ser usados por meio dos utilitários a seguir:

* Portal do Azure
* Azure PowerShell
* CLI do Azure
* SDK do .NET do HDInsight

O HDInsight fornece scripts para instalar os seguintes componentes nos clusters do HDInsight:

| Nome | Script |
| --- | --- |
| **Adicionar uma conta de armazenamento do Azure** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Veja [Adicionar armazenamento adicional a um cluster HDInsight](hdinsight-hadoop-add-storage.md). |
| **Instalar o Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Veja [Instalar e usar o Hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md). |
| **Instalar o Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Veja [Instalar e usar o Presto em clusters HDInsight](hdinsight-hadoop-install-presto.md). |
| **Instalar Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consulte [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| **Instalar o Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consulte [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| **Pré-carregar bibliotecas Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Veja [Adicionar bibliotecas do Hive em clusters HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| **Instalar ou atualizar Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Veja [Instalar ou atualizar o Mono no HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Usar uma Ação de Script durante a criação do cluster

Esta seção fornece exemplos sobre as diferentes maneiras em que você pode usar ações de script ao criar um cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Usar uma Ação de Script durante a criação do cluster no Portal do Azure

1. Comece criando um cluster como descrito em [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Pare quando atingir a seção __Resumo do cluster__.

2. Na seção __Resumo do cluster__, selecione o link __editar__ para __Configurações avançadas__.

    ![Link Configurações avançadas](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Na seção __Configurações avançadas__, selecione __Ações de script__. Na seção __Ações de script__, selecione __+ Enviar novo__

    ![Enviar uma nova ação de script](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Use a entrada __Selecionar um script__ para selecionar um script pré-criado. Para usar um script personalizado, selecione __Personalizado__ e, em seguida, forneça o __Nome__ e o __URI do script Bash__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para usar seu próprio script, selecione __Personalizado__. Caso contrário, selecione um dos scripts fornecidos. |
    | Nome |Especifique um nome para a ação de script. |
    | URI do script Bash |Especifique o URI para o script que é chamado para personalizar o cluster. |
    | Cabeçalho/Trabalho/Zookeeper |Especifique os nós (**Cabeçalho**, de **Trabalho** ou **ZooKeeper**) nos quais o script de personalização é executado. |
    | parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Use a entrada __Persistir essa ação de script__ para garantir que o script seja aplicado durante operações de colocação em escala.

5. Selecione __Criar__ para salvar o script. Em seguida, você pode usar __+ Enviar novo__ para adicionar outro script.

    ![Várias ações de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Quando você terminar de adicionar scripts, use o botão __Selecionar__ e, em seguida, o botão __Avançar__ para retornar à seção __Resumo do cluster__.

3. Para criar o cluster, selecione __Criar__ na seleção __Resumo do cluster__.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Usar uma Ação de Script em modelos do Gerenciador de Recursos do Azure

Os exemplos nesta seção demonstram como usar ações de script com modelos do Azure Resource Manager.

#### <a name="before-you-begin"></a>Antes de começar

* Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).
* Para obter instruções sobre como criar modelos, confira [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Se você ainda não utilizou o PowerShell do Azure com o Gerenciador de recursos, consulte [Uso do PowerShell do Azure com o Gerenciador de recursos do Azure](../azure-resource-manager/powershell-azure-resource-manager.md).

#### <a name="create-clusters-using-script-action"></a>Criar clusters usando a Ação de Script

1. Copie o modelo a seguir para um local no computador. Esse modelo instala o Giraph nos nós de cabeçalho e em nós de trabalho no cluster. Você também pode verificar se o modelo JSON é válido. Cole o conteúdo do modelo no [JSONLint](http://jsonlint.com/), uma ferramenta online para validação de JSON.

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installGiraph",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }
2. Inicie o Azure PowerShell e faça logon em sua conta do Azure. Depois de fornecer suas credenciais, o comando retornará informações sobre sua conta.

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...
3. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [!NOTE]
    > Você pode usar `Get-AzureRmSubscription` para obter uma lista de todas as assinaturas associadas à sua conta, o que inclui a ID de assinatura de cada uma delas.

4. Se você não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça o nome do grupo de recursos e o local necessários para sua solução. Um resumo do novo grupo de recursos é retornado.

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Para criar uma implantação para seu grupo de recursos, execute o comando **New-AzureRmResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluem os seguintes dados:

    * Um nome para sua implantação
    * O nome do seu grupo de recursos
    * O caminho ou a URL para o modelo que você criou.

  Se seu modelo exige quaisquer parâmetros, você deve passar esses parâmetros também. Nesse caso, a ação de script para instalar o R no cluster não exige nenhum parâmetro.

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>

    Você será solicitado a fornecer valores para os parâmetros definidos no modelo.

1. Quando o grupo de recursos tiver sido implantado, você verá um resumo da implantação.

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/14/2017 7:00:27 PM
          Mode              : Incremental
          ...

2. Se sua implantação falhar, você poderá usar os cmdlets a seguir para obter informações sobre as falhas.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Usar uma Ação de Script durante a criação do cluster no Azure PowerShell

Nesta seção, usamos o cmdlet [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/overview).

O script a seguir demonstra como aplicar uma ação de script ao criar um cluster usando o PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode levar alguns minutos até que o cluster seja criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Usar uma Ação de Script durante a criação do cluster no SDK do .NET do HDInsight

O SDK do .NET do HDInsight fornece bibliotecas de cliente que facilitam o trabalho com o HDInsight por meio de um aplicativo .NET. Para ver um exemplo de código, consulte [Criar clusters baseados em Linux no HDInsight usando o SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Aplicar uma Ação de Script em um cluster em execução

Nesta seção, aprenda como aplicar ações de script a um cluster em execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicar uma Ação de Script em um cluster em execução no portal do Azure

1. No [Portal do Azure](https://portal.azure.com), escolha o cluster HDInsight.

2. Na visão geral do cluster HDInsight, selecione o bloco **Ações de Script**.

    ![Bloco Ações de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Você também pode selecionar **Todas as configurações** e **Ações de Script** na seção Configurações.

3. Na parte superior da seção Ações de Script, selecione **Enviar novo**.

    ![Adicionar um script a um cluster em execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Use a entrada __Selecionar um script__ para selecionar um script pré-criado. Para usar um script personalizado, selecione __Personalizado__ e, em seguida, forneça o __Nome__ e o __URI do script Bash__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para usar seu próprio script, selecione __personalizado__. Caso contrário, selecione um script fornecido. |
    | Nome |Especifique um nome para a ação de script. |
    | URI do script Bash |Especifique o URI para o script que é chamado para personalizar o cluster. |
    | Cabeçalho/Trabalho/Zookeeper |Especifique os nós (**Cabeçalho**, de **Trabalho** ou **ZooKeeper**) nos quais o script de personalização é executado. |
    | parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Use a entrada __Persistir essa ação de script__ para garantir que o script seja aplicado durante operações de colocação em escala.

5. Por fim, use o botão **Criar** para aplicar o script ao cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicar uma Ação de Script em um cluster em execução no Azure PowerShell

Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/overview).

O exemplo a seguir demonstra como aplicar uma ação de script a um cluster em execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Quando a operação for concluída, você receberá informações semelhantes ao seguinte texto:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicar uma Ação de Script em um cluster em execução da CLI do Azure

Antes de prosseguir, certifique-se de ter instalado e configurado a CLI do Azure. Para obter mais informações, consulte [Instalar a CLI do Azure](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Para alternar para o modo Azure Resource Manager, use o seguinte comando na linha de comando:

        azure config mode arm

2. Use o comando a seguir para fazer logon em sua assinatura do Azure.

        azure login

3. Use o comando a seguir para aplicar uma ação de script a um cluster em execução

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Se você omitir parâmetros para esse comando, você será solicitado a fornecê-los. Caso o script especificado com `-u` aceite parâmetros, você poderá especificá-los usando o parâmetro `-p`.

    Os tipos de nós válidos são `headnode`, `workernode` e `zookeeper`. Caso o script deva ser aplicado a vários tipos de nós, especifique os tipos separados por um ';'. Por exemplo: `-n headnode;workernode`.

    Para persistir o script, adicione `--persistOnSuccess`. Também é possível persistir o script posteriormente usando `azure hdinsight script-action persisted set`.

    Quando o trabalho for concluído, você receberá uma saída semelhante ao seguinte texto:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Aplicar uma Ação de Script em um cluster em execução usando a API REST

Consulte [Executar Ações de Script em um cluster em execução](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicar uma Ação de Script em um cluster em execução no SDK do .NET do HDInsight

Para obter um exemplo de como usar o SDK do .NET para aplicar scripts a um cluster, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Exibir o histórico, promover e rebaixar Ações de Script

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

1. No [Portal do Azure](https://portal.azure.com), escolha o cluster HDInsight.

2. Na visão geral do cluster HDInsight, selecione o bloco **Ações de Script**.

    ![Bloco Ações de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Você também pode selecionar **Todas as configurações** e **Ações de Script** na seção Configurações.

4. Um histórico de scripts para esse cluster é exibido na seção Ações de Script. Essas informações incluem uma lista de scripts persistentes. Na captura de tela abaixo, você pode ver que o script Solr foi executado nesse cluster. A captura de tela não mostra nenhum os scripts persistente.

    ![Seção Ações de Script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. A seleção de um script no histórico exibe a seção Propriedades desse script. Na parte superior da tela, é possível executar novamente o script ou promovê-lo.

    ![Propriedades de Ações de script](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Também é possível usar **...** à direita das entradas na seção Ações de Script para executar ações.

    ![Ações de script... uso](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

| Usar o seguinte... | Para... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Recuperar informações sobre as ações de script persistentes |
| Get-AzureRmHDInsightScriptActionHistory |Recuperar um histórico das ações de script aplicadas no cluster ou detalhes de um script específico |
| Set-AzureRmHDInsightPersistedScriptAction |Promove uma ação de script ad hoc para uma ação de script persistente |
| Remove-AzureRmHDInsightPersistedScriptAction |Rebaixa uma ação de script persistente a uma ação ad hoc |

> [!IMPORTANT]
> Usar `Remove-AzureRmHDInsightPersistedScriptAction` não desfaz as ações realizadas por um script. Esse cmdlet remove apenas o sinalizador persistente.

O script de exemplo a seguir demonstra como usar os cmdlets para promover e depois rebaixar um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

| Usar o seguinte... | Para... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Recuperar uma lista de ações de script persistente |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Recuperar informações sobre uma ação de script persistente específica |
| `azure hdinsight script-action history list <clustername>` |Recuperar um histórico das ações de script aplicadas ao cluster |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Recuperar informações sobre uma ação de script específica |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promove uma ação de script ad hoc para uma ação de script persistente |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Rebaixa uma ação de script persistente a uma ação ad hoc |

> [!IMPORTANT]
> Usar `azure hdinsight script-action persisted delete` não desfaz as ações realizadas por um script. Esse cmdlet remove apenas o sinalizador persistente.

### <a name="using-the-hdinsight-net-sdk"></a>Usando o SDK do .NET do HDInsight

Para obter um exemplo de como usar o SDK do .NET para recuperar o histórico de scripts de um cluster, promover ou rebaixar scripts, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Este exemplo também demonstra como instalar um aplicativo do HDInsight usando o SDK do .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre utilizado em clusters do HDInsight

O serviço do Microsoft Azure HDInsight usa um ecossistema de tecnologias de software livre formado em torno do Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para obter mais informações, consulte a seção **Escopo do Suporte** do [site de perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

* **Componentes internos** : estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o gerenciador de recursos YARN RM, o HiveQL (linguagem de consulta do Hive) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível em [Novidades nas versões do cluster Hadoop fornecidas pelo HDInsight](hdinsight-component-versioning.md).
* **Componentes personalizados** : como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [!WARNING]
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode estar apto a resolver o problema OU pode solicitar que você contate canais disponíveis para as tecnologias de software livre em que é encontrado conhecimento profundo sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, os projetos do Apache têm sites do projeto em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. O mesmo nível de suporte se aplica, independentemente de como um componente é usado ou instalado no cluster. A lista a seguir descreve as formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. Envio de trabalhos: trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.

2. Personalização de clusters: durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados para instalação nos nós de cluster.

3. Exemplos: para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## <a name="troubleshooting"></a>Solucionar problemas

Você pode usar a interface do usuário da Web Ambari para exibir as informações registradas pelas ações de script. Se o script falhar durante a criação do cluster, os logs também estarão disponíveis na conta de armazenamento padrão associada ao cluster. Esta seção fornece informações sobre como recuperar logs usando ambas as opções.

### <a name="using-the-ambari-web-ui"></a>Usando a interface do usuário da Web do Ambari

1. No seu navegador, navegue até https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster HDInsight.

    Quando solicitado, insira o nome de conta de administrador (admin) e a senha correspondente para o cluster. Você precisará reinserir as credenciais de administrador em um formulário da Web.

2. Na barra na parte superior da página, selecione a entrada **ops**. Uma lista das operações atuais e anteriores realizadas no cluster por meio do Ambari é exibida.

    ![Barra de interface do usuário da Web do Ambari com o item "ops" selecionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Localize as entradas com **run\_customscriptaction** na coluna **Operações**. Essas entradas são criadas quando as Ações de Script são executadas.

    ![Captura de tela de operações](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Para exibir a saída STDOUT e STDERR, selecione a entrada run\customscriptaction e faça uma busca detalhada pelos links. Essa saída é gerada quando o script é executado e pode conter informações úteis.

### <a name="access-logs-from-the-default-storage-account"></a>Acessar logs na conta de armazenamento padrão

Se a criação do cluster falhar devido a um erro de ação de script, os logs poderão ser acessados da conta de armazenamento de cluster.

* Os logs de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Captura de tela de operações](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Nesse diretório, os logs são organizados separadamente em nó de cabeçalho, nó de trabalho e nós do zookeeper. Alguns exemplos incluem:

    * **Nó de cabeçalho** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Nó de trabalho** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Nó zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos os stdout e stderr do host correspondentes são carregados para a conta de armazenamento. Há um **output-\*.txt** e um **errors-\*.txt** para cada ação de script. O arquivo *.txt de saída contém informações sobre o URI do script que foi executado no host. Por exemplo,

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível criar repetidamente um cluster de ação de script com o mesmo nome. Nesse caso, você pode distinguir os logs relevantes com base no nome da pasta DATE. Por exemplo, a estrutura de pastas de um cluster (mycluster) criado em datas diferentes será semelhante às seguintes entradas de log:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se você criar um cluster de ação de script com o mesmo nome no mesmo dia, você poderá usar o prefixo exclusivo para identificar os arquivos de log relevantes.

* Se você criar um cluster perto de 12:00 (meia-noite), é possível que os arquivos de log incluam dois dias. Nesses casos, você verá duas pastas com datas diferentes para o mesmo cluster.

* Carregar arquivos de log no contêiner padrão pode levar até 5 minutos, especialmente para clusters grandes. Portanto, se desejar acessar os logs, você não deverá imediatamente excluir o cluster se uma ação de script falhar.

### <a name="ambari-watchdog"></a>Ambari Watchdog

> [!WARNING]
> Não altere a senha do Ambari Watchdog (hdinsightwatchdog) no seu cluster HDInsight baseado em Linux. A alteração da senha para essa conta interrompe a capacidade de executar novas ações de script no cluster HDInsight.

### <a name="cant-import-name-blobservice"></a>Não é possível importar o BlobService de nome

__Sintomas__: falha na ação de script. Texto semelhante ao seguinte erro é exibido quando você exibe a operação no Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__: esse erro ocorre se você atualizar o cliente de Armazenamento do Azure Python incluído com o cluster HDInsight. O HDInsight espera o cliente de Armazenamento do Azure 0.20.0.

__Resolução__: para resolver esse erro, conecte-se manualmente a cada nó de cluster usando `ssh`, e use o comando a seguir para reinstalar a versão do cliente de armazenamento correta:

```
sudo pip install azure-storage==0.20.0
```

Para saber mais sobre como se conectar ao cluster com o SSH, veja [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>O histórico de não mostra os scripts usados durante a criação do cluster

Se o cluster tiver sido criado antes de 15 de março de 2016, talvez você não veja uma entrada no histórico de Ação de Script. Se você redimensionar o cluster após 15 de março de 2016, os scripts usados durante a criação do cluster aparecerão no histórico, pois eles são aplicados aos novos nós no cluster como parte da operação de redimensionamento.

Há duas exceções:

* Se o cluster tiver sido criado antes de 1º de setembro de 2015. Esta data é quando as Ações de Script foram introduzidas. Qualquer cluster criado antes dessa data não poderia ter usado as Ações de Script para a criação do cluster.

* Se você tiver usado várias Ações de Script durante a criação do cluster e se tiver usado o mesmo nome para vários scripts ou o mesmo nome, o mesmo URI, mas diferentes parâmetros para vários scripts. Nesses casos, você recebe o erro a seguir:

    Nenhuma nova ação de script pode ser executada nesse cluster devido aos nomes de script conflitantes nos scripts existentes. Os nomes de script fornecidos na criação do cluster devem ser exclusivos. Os scripts existentes são executados no redimensionamento.

## <a name="next-steps"></a>Próximas etapas

* [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Acrescentar armazenamento adicional a um cluster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Estágios durante a criação de cluster"
