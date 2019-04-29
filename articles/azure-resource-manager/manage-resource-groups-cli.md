---
title: Gerenciar grupos do Azure Resource Manager usando a CLI do Azure | Microsoft Docs
description: Use a CLI do Azure para gerenciar os grupos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 9245d4c01816d26e262b1c8bd35015e2a6a9558c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550539"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gerenciar grupos de recursos do Azure Resource Manager usando a CLI do Azure

Saiba como usar a CLI do Azure com [do Azure Resource Manager](resource-group-overview.md) para gerenciar os grupos de recursos do Azure. Para gerenciar recursos do Azure, confira [gerenciar recursos do Azure usando a CLI do Azure](./manage-resources-cli.md).

Outros artigos sobre como gerenciar grupos de recursos:

- [Gerenciar grupos de recursos do Azure usando o portal do Azure](./manage-resources-portal.md)
- [Gerenciar grupos de recursos do Azure usando o PowerShell do Azure](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando você especificar um local para o grupo de recursos, você está especificando onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O script CLI a seguir cria um grupo de recursos e, em seguida, mostra o grupo de recursos.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Listar os grupos de recursos

O script CLI a seguir lista os grupos de recursos em sua assinatura.

```azurecli-interactive
az group list
```

Para obter um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

O script CLI a seguir exclui um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte [exclusão do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos em um grupo de recursos

Ver [implantar recursos em um grupo de recursos](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos no grupo, usando um modelo do Resource Manager. Para saber mais, confira [Create resource group and deploy resources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources) (Criar grupo de recursos e implantar recursos).

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Esse recurso também é conhecido como *reversão em erro*. Para obter mais informações, consulte [reimplantar quando ocorre falha na implantação](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou assinatura

Você pode mover os recursos no grupo para outro grupo de recursos. Para obter mais informações, consulte [mover recursos](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

O bloqueio impede que outros usuários na sua organização acidentalmente excluam ou modifiquem recursos críticos, como a assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia um grupo de recursos, o grupo de recursos não pode ser excluído.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

O script a seguir obtém todos os bloqueios para um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

O script a seguir exclui um bloqueio:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Marcar grupos de recursos

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [usando marcas para organizar seus recursos do Azure](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Depois de configurar seu grupo de recursos com êxito, você talvez queira exibir o modelo do Resource Manager para o grupo de recursos. A exportação do modelo oferece dois benefícios:

- Automatize implantações futuras da solução porque o modelo contém toda a infraestrutura completa.
- Aprenda a sintaxe de modelo examinando com a notação JSON (JavaScript Object) que representa sua solução.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

O script exibe o modelo no console.  Copie o JSON e salve como um arquivo.

Para obter mais informações, consulte [grupo de recursos de exportação](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Gerenciar o acesso a grupos de recursos

O [Controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, consulte [gerenciar o acesso usando a CLI do Azure e RBAC](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber o Azure Resource Manager, consulte [visão geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e sintaxe dos modelos do Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte o [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).