---
title: Dados pessoais
description: Saiba como gerenciar dados pessoais associados às operações do Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 1e531f7cd9992536bcc191637111761c5bbdefa2
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693705"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gerenciar dados pessoais associados com o Azure Resource Manager

Para evitar a exposição de informações confidenciais, exclua todas as informações pessoais fornecidas em implantações, grupos de recursos ou marcas. O Azure Resource Manager fornece operações que permitem que você gerencie dados pessoais fornecidos em implantações, grupos de recursos ou marcas.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Excluir dados pessoais no histórico de implantação

Para implantações, o Resource Manager mantém valores de parâmetro e mensagens de status no histórico de implantação. Esses valores são mantidos até que você exclua a implantação do histórico. Para ver se você forneceu dados pessoais nesses valores, lista as implantações. Se você encontrar dados pessoais, exclua as implantações do histórico.

Para listar **implantações** no histórico, use:

* [Listar por Grupo de Recursos](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [lista de grupos de implantação AZ](/cli/azure/deployment/group#az_deployment_group_list)

Para excluir **implantações** do histórico, use:

* [Excluir](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [excluir grupo de implantação AZ](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Excluir dados pessoais em nomes de grupo de recursos

O nome do grupo de recursos é mantido até que você exclua o grupo de recursos. Para ver se você forneceu dados pessoais nesses valores, lista os grupos de recursos. Se você encontrar dados pessoais, [mova os recursos](move-resource-group-and-subscription.md) para um novo grupo de recursos e exclua o grupo de recursos com os dados pessoais no nome.

Para listar os **grupos de recursos**, use:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

Para excluir os **grupos de recursos**, use:

* [Excluir](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Excluir os dados pessoais em marcas

Nomes e valores de marcas são mantidos até que você exclua ou modifique a marca. Para ver se você forneceu dados pessoais nas marcas, lista as marcas. Se você encontrar dados pessoais, exclua as marcas.

Para listar as **marcas**, use:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

Para excluir as **marcas**, use:

* [Excluir](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral do Azure Resource Manager, confira [O que é o Resource Manager?](overview.md)