---
title: Criar um namespace do barramento de serviço do Azure usando o modelo
description: Usar modelo do Azure Resource Manager para criar um namespace das Mensagens do Barramento de Serviço
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 1b7aafca331170100ce99c084a11c96c97df7781
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067385"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Criar um namespace do barramento de serviço usando um modelo de Azure Resource Manager

Saiba como implantar um modelo de Azure Resource Manager para criar um namespace do barramento de serviço. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Para obter mais informações sobre como criar modelos, consulte a [documentação do Azure Resource Manager](../azure-resource-manager/index.yml).

Os modelos a seguir também estão disponíveis para a criação de namespaces do barramento de serviço:

* [Criar um namespace do Barramento de Serviço com fila](./service-bus-resource-manager-namespace-queue.md)
* [Criar um namespace do Barramento de Serviço com tópico e assinatura](./service-bus-resource-manager-namespace-topic.md)
* [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](./service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um namespace do Barramento de Serviço com tópico, assinatura e regra](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um namespace de barramento de serviço

Neste guia de início rápido, você usa um [modelo existente do Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Para encontrar mais exemplos de modelos, consulte [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Para criar um namespace do barramento de serviço implantando um modelo:

1. Selecione **Experimente** no seguinte bloco de código e siga as instruções para entrar no Azure cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome do grupo de recursos é o nome do namespace do barramento de serviço com **RG** anexado.

2. Selecione **Copiar** para copiar o script do PowerShell.
3. Clique com o botão direito do mouse no console do Shell e selecione **Colar**.

Pode demorar alguns minutos para criar um hub de eventos.

## <a name="verify-the-deployment"></a>Verificar a implantação

Para ver o namespace do barramento de serviço implantado, você pode abrir o grupo de recursos no portal do Azure ou usar o script de Azure PowerShell a seguir. Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira e segunda linhas do script a seguir.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implantação de modelo, consulte:

* [Usando o portal do Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Usando CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).
* [Usando a API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira e segunda linhas do script a seguir.

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