---
title: 'Tutorial: Usar runbooks de Automação do Azure para criar clusters – Azure HDInsight'
description: Saiba como criar e excluir clusters do Azure HDInsight com scripts executados na nuvem usando runbooks de Automação do Azure.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 5eb0f353579233041bb5ccba46de2549ada7e9b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864781"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Tutorial: Criar clusters do Azure HDInsight com a Automação do Azure

A Automação do Azure permite criar scripts executados na nuvem e gerenciar recursos do Azure sob demanda ou com base em uma agenda. Este artigo descreve como criar runbooks do PowerShell para criar e excluir clusters do Azure HDInsight.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Instalar os módulos necessários para interagir com o HDInsight.
> * Criar e armazenar as credenciais necessárias durante a criação do cluster.
> * Criar um novo runbook de Automação do Azure para criar um cluster do HDInsight.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta de Automação do Azure](../automation/automation-quickstart-create-account.md) existente.
* Uma [conta de Armazenamento do Azure](../storage/common/storage-account-create.md) existente, que será usada como armazenamento de cluster.

## <a name="install-hdinsight-modules"></a>Instalar módulos do HDInsight

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione suas contas de Automação do Azure.
1. Selecione **Galeria de módulos** em **Recursos Compartilhados**.
1. Digite **AzureRM.Profile** na caixa e pressione Enter para pesquisar. Selecione o resultado da pesquisa disponível.
1. Na tela **AzureRM.Profile**, selecione **Importar**. Marque a caixa para atualizar os módulos do Azure e, em seguida, selecione **OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="importar módulo do AzureRM.profile" border="false":::

1. Retorne à galeria de módulos selecionando **Galeria de Módulos** em **Recursos Compartilhados**.
1. Digite **HDInsight**. Selecione **AzureRM.HDInsight**.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="procurar módulos do HDInsight" border="true":::

1. No painel **AzureRM.HDInsight**, selecione **Importar** e **OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="importar módulo do AzureRM.HDInsight" border="true":::

## <a name="create-credentials"></a>Criar credenciais

1. Em **Recursos Compartilhados**, selecione **Credenciais**.
1. Selecione **Adicionar uma credencial**.
1. Insira as informações necessárias no painel **Nova Credencial**. Essa credencial é para armazenar a senha do cluster, que permitirá que você faça logon no Ambari.

    | Propriedade | Valor |
    | --- | --- |
    | Nome | `cluster-password` |
    | Nome de usuário | `admin` |
    | Senha | `SECURE_PASSWORD` |
    | Confirmar senha | `SECURE_PASSWORD` |

1. Selecione **Criar**.
1. Repita o mesmo processo para uma nova credencial `ssh-password` com o nome de usuário `sshuser` e uma senha de sua escolha. Selecione **Criar**. Essa credencial é para armazenar a senha SSH para o cluster.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="criar credencial" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Criar um runbook para criar um cluster

1. Selecione **Runbook**, em **Automação de Processo**.
1. Selecione **Criar um runbook**.
1. No painel **Criar um runbook**, insira um nome para o runbook, como `hdinsight-cluster-create`. Selecione **PowerShell** na lista suspensa **Tipo de runbook**.
1. Selecione **Criar**.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="criar runbook" border="true":::

1. Insira o código a seguir na tela **Editar Runbook do PowerShell** e selecione **Publicar**:

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="publicar runbook" border="true":::

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Criar um runbook para excluir um cluster

1. Selecione **Runbook**, em **Automação de Processo**.
1. Selecione **Criar um runbook**.
1. No painel **Criar um runbook**, insira um nome para o runbook, como `hdinsight-cluster-delete`. Selecione **PowerShell** na lista suspensa **Tipo de runbook**.
1. Selecione **Criar**.
1. Insira o código a seguir na tela **Editar Runbook do PowerShell** e selecione **Publicar**:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Executar Runbooks

### <a name="create-a-cluster"></a>Criar um cluster

1. Exiba a lista de Runbooks para sua conta de automação selecionando **Runbooks** em **Automação de Processo**.
1. Selecione `hdinsight-cluster-create` ou o nome que você usou ao criar o runbook de criação do cluster.
1. Selecione **Iniciar** para executar o runbook imediatamente. Você também pode agendar runbooks para serem executados periodicamente. Confira [Como agendar um runbook na Automação do Azure](../automation/shared-resources/schedules.md)
1. Insira os parâmetros necessários para o script e selecione **OK**. Isso criará um novo cluster do HDInsight com o nome que você especificou no parâmetro **CLUSTERNAME**.

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="execute criar runbook do cluster" border="true":::

### <a name="delete-a-cluster"></a>Excluir um cluster

Exclua o cluster selecionando o runbook `hdinsight-cluster-delete` que você criou. Selecione **Iniciar**, insira o parâmetro **CLUSTERNAME** e selecione **OK**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando ela não for mais necessária, exclua a conta de Automação do Azure criada para evitar encargos indesejados. Para fazer isso, navegue até a portal do Azure, selecione o grupo de recursos em que você criou a Conta de Automação do Azure, selecione a Conta de Automação e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar clusters do Apache Hadoop no HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md)