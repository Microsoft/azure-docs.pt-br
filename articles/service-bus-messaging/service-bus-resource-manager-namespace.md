---
title: Criar um namespace das Mensagens do Barramento de Serviço usando modelo do Azure Resource Manager | Microsoft Docs
description: Usar modelo do Azure Resource Manager para criar um namespace das Mensagens do Barramento de Serviço
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444765"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Criar um namespace do barramento de serviço usando um modelo do Azure Resource Manager

Saiba como implantar um modelo do Azure Resource Manager para criar um namespace do barramento de serviço. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Para obter mais informações sobre a criação de modelos, consulte [documentação do Azure Resource Manager](/azure/azure-resource-manager/).

Os modelos a seguir também estão disponíveis para a criação de namespaces do barramento de serviço:

* [Criar um namespace do Barramento de Serviço com fila](./service-bus-resource-manager-namespace-queue.md)
* [Criar um namespace do Barramento de Serviço com tópico e assinatura](./service-bus-resource-manager-namespace-topic.md)
* [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](./service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um namespace do Barramento de Serviço com tópico, assinatura e regra](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um namespace de barramento de serviço

Neste início rápido, você deve usar um [modelo do Resource Manager existente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Para obter mais exemplos de modelo, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Para criar um namespace de barramento de serviço Implantando um modelo:

1. Selecione **Experimente** do bloco de código a seguir e, em seguida, siga as instruções para entrar no Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome do grupo de recursos é o nome do namespace do barramento de serviço com **rg** acrescentado.

2. Selecione **Copiar** para copiar o script do PowerShell.
3. Clique com botão direito do console do shell e, em seguida, selecione **colar**.

Demora alguns minutos para criar um hub de eventos.

## <a name="verify-the-deployment"></a>Verificar a implantação

Para ver o namespace do barramento de serviço implantado, abra o grupo de recursos do portal do Azure ou use o seguinte script do PowerShell do Azure. Se o Cloud shell ainda estiver aberto, você não precisa copiar/executar a primeira e segunda linhas do script a seguir.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

O Azure PowerShell é usado para implantar o modelo neste tutorial. Para obter outros métodos de implantação de modelo, consulte:

* [Usando o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Usando a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Usando a API REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. Se o Cloud shell ainda estiver aberto, você não precisa copiar/executar a primeira e segunda linhas do script a seguir.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace do Barramento de Serviço. Consulte outros guias de início rápido para saber como criar filas, tópicos/assinaturas e como usá-los:

* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Introdução aos tópicos do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
