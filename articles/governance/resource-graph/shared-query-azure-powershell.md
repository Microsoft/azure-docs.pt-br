---
title: 'Início rápido: Criar uma consulta compartilhada com o Azure PowerShell'
description: Neste guia de início rápido, você seguirá as etapas para criar uma consulta compartilhada do Resource Graph usando o Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9015b6df99bdd6f153194e8f4cbbe7658cf1d6dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535799"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Início Rápido: Criar uma consulta compartilhada do Resource Graph usando o Azure PowerShell

Este artigo descreve como você pode criar uma consulta compartilhada do Azure Resource Graph usando o módulo [Az.ResourceGraph](/powershell/module/az.resourcegraph) do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Enquanto o módulo **Az.ResourceGraph** do PowerShell estiver em versão prévia, você precisará instalá-lo separadamente usando o cmdlet `Install-Module`.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Criar uma consulta compartilhada do Resource Graph

Com o módulo **Az.ResourceGraph** do PowerShell adicionado ao ambiente de sua escolha, é hora de criar uma consulta compartilhada do Resource Graph. A consulta compartilhada é um objeto do Azure Resource Manager ao qual você pode conceder permissão ou o qual você pode executar no Explorador do Azure Resource Graph. A consulta resume a contagem de todos os recursos agrupados por _localização_.

1. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para armazenar a consulta compartilhada do Azure Resource Graph. Esse grupo de recursos é chamado `resource-graph-queries`, e a localização é `westus2`.

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Crie a consulta compartilhada do Azure Resource Graph usando o módulo **Az.ResourceGraph** do PowerShell e o cmdlet [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery):

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Liste as consultas compartilhadas no novo grupo de recursos. O cmdlet [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) retorna uma matriz de valores.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Para obter apenas um resultado de consulta compartilhada, use `Get-AzResourceGraphQuery` com seu parâmetro `Name`.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Limpar recursos

Caso deseje remover a consulta compartilhada do Resource Graph e o grupo de recursos do ambiente do Azure, use os seguintes comandos:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma consulta compartilhada do Resource Graph usando o Azure PowerShell. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)
