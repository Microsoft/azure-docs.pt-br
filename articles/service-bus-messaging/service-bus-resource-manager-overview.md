---
title: Criar recursos do barramento de serviço do Azure usando modelos
description: Usar modelos do Azure Resource Manager para automatizar a criação de recursos do Barramento de Serviço
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075249"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar recursos do Barramento de Serviço usando modelos do Azure Resource Manager

Este artigo descreve como criar e implantar recursos do Barramento de Serviço usando modelos do Azure Resource Manager, o PowerShell e o provedor de recursos do Barramento de Serviço.

Os modelos do Azure Resource Manager ajudam você a definir os recursos a serem implantados em uma solução e a especificar os parâmetros e variáveis que lhe permitem inserir valores para diferentes ambientes. O modelo é gravado em JSON e consiste de expressões que você pode usar para construir valores para sua implantação. Para obter informações detalhadas sobre a criação de modelos do Azure Resource Manager e uma discussão sobre o formato do modelo, consulte [Estrutura e sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> Os exemplos neste artigo mostram como usar o Azure Resource Manager para criar um namespace do Barramento de Serviço e uma entidade de mensagens (fila). Para obter outros exemplos de modelo, visite a [Galeria de Modelos de Início Rápido do Azure][Azure Quickstart Templates gallery] e pesquise **Barramento de Serviço**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Barramento de Serviço

Esses modelos do Azure Resource Manager no Barramento de Serviço estão disponíveis para download e implantação. Clique nos links abaixo para obter detalhes sobre cada um, com links para os modelos no GitHub:

* [Criar um namespace do Barramento de Serviço](service-bus-resource-manager-namespace.md)
* [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
* [Criar um namespace do Barramento de Serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
* [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um namespace do Barramento de Serviço com tópico, assinatura e regra](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implantar com o PowerShell

O procedimento a seguir descreve como usar o PowerShell para implantar um modelo do Azure Resource Manager que cria um namespace de Barramento de Serviço de camada Standard e uma fila dentro desse namespace. Este exemplo se baseia no modelo [Criar um namespace de Barramento de Serviço com fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue). O fluxo de trabalho é mais ou menos o seguinte:

1. Instale o PowerShell.
2. Crie o modelo e (opcionalmente) um arquivo de parâmetro.
3. No PowerShell, faça logon em sua conta do Azure.
4. Crie um novo grupo de recursos se já não tiver um.
5. Teste a implantação.
6. Se desejar, defina o modo de implantação.
7. Implante o modelo.

Para obter informações completas sobre a implantação de modelos do Azure Resource Manager, consulte [Implantar recursos com modelos do Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalar o PowerShell

Instale o Azure PowerShell seguindo as instruções em [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Criar um modelo

Clone o repositório ou copie o modelo [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) do GitHub:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Criar um arquivo de parâmetros (opcional)

Para usar um arquivo de parâmetros opcionais, copie o arquivo [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json). Substitua o valor de `serviceBusNamespaceName` pelo nome do namespace do Barramento de Serviço que você deseja criar nessa implantação e substitua o valor do `serviceBusQueueName` pelo nome da fila que deseja criar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Para saber mais, consulte o tópico [Parâmetros](../azure-resource-manager/templates/parameter-files.md).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Fazer logon no Azure e definir a assinatura do Azure

Em um prompt do PowerShell, execute o seguinte comando:

```powershell
Connect-AzAccount
```

Você precisará entrar em sua conta do Azure. Após o logon, execute o comando a seguir para exibir as assinaturas disponíveis:

```powershell
Get-AzSubscription
```

Esse comando retorna uma lista de assinaturas do Azure disponíveis. Escolha uma assinatura para a sessão atual executando o comando a seguir. Substitua `<YourSubscriptionId>` pelo GUID da assinatura do Azure que deseja usar:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Caso não tenha um grupo de recursos existente, crie um grupo de recursos com o comando **New-AzResourceGroup**. Forneça o nome do grupo de recursos e local que você deseja usar. Por exemplo:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Se for bem-sucedido, um resumo do novo grupo de recursos será exibido.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Teste a implantação

Valide a implantação executando o cmdlet `Test-AzResourceGroupDeployment`. Ao testar a implantação, forneça parâmetros exatamente como faria durante a sua execução.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implantação

Para criar a nova implantação, execute o cmdlet `New-AzResourceGroupDeployment` e forneça os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o arquivo do modelo. Caso o parâmetro **Mode** não esteja especificado, o valor padrão de **Incremental** será usado. Para saber mais, consulte [Implantações incrementais e completas](../azure-resource-manager/templates/deployment-modes.md).

O comando abaixo solicita os três parâmetros na janela do PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um arquivo de parâmetros em vez disso, use o comando a seguir:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Você também pode usar parâmetros embutidos quando executa o cmdlet de implantação. O comando é o seguinte:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma implantação [completa](../azure-resource-manager/templates/deployment-modes.md), defina o parâmetro **Mode** como **Complete**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Verificar a implantação
Se os recursos forem implantados com êxito, um resumo da implantação será exibido na janela do PowerShell:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Próximas etapas
Agora você já viu o fluxo de trabalho básico e os comandos para implantar um modelo do Azure Resource Manager. Para obter informações mais detalhadas, visite os seguintes links:

* [Visão geral do Azure Resource Manager][Azure Resource Manager overview]
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Criando modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/template-syntax.md)
* [Tipos de recursos do Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
