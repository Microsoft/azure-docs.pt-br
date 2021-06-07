---
title: Implantar o Azure Monitor para soluções SAP com o Azure PowerShell
description: Implantar o Azure Monitor para soluções SAP com o Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.date: 09/08/2020
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0af2611bada7a9aad206ce7463ef72ec930c89a2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538700"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>'Início Rápido: Implantar o Azure Monitor para Soluções SAP com o Azure PowerShell

Este artigo descreve como você pode criar recursos do Azure Monitor para Soluções SAP usando o módulo [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) do PowerShell.

> [!CAUTION]
> No momento, o Azure Monitor para Soluções SAP está em versão prévia pública. A versão prévia é fornecida sem um contrato de nível de serviço. Ela não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Se você optar por usar o Cloud Shell, confira [Visão geral do Azure Cloud Shell](../../../cloud-shell/overview.md) para obter mais informações.

> [!IMPORTANT]
> Enquanto o módulo **Az.HanaOnAzure** do PowerShell está na versão prévia, você precisa instalá-lo separadamente usando o cmdlet `Install-Module`. Quando esse módulo do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../../../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos com o nome especificado no local especificado.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>Monitor SAP

Para criar um monitor SAP, use o cmdlet [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor). O exemplo a seguir cria um monitor SAP para a assinatura, o grupo de recursos e o nome do recurso especificados.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Para recuperar as propriedades de um monitor SAP, use o cmdlet [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor). O exemplo a seguir obtém as propriedades de um monitor SAP para a assinatura, o grupo de recursos e o nome do recurso especificados.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Instância do provedor

Para criar uma instância de provedor, use o cmdlet [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance). O exemplo a seguir cria uma instância de provedor para a assinatura, o grupo de recursos e o nome do recurso especificados.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Para recuperar propriedades de uma instância de provedor, use o cmdlet [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance). O exemplo a seguir obtém as propriedades de uma instância de provedor para a assinatura, o grupo de recursos, o nome do SapMonitor e o nome do recurso especificados.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, você poderá excluí-los executando o exemplo a seguir.

### <a name="delete-the-provider-instance"></a>Excluir a instância de provedor

Para remover uma instância de provedor, use o cmdlet [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance). O exemplo a seguir exclui uma instância de provedor para a assinatura, o grupo de recursos, o nome do SapMonitor e o nome do recurso especificados.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Excluir o monitor SAP

Para remover um monitor SAP, use o cmdlet [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor). O exemplo a seguir exclui um monitor SAP para a assinatura, o grupo de recursos e o nome do monitor especificados.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Azure Monitor para Soluções SAP](azure-monitor-overview.md).
