---
title: Como interromper o monitoramento do cluster do Serviço de Kubernetes do Azure | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento do seu cluster do Azure AKS com o Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 476934d84d8ffc63336ec620432db3507b2cbb34
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943835"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Como interromper o monitoramento do Azure Monitor do Serviço de Kubernetes do Azure (AKS) para contêineres

Após habilitar o monitoramento do cluster do AKS você poderá parar o monitoramento do cluster, caso decida não continuar monitorando-o. Este artigo mostra como fazer isso usando a CLI do Azure ou com os modelos do Azure Resource Manager fornecidos.  


## <a name="azure-cli"></a>CLI do Azure

Use o comando [az aks disable-complementos](/cli/azure/aks#az-aks-disable-addons) para desabilitar o Azure Monitor para contêineres. O comando Remove o agente dos nós de cluster, ele não remove a solução ou os dados já coletados e armazenados em seu recurso de Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Para habilitar novamente o monitoramento para seu cluster, consulte [Habilitar o monitoramento usando a CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

São fornecido dois modelos do Azure Resource Manager para dar suporte para remover os recursos de solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON especificando a configuração para parar o monitoramento e o outro contém valores de parâmetros que você configura para especificar a ID do recurso de cluster do AKS e o grupo de recursos no qual o cluster está implementado.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos do cluster. Se você omitir quaisquer outras propriedades ou complementos ao usar esse modelo, isso poderá resultar na remoção do cluster. Por exemplo, *enableRBAC* para políticas de RBAC kubernetes implementadas em seu cluster ou *aksResourceTagValues* se as marcas forem especificadas para o cluster AKs.  
>

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Criar modelo

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
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Salve esse arquivo como **OptOutTemplate.json** em uma pasta local.

3. Cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Edite o valor para **aksResourceId** e **aksResourceLocation** com usando os valores do cluster do AKS, que você pode encontrar na página **Propriedades** para o cluster selecionado.

    ![Página de propriedades do contêiner](media/container-insights-optout/container-properties-page.png)

    Enquanto você estiver na página **Propriedades**, copie também a **ID do Recurso de Workspace**. Esse valor será necessário se você decidir que deseja excluir o espaço de trabalho do Log Analytics posteriormente. A exclusão do espaço de trabalho do Log Analytics não é executada como parte desse processo.

    Edite os valores de **aksResourceTagValues** para corresponder aos valores de marca existentes especificados para o cluster AKs.

5. Salve esse arquivo como **OptOutParam.json** em uma pasta local.

6. Você está pronto para implantar o modelo.

### <a name="remove-the-solution-using-azure-cli"></a>Remover a solução usando a CLI do Azure

Execute o comando a seguir com a CLI do Azure no Linux para remover a solução e limpar a configuração no cluster do AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remover a solução usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute os seguintes comandos do PowerShell na pasta que contém o modelo para remover a solução e limpar a configuração do cluster do AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Próximas etapas

Se o workspace foi criado apenas para dar suporte ao monitoramento do cluster e não for mais necessário, você terá que excluí-lo manualmente. Se você não souber como excluir um espaço de trabalho, confira [Excluir um espaço de trabalho do Azure Log Analytics com o portal do Azure](../platform/delete-workspace.md). Não se esqueça de que a **ID de recurso do espaço de trabalho** foi copiada anteriormente na etapa 4, você precisará dela.
