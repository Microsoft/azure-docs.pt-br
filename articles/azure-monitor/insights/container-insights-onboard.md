---
title: Como carregar o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como carregar e configurar o Azure Monitor para contêineres para que você possa entender o desempenho do seu contêiner e quais problemas relacionados ao desempenho foram identificados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: b5ba9a0abe8ec0f72cfaf42c747616e733fb3f32
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101142"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Como carregar o Azure Monitor para contêineres  

Este artigo descreve como configurar o Azure Monitor para contêineres para monitorar o desempenho das cargas de trabalho implantadas em ambientes do Kubernetes e hospedadas no [Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/aks/).

O Azure Monitor para contêineres pode ser habilitado para novas implantações ou para uma ou mais implantações existentes do AKS usando os seguintes métodos compatíveis:

* No portal do Azure, Azure PowerShell, ou com a CLI do Azure
* Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
Antes de começar, verifique se você tem o seguinte:

- **Um espaço de trabalho do Log Analytics.** É possível criá-lo ao habilitar o monitoramento do novo cluster do AKS ou permitir que a experiência de integração crie um workspace padrão no grupo de recursos padrão da assinatura de cluster do AKS. Se optar por criá-lo por conta própria, será possível criá-lo por meio do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).
- Você é membro do **função de Colaborador do Log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../../azure-monitor/platform/manage-access.md).
- Você é membro do **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** função no recurso de cluster do AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Componentes 

A capacidade de monitorar o desempenho se baseia em um agente do Log Analytics para Linux em contêineres desenvolvido especificamente para o Azure Monitor para contêineres. Esse agente especializado coleta dados de desempenho e eventos de todos os nós no cluster e é implantado e registrado automaticamente com o espaço de trabalho do Log Analytics especificado durante a implantação. A versão do agente é microsoft/oms:ciprod04202018 ou posterior e é representada por uma data no seguinte formato: *mmddaaaa*. 

Quando uma nova versão do agente é lançada, ele é atualizado automaticamente nos clusters gerenciados do Kubernetes hospedados no AKS (Serviço de Kubernetes do Azure). Para seguir as versões lançadas, confira [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Se você já implantou um cluster do AKS, habilite o monitoramento usando a CLI do Azure ou um modelo do Azure Resource Manager fornecido, conforme demonstrado posteriormente neste artigo. Não é possível usar `kubectl` para atualizar, excluir, implantar ou reimplantar o agente. O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Entre no [Portal do Azure](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Habilitar o monitoramento para um novo cluster
Durante a implantação, você poderá habilitar o monitoramento de um novo cluster do AKS no portal do Azure, com a CLI do Azure ou com o Terraform.  Para habilitar a partir do portal, siga as etapas no artigo de início rápido [implantar um cluster do serviço de Kubernetes do Azure (AKS)](../../aks/kubernetes-walkthrough-portal.md) e siga as etapas na seção **monitorar a integridade e os logs de**.  

>[!NOTE]
>Ao seguir as etapas no artigo de início rápido para habilitar o monitoramento para o cluster do AKS do portal, você precisará selecione um espaço de trabalho do Log Analytics existente ou crie um novo. 

### <a name="enable-using-azure-cli"></a>Habilitar usando a CLI do Azure
Para habilitar o monitoramento de um novo cluster do AKS criado com a CLI do Azure, siga a etapa no artigo de início rápido na seção [Criar cluster do AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.59 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

### <a name="enable-using-terraform"></a>Habilitar usando Terraform
Se estiver [implantando um novo cluster do AKS usando o Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), especifique os argumentos necessários no perfil [para criar um espaço de trabalho do Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) se não optar por especificar um existente. 

>[!NOTE]
>Se optar por usar o Terraform, você deverá estar executando o Provedor de RM do Terraform do Azure versão 1.17.0 ou superior.

Para adicionar o Azure Monitor para contêineres ao workspace, consulte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) e preencha o perfil incluindo o [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) e especifique **oms_agent**. 

Depois de habilitar o monitoramento e todas as tarefas de configuração terem sido concluídas com sucesso, você poderá monitorar o desempenho do cluster de uma destas duas maneiras:

* Diretamente no cluster do AKS selecionando **Integridade** no painel esquerdo.
* Ao selecionar o bloco **Monitorar insights do contêiner** na página de cluster do AKS do cluster selecionado. No Azure Monitor, no painel esquerdo, selecione **Integridade**. 

  ![Opções para selecionar o Azure Monitor para contêineres no AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>Habilitar monitoramento para clusters gerenciados existentes
Você pode habilitar o monitoramento de um cluster do AKS que já esteja implantado usando um dos métodos com suporte:

* CLI do Azure
* Terraform
* [Do Azure Monitor](#enable-from-azure-monitor-in-the-portal) ou [diretamente do cluster do AKS](#enable-directly-from-aks-cluster-in-the-portal) no portal do Azure 
* Com o [fornecidos o modelo do Resource Manager](#enable-using-an-azure-resource-manager-template) usando o cmdlet do PowerShell do Azure `New-AzResourceGroupDeployment` ou com a CLI do Azure. 

### <a name="enable-using-azure-cli"></a>Habilitar usando a CLI do Azure
A etapa a seguir permite o monitoramento do cluster do AKS usando a CLI do Azure. Neste exemplo, não é necessário criar ou especificar um workspace existente. Esse comando simplificará o processo, criando um workspace padrão no grupo de recursos padrão da assinatura do cluster do AKS, se ainda não existir um na região.  O workspace padrão criado é semelhante ao formato do *DefaultWorkspace-\<GUID>-\<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A saída será semelhante à seguinte:

```azurecli
provisioningState       : Succeeded
```

Caso prefira se integrar a um workspace existente, use o seguinte comando para especificar esse workspace.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

A saída será semelhante à seguinte:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-using-terraform"></a>Habilitar usando Terraform
1. Adicione o perfil de complemento **oms_agent** ao [recurso azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) existente

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adicione [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) após as etapas na documentação do Terraform.

### <a name="enable-from-azure-monitor-in-the-portal"></a>Habilitar o Azure Monitor no portal 
Para habilitar o monitoramento do seu contêiner AKS no portal do Azure por meio do Azure Monitor, faça o seguinte:

1. No portal do Azure, selecione **Monitor**. 
2. Selecione **Contêineres** na lista.
3. Na página **Monitor – contêineres**, selecione **Clusters não monitorados**.
4. Na lista de clusters não monitorados, localize o contêiner na lista e clique em **Habilitar**.   
5. Na página **Integração do Azure Monitor para contêineres**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura do cluster, selecione-o na lista suspensa.  
    A lista seleciona previamente o workspace e o local padrão no qual o contêiner do AKS está implantado na assinatura. 

    ![Habilitar o monitoramento de insights do contêiner do AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as instruções em [Criar um espaço de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Crie o workspace na mesma assinatura em que o contêiner AKS é implantado. 
 
Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster. 

### <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Habilitar diretamente do cluster do AKS no portal
Para habilitar o monitoramento diretamente de um dos seus clusters AKS no portal do Azure, faça o seguinte:

1. No portal do Azure, clique em **Todos os serviços**. 
2. Na lista de recursos, comece digitando **Contêineres**.  
    A lista filtra com base na sua entrada. 
3. Selecione **Serviços de Kubernetes**.  

    ![O link de serviços do Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Na lista de contêineres, selecione um contêiner.
5. Na página de visão geral do contêiner, selecione **Monitorar contêineres**.  
6. Na página **Integração do Azure Monitor para contêineres**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura do cluster, selecione-o na lista suspensa.  
    A lista seleciona previamente o workspace e o local padrão no qual o contêiner do AKS está implantado na assinatura. 

    ![Habilitar o monitoramento de integridade do contêiner do AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as instruções em [Criar um espaço de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Crie o workspace na mesma assinatura em que o contêiner AKS é implantado. 
 
Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir dados operacionais para o cluster. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Habilitar o uso de um modelo do Azure Resource Manager
Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

* A ID de recurso do contêiner do AKS. 
* O grupo de recursos no qual o cluster está implantado.

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.
>

O espaço de trabalho do Log Analytics deve ser criado antes de habilitar o monitoramento usando o Azure PowerShell ou CLI. Para criar o workspace, você pode configurá-lo por meio do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), por meio do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.59 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     }
     ]
    }
    ```

2. Salve esse arquivo como **existingClusterOnboarding.json** em uma pasta local.
3. Cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       }  
     }
    }
    ```

4. Edite os valores **aksResourceId** e **aksResourceLocation** usando os valores no **visão geral do AKS** página para o cluster do AKS. O valor para **workspaceResourceId** é a ID do recurso completa do espaço de trabalho do Log Analytics, que inclui o nome do espaço de trabalho. 
5. Salve esse arquivo como **existingClusterParam.json** em uma pasta local.
6. Você está pronto para implantar o modelo. 

   * Para implantar com o Azure PowerShell, use os seguintes comandos na pasta que contém o modelo:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Para implantar com a CLI do Azure, execute os seguintes comandos:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```azurecli
       provisioningState       : Succeeded
       ```
     Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implantação do agente e solução
Com a versão do agente *06072018* ou posterior, você pode verificar se tanto o agente quanto a solução foram implantados com êxito. Com versões anteriores do agente, você pode verificar somente a implantação do agente.

### <a name="agent-version-06072018-or-later"></a>Agente versão 06072018 ou posterior
Execute o seguinte comando para verificar se o agente foi implantado com êxito. 

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implantação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão do agente anterior a 06072018

Para verificar se a versão do agente do Log Analytics liberada antes de *06072018* está implementada corretamente, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Exibir configuração com CLI
Use o comando `aks show` para obter detalhes, como se a solução estivesse habilitada ou não, o que é o resourceID do espaço de trabalho do Log Analytics e detalhes de resumo sobre o cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando concluirá e retornará informações no formato JSON sobre a solução.  Os resultados do comando deverão mostrar o perfil do complemento de monitoramento e serem semelhantes à seguinte saída de exemplo:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>Próximas etapas

* Se enfrentar problemas ao tentar carregar a solução, examine o [guia de solução de problemas](container-insights-troubleshoot.md)

* Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster do AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar o Azure Monitor para contêineres, veja [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).
