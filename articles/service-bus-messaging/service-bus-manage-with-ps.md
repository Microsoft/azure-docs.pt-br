---
title: "Usar o PowerShell para gerenciar recursos do Barramento de Serviço do Azure | Microsoft Docs"
description: "Use o módulo do PowerShell para criar e gerenciar recursos do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 1520cea72069be5d7bb078a3770d61fade26b853
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Use o módulo do PowerShell para gerenciar recursos do Barramento de Serviço

O PowerShell do Microsoft Azure é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento dos serviços do Azure. Este artigo descreve como usar o [Módulo do PowerShell do Resource Manager do Barramento de Serviço](/powershell/module/azurerm.servicebus) para provisionar e gerenciar entidades do Barramento de Serviço (namespaces, filas, tópicos e assinaturas) usando um script ou console local do Azure PowerShell.

Também é possível gerenciar entidades do Barramento de Serviço usando modelos do Azure Resource Manager. Para obter mais informações, consulte o artigo [Criar recursos do Barramento de Serviço usando modelos do Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará atender aos seguintes pré-requisitos:

* Uma assinatura do Azure. Para saber mais sobre como adquirir uma assinatura, confira [Opções de compra][purchase options], [ofertas para membros][member offers] ou [conta gratuita][free account].
* Um computador com o PowerShell do Azure. Para obter instruções, consulte [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps).
* Um entendimento geral dos scripts do PowerShell, dos pacotes NuGet e do .NET Framework.

## <a name="get-started"></a>Introdução

A primeira etapa é usar o PowerShell para fazer logon em sua conta do Azure e assinatura do Azure. Siga as instruções contidas em [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps) para fazer logon em sua conta do Azure e recuperar e acessar os recursos em sua assinatura do Azure.

## <a name="provision-a-service-bus-namespace"></a>Provisionar um namespace do Barramento de Serviço

Ao trabalhar com namespaces do Barramento de Serviço, você pode usar os cmdlets [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) e [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace).

Este exemplo cria algumas variáveis locais no script; `$Namespace` e `$Location`.

* `$Namespace` é o nome do namespace do Barramento de Serviço com o qual queremos trabalhar.
* `$Location` identifica o data center no qual provisionamos o namespace.
* `$CurrentNamespace` armazena o namespace de referência que recuperamos (ou criamos).

Em um script real, `$Namespace` e `$Location` podem ser passados como parâmetros.

Esta parte do script tem a seguinte função:

1. Tenta recuperar um namespace do Barramento de Serviço com o nome especificado.
2. Se o namespace for encontrado, ele informará o que foi encontrado.
3. Se o namespace não for encontrado, ele vai criar o namespace e, em seguida, recuperar o namespace recentemente criado.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Criar uma regra de autorização de namespace

O exemplo a seguir mostra como gerenciar regras de autorização de namespace usando os cmdlets [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule) e [Remove-AzureRmServiceBusNamespaceAuthorizationRule cmdlets](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila ou um tópico, execute uma verificação de namespace usando o script na seção anterior. Em seguida, crie a fila:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modificar propriedades da fila

Após executar o script na seção anterior, você pode usar o cmdlet [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) para atualizar as propriedades de uma fila, como no exemplo a seguir:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Provisionamento de outras entidades do Barramento de Serviço

Você pode usar o [Módulo do PowerShell do Barramento de Serviço](/powershell/module/azurerm.servicebus) para provisionar outras entidades, como tópicos e assinaturas. Esses cmdlets são sintaticamente semelhantes aos cmdlets de criação de fila demonstrados na seção anterior.

## <a name="next-steps"></a>Próximas etapas

- Consulte a documentação completa sobre o módulo do PowerShell do Resource Manager do Barramento de Serviço [aqui](/powershell/module/azurerm.servicebus). Esta página lista todos os cmdlets disponíveis.
- Para obter informações sobre o uso de modelos do Azure Resource Manager, consulte o artigo [Criar recursos do Barramento de Serviço usando modelos do Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informações sobre [Bibliotecas de gerenciamento .NET do Barramento de Serviço](service-bus-management-libraries.md).

Há algumas formas alternativas de gerenciar entidades do Barramento de Serviço, conforme descrito nessas postagens de blog:

* [Como criar filas, tópicos e assinaturas do Barramento de Serviço usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Como criar um namespace do Barramento de Serviço e um Hub de Eventos usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Scripts do PowerShell do Barramento de Serviço](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
