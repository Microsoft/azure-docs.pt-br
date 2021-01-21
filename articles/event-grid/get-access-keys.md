---
title: Obter a chave de acesso para um recurso da grade de eventos
description: Este artigo descreve como obter a chave de acesso de um domínio ou tópico da grade de eventos
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: 2e258cebe1652178a67c292d0cccab3a151eddf7
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624817"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Obter chaves de acesso para recursos da grade de eventos (tópicos ou domínios)
As chaves de acesso são usadas para autenticar eventos de publicação de um aplicativo para recursos da grade de eventos do Azure (tópicos e domínios). É recomendável regenerar suas chaves regularmente e armazená-las com segurança. Você recebe duas chaves de acesso para que possa manter conexões usando uma chave ao regenerar a outra.

Este artigo descreve como obter chaves de acesso para um recurso da grade de eventos (tópico ou domínio) usando portal do Azure, PowerShell ou CLI. 

## <a name="azure-portal"></a>Portal do Azure
No portal do Azure, alterne para a guia **chaves de acesso** do **tópico da grade de eventos** ou da página de domínio da grade de **eventos** para seu tópico ou domínio.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Página chaves de acesso":::

## <a name="azure-powershell"></a>Azure PowerShell
Use o comando [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) para obter as chaves de acesso para tópicos. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Use o comando [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) para obter chaves de acesso para domínios. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>CLI do Azure
Use a [lista de chaves do tópico AZ eventgrid](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list) para obter as chaves de acesso para tópicos. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Use a [lista de chaves de domínio AZ eventgrid](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list) para obter chaves de acesso para domínios. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo: [autenticar clientes de publicação](security-authenticate-publishing-clients.md). 
