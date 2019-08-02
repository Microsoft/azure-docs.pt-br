---
title: Consultar assinaturas de Grade de Eventos do Azure
description: Descreve como listar assinaturas de Grade de Eventos do Azure.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: spelluru
ms.openlocfilehash: 08fa9ada97c652ca609a499da1c8e87f7e4487db
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786622"
---
# <a name="query-event-grid-subscriptions"></a>Consultar assinaturas de Grade de Eventos 

Este artigo descreve como listar as assinaturas de Grade de Eventos em sua assinatura do Azure. Ao consultar as assinaturas existentes de Grade de Eventos, é importante entender os diferentes tipos de assinaturas. Você fornecer parâmetros diferentes com base no tipo de assinatura que deseja obter.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Grupos de recursos e assinaturas do Azure

Os grupos de recursos e as assinaturas do Azure não são recursos do Azure. Portanto, as assinaturas de grade de eventos para grupos de recursos ou assinaturas do Azure não têm as mesmas propriedades de assinaturas de grade de eventos para recursos do Azure. As assinaturas de grade de eventos para grupos de recursos ou assinaturas do Azure são consideradas globais.

Para obter as assinaturas de grade de eventos para uma assinatura do Azure e seus grupos de recursos, você não precisa fornecer nenhum parâmetro. Certifique-se de que tenha selecionado a assinatura do Azure que você deseja consultar. Os exemplos a seguir não obtêm assinaturas de grade de eventos para tópicos personalizados ou recursos do Azure.

Para a CLI do Azure, use:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Para o PowerShell, use:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Para obter as assinaturas de grade de eventos para uma assinatura do Azure, forneça o tipo de tópico de **Microsoft.Resources.Subscriptions**.

Para a CLI do Azure, use:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Para obter as assinaturas de grade de eventos para uma assinatura do Azure, forneça o tipo de tópico de **Microsoft.Resources.Subscriptions**.

Para a CLI do Azure, use:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Para obter as assinaturas de grade de eventos para um grupo de recursos especificado, forneça o nome do grupo de recursos como um parâmetro.

Para a CLI do Azure, use:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Tópicos personalizados e recursos do Azure

Os tópicos personalizados de grade de eventos são recursos do Azure. Portanto, você deve consultar as assinaturas de grade de eventos para os tópicos personalizados e outros recursos, como a conta de armazenamento de Blob, da mesma maneira. Para obter assinaturas de grade de eventos para tópicos personalizados, você deve fornecer parâmetros que identificam o recurso ou identificam o local do recurso. Não é possível consultar amplamente as assinaturas de grade de eventos para recursos em sua assinatura do Azure.

Para obter as assinaturas de grade de eventos para tópicos personalizados e outros recursos em um local, forneça o nome do local.

Para a CLI do Azure, use:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Para obter assinaturas de tópicos personalizados para um local, forneça o local e o tipo de tópico de **Microsoft.EventGrid.Topics**.

Para a CLI do Azure, use:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Para obter as assinaturas para contas de armazenamento para um local, forneça o local e o tipo de tópico de **Microsoft.Storage.StorageAccounts**.

Para a CLI do Azure, use:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Para obter as assinaturas de grade de eventos para um tópico personalizado, forneça o nome do tópico personalizado e o nome do seu grupo de recursos.

Para a CLI do Azure, use:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Para obter assinaturas de grade de eventos para um recurso específico, forneça a ID de recurso.

Para a CLI do Azure, use:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Para o PowerShell, use:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
