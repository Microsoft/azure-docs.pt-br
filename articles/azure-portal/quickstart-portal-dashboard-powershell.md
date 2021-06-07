---
title: Criar um painel no portal do Azure com o PowerShell
description: Saiba como criar um painel no portal do Azure usando o Azure PowerShell.
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 03/25/2021
ms.openlocfilehash: cd001a8259c54f1d86aab5983da1413c8163008c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557429"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Início Rápido: Criar um painel no portal do Azure com o PowerShell

Um painel no portal do Azure é uma exibição concentrada e organizada dos seus recursos da nuvem. Este artigo se concentra no processo de usar o módulo do PowerShell Az.Portal para criar um painel.
O painel mostra o desempenho de uma VM (máquina virtual), bem como algumas informações e links estáticos.

## <a name="requirements"></a>Requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo **Az.Portal** do PowerShell estiver em versão prévia, será preciso instalá-lo de modo separado do módulo Az do PowerShell usando o cmdlet `Install-Module`. Quando esse módulo do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Escolher uma assinatura específica do Azure

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definir variáveis

Você usará várias informações repetidamente. Crie variáveis para armazenar as informações.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos com base no nome na variável `$resourceGroupName` na região especificada na variável `$location`.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

O painel que você criará na próxima parte deste guia de início rápido exige uma VM existente. Crie uma VM seguindo estas etapas.

Armazene as credenciais de logon da VM em uma variável. A senha deve ser complexa. Esse é um novo nome de usuário e uma nova senha; não é, por exemplo, a conta que você usa para entrar no Azure. Para obter mais informações, confira [Requisitos de nome de usuário](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) e [Requisitos de senha](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Crie a VM.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

A implantação da VM agora é iniciada e normalmente leva alguns minutos para ser concluída. Após a conclusão da implantação, vá para a próxima seção.

## <a name="download-the-dashboard-template"></a>Baixar o modelo de painel

Como os painéis do Azure são recursos, eles podem ser representados como JSON. O código a seguir baixa uma representação JSON de um painel de exemplo. Para obter mais informações, confira [A estrutura de Painéis do Azure](./azure-portal-dashboards-structure.md).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$HOME\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Personalizar o modelo

Personalize o modelo baixado executando o código a seguir.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Para obter mais informações, confira [Referência de modelos de painéis no portal da Microsoft](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Implantar o modelo de painel

Use o cmdlet `New-AzPortalDashboard`, que faz parte do módulo Az.Portal, para implantar o modelo diretamente do PowerShell.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Examinar os recursos implantados

Verifique se o painel foi criado com êxito.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a VM e o painel associado, exclua o grupo de recursos que os contém.

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
Remove-Item -Path "$HOME\portal-dashboard-template-testvm.json"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os cmdlets contidos no módulo Az.Portal do PowerShell, confira:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: cmdlets do Painel do Portal](/powershell/module/Az.Portal/)