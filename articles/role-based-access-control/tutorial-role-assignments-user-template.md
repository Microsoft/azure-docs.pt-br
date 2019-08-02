---
title: Tutorial – Permitir acesso a um usuário aos recursos do Azure usando o RBAC e o modelo do Resource Manager | Microsoft Docs
description: Saiba como permitir acesso a um usuário aos recursos do Azure usando o RBAC (controle de acesso baseado em função) e modelo do Azure Resource Manager.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: edb20221862e6439b3bc574995f4037cbc95f8f9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668870"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Tutorial: Permitir a um usuário acesso a recursos do Azure usando RBAC e modelo do Resource Manager

O [RBAC (controle de acesso baseado em função)](overview.md) serve para gerenciar o acesso aos recursos do Azure. Neste tutorial, você cria um grupo de recursos e permite acesso a um usuário para criar e gerenciar máquinas virtuais no grupo de recursos. Este tutorial aborda o processo de implantação de um modelo do Resource Manager para permitir o acesso. Para obter mais informações sobre como desenvolver modelos do Resource Manager, confira [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelo](/azure/templates/microsoft.authorization/allversions
).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conceder acesso a um usuário em um escopo do grupo de recursos
> * Validar a implantação
> * Limpar

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar e remover as atribuições de função, você deve ter:

* as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="grant-access"></a>Conceder acesso

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Mais modelos relacionados à autorização do Azure podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Para implantar o modelo, selecione **Experimentar** para abrir o Azure Cloud Shell e, em seguida, cole o script do PowerShell a seguir na janela do shell. Para colar o código, clique com o botão direito do mouse na janela do shell e, em seguida, selecione **Colar**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Validar a implantação

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos criado no último procedimento. O nome padrão é o nome do projeto com **rg** acrescentado.
1. Selecione **Controle de acesso (IAM)** no menu à esquerda.
1. Selecione **Atribuições de função**. 
1. Em **Nome**, insira o endereço de email que você digitou no último procedimento. Você verá que o usuário com o endereço de email tem a função de **Colaborador da Máquina Virtual**.

## <a name="clean-up"></a>Limpar

Para remover o grupo de recursos criado no último procedimento, selecione **Experimentar** para abrir o Azure Cloud Shell e, em seguida, cole o seguinte script do PowerShell na janela do shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Permitir acesso a um usuário aos recursos do Azure usando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)