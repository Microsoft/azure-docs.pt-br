---
title: Criar um provedor de recursos personalizado do Azure com o Azure PowerShell
description: Descreve como criar um provedor de recursos personalizado do Azure com o Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c19eb41210b6fba1935a0d158c8240375f4f8f5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533919"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Início Rápido: Criar um provedor de recursos personalizado do Azure com o Azure PowerShell

Neste início rápido, você aprenderá a criar um provedor de recursos personalizado do Azure usando o módulo [Az.CustomProviders](/powershell/module/az.customproviders) do PowerShell.

> [!CAUTION]
> No momento, o Azure Custom Providers está em versão prévia pública. A versão prévia é fornecida sem um contrato de nível de serviço. Ela não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Se você optar por usar o Cloud Shell, confira [Visão geral do Azure Cloud Shell](../../cloud-shell/overview.md) para obter mais informações.

> [!IMPORTANT]
> Enquanto o módulo **Az.CustomProviders** do PowerShell estiver em versão prévia, você precisará instalá-lo separadamente usando o cmdlet `Install-Module`. Quando esse módulo do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos com o nome especificado no local especificado.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Criar um provedor de recursos personalizado

Para criar ou atualizar um provedor de recursos personalizado, use o cmdlet [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider), conforme mostrado no exemplo a seguir.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Obter o manifesto do provedor de recursos personalizado

Para recuperar informações sobre o manifesto do provedor de recursos personalizado, use o cmdlet [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider), conforme mostrado no exemplo a seguir.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Criar uma associação

Para criar ou atualizar uma associação, use o cmdlet [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation), conforme mostrado no exemplo a seguir.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Obter uma associação

Para recuperar informações sobre uma associação, use o cmdlet [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation), conforme mostrado no exemplo a seguir.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, você poderá excluí-los executando o exemplo a seguir.

### <a name="delete-an-association"></a>Excluir uma associação

Para remover uma associação, use o cmdlet [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation). O exemplo a seguir exclui uma associação.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Excluir um provedor de recursos personalizado

Para remover um provedor de recursos personalizado, use o cmdlet [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider). O exemplo a seguir exclui um provedor de recursos personalizado.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Provedores de Recursos Personalizados do Azure](overview.md).
