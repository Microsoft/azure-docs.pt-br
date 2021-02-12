---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 596b73f8fb205b6a5681fecf3d00fd2a67c1f59f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628645"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso Âncoras Espaciais

### <a name="portal"></a>[Portal](#tab/azure-portal)

Vá para o <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

No painel esquerdo, clique em **Criar um recurso**.

Use a caixa de pesquisa para **Âncoras Espaciais**.

![Uma captura de tela mostrando os resultados de uma pesquisa de Âncoras Espaciais.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecione **Âncoras Espaciais** e clique em **Criar**.

No painel **Conta de Âncoras Espaciais**, faça o seguinte:

* Insira um nome de recurso exclusivo usando caracteres alfanuméricos normais.
* Selecione a assinatura que você deseja anexar o recurso.
* Crie um grupo de recursos, selecionando **Criar novo**. Nomeie-o **myResourceGroup** e clique em **OK**.

  [!INCLUDE [resource group intro text](resource-group.md)]

* Selecione um local (região) para criar o recurso.
* Selecione **Novo** para começar a criar o recurso.

![Uma captura de tela do painel de Âncoras Espaciais para a criação de um recurso.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Depois que o recurso for criado, o portal do Azure mostrará que a implantação foi concluída.

![Uma captura de tela mostrando que a implantação do recurso foi concluída.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Selecione **Ir para o recurso**. Agora você pode ver as propriedades do recurso.

Copie o valor da **ID da Conta** do recurso em um editor de texto para usar posteriormente.

![Uma captura de tela do painel de propriedades do recurso.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Copie também o valor do **Domínio de Contas** do recurso em um editor de texto para usar posteriormente.

![Uma captura de tela mostrando o valor do domínio de contas do recurso.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Em **Configurações**, selecione **Chave**. Copie o valor da **Chave primária**, **Chave de Conta**, em um editor de texto para usar posteriormente.

![Uma captura de tela do painel Chaves da conta.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Comece preparando seu ambiente para a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Depois de se conectar, use o comando [az account set](/cli/azure/account#az_account_set) para escolher a assinatura na qual a conta de âncoras espaciais será configurada:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos ou use um grupo de recursos existente:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Veja suas contas atuais de âncoras espaciais para um grupo de recursos usando o comando [az spatial-anchors-account list](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list):

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Veja também as contas de âncoras espaciais para sua assinatura:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Execute o comando [az spatial-anchors-account create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) para criar sua conta de âncoras espaciais:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Veja as propriedades do recurso usando o comando [az spatial-anchors-account show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show):

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Copie os valores de **ID da Conta** e **Domínio da Conta** do recurso em um editor de texto para uso posterior.

1. Execute o comando [az spatial-anchors-account key show](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) para obter suas chaves primária e secundária:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Copie os valores das chaves em um editor de texto para uso posterior.

   Caso precise regenerar as chaves, use o comando [az spatial-anchors-account key renew](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew):

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

Exclua uma conta usando o comando [az spatial-anchors-account delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete):

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Comece preparando seu ambiente para o Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Enquanto o módulo **Az.MixedReality** do PowerShell estiver em versão prévia, você precisará instalá-lo separadamente usando o cmdlet `Install-Module`. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Depois de entrar, use o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) para escolher a assinatura na qual a conta de âncoras espaciais será configurada:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Execute o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para criar um grupo de recursos ou use um grupo de recursos existente:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Veja suas contas atuais de âncoras espaciais para um grupo de recursos usando o cmdlet [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount):

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Veja também as contas de âncoras espaciais para sua assinatura:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Execute o cmdlet [New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) para criar sua conta de âncoras espaciais:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Veja as propriedades do recurso usando o cmdlet [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount):

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Copie o valor de **accountId** da propriedade e o valor de **accountDomain** da propriedade em um editor de texto para uso posterior.

1. Execute o cmdlet [Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) para obter suas chaves primária e secundária:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Copie os valores das chaves em um editor de texto para uso posterior.

   Caso precise regenerar as chaves, use o cmdlet [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey):

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

Exclua uma conta usando o cmdlet [Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount):

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
