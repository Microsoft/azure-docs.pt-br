---
title: Usar o PowerShell para gerenciar recursos dos Hubs de Eventos do Azure | Microsoft Docs
description: "Usar o módulo do PowerShell para criar e gerenciar Hubs de Eventos"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 2b49c01153b1104612e6ebf9c88566fc40d1f635
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Usar o PowerShell para gerenciar recursos dos Hubs de Eventos

O PowerShell do Microsoft Azure é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento dos serviços do Azure. Este artigo descreve como usar o [Módulo PowerShell do Gerenciador de Recursos dos Hubs de Eventos](/powershell/module/azurerm.eventhub) para provisionar e gerenciar entidades dos Hubs de Eventos (namespaces, hubs de eventos individuais e grupos de consumidores) usando um script ou console local do Azure PowerShell.

Também é possível gerenciar recursos dos Hubs de Eventos usando modelos do Azure Resource Manager. Para saber mais, confira o artigo [Criar um namespace dos Hubs de Eventos com um hub de eventos e um grupo de consumidores usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará do seguinte:

* Uma assinatura do Azure. Para saber mais sobre como adquirir uma assinatura, confira [Opções de compra][purchase options], [ofertas para membros][member offers] ou [conta gratuita][free account].
* Um computador com o PowerShell do Azure. Para obter instruções, consulte [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps).
* Um entendimento geral dos scripts do PowerShell, dos pacotes NuGet e do .NET Framework.

## <a name="get-started"></a>Introdução

A primeira etapa é usar o PowerShell para fazer logon em sua conta do Azure e assinatura do Azure. Siga as instruções contidas em [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps) para fazer logon em sua conta do Azure, bem como para recuperar e acessar os recursos em sua assinatura do Azure.

## <a name="provision-an-event-hubs-namespace"></a>Provisionar um namespace dos Hubs de Eventos

Ao trabalhar com namespaces dos Hubs de Eventos, você pode usar os cmdlets [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace) e [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace).

Este exemplo cria algumas variáveis locais no script; `$Namespace` e `$Location`.

* `$Namespace` é o nome do namespace dos Hubs de Eventos com que queremos trabalhar.
* `$Location` identifica o datacenter em que iremos provisionar o namespace.
* `$CurrentNamespace` armazena o namespace de referência que recuperamos (ou criamos).

Em um script real, `$Namespace` e `$Location` podem ser passados como parâmetros.

Esta parte do script tem a seguinte função:

1. Tenta recuperar um namespace dos Hubs de Eventos com o nome especificado.
2. Se o namespace for encontrado, ele informará o que foi encontrado.
3. Se o namespace não for encontrado, ele vai criar o namespace e, em seguida, recuperar o namespace recentemente criado.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Para criar um hub de eventos, execute uma verificação de namespace usando o script na seção anterior. Em seguida, use o cmdlet [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) para criar o hub de eventos:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Para criar um grupo de consumidores em um hub de eventos, execute as verificações de namespace e hub de eventos usando os scripts na seção anterior. Em seguida, use o cmdlet [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) para criar o grupo de consumidores no hub de eventos. Por exemplo:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Definir metadados do usuário

Após executar os scripts nas seções anteriores, você pode usar o cmdlet [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) para atualizar as propriedades de um grupo de consumidores, como no exemplo a seguir:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Remover hub de eventos

Para remover os hubs de eventos que criou, você pode usar os cmdlets `Remove-*`, como no seguinte exemplo:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Próximas etapas

- Consulte a documentação completa sobre o módulo do PowerShell do Gerenciador de Recursos dos Hubs de Eventos [aqui](/powershell/module/azurerm.eventhub). Esta página lista todos os cmdlets disponíveis.
- Para saber mais sobre como usar modelos do Azure Resource Manager, confira o artigo [Criar um namespace dos Hubs de Eventos com um hub de eventos e um grupo de consumidores usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Informações sobre [Bibliotecas de gerenciamento .NET dos Hubs de Eventos](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
