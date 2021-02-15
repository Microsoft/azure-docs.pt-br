---
title: Publicar eventos com domínios de evento com a grade de eventos do Azure
description: Mostra como gerenciar grandes conjuntos de tópicos na Grade de Eventos do Azure e publicar eventos para eles usando domínios de eventos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e6861e89def10eec391bf302b1ddc726b38bb98c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109888"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gerenciar tópicos e publicar eventos usando domínios de eventos

Este artigo mostra como:

* Criar um domínio de Grade de Eventos
* Assinar tópicos da grade de eventos
* Listar chaves
* Publicar eventos em um domínio

Para saber mais sobre domínios de eventos, consulte [Entender os domínios de eventos para gerenciar tópicos da Grade de Eventos](event-domains.md).

## <a name="create-an-event-domain"></a>Crie um domínio de evento

Para gerenciar grandes conjuntos de tópicos, crie um domínio de evento.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
New-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

A criação com êxito retornará os seguintes valores:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Observe que `endpoint` e `id` são necessários para gerenciar o domínio e publicar eventos.

## <a name="manage-access-to-topics"></a>Gerenciar o acesso aos tópicos

O gerenciamento de acesso aos tópicos é feito por meio da [atribuição de função](../role-based-access-control/role-assignments-cli.md). A atribuição de função usa o controle de acesso baseado em função do Azure para limitar as operações nos recursos do Azure a usuários autorizados em um determinado escopo.

A Grade de Eventos possui duas funções internas que podem ser utilizadas para atribuir acesso de usuários específicos a vários tópicos em um domínio. Essas funções são `EventGrid EventSubscription Contributor (Preview)`, o que permite a criação e exclusão de assinaturas, e `EventGrid EventSubscription Reader (Preview)`, que permite apenas a listagem de assinaturas de eventos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)
O comando da CLI do Azure a seguir limita `alice@contoso.com` para criação e exclusão de assinaturas de eventos apenas no tópico `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
O comando do PowerShell a seguir limita `alice@contoso.com` para criação e exclusão de assinaturas de eventos apenas no tópico `demotopic1`:

```azurepowershell-interactive
New-AzRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Para obter mais informações sobre como gerenciar o acesso às operações da Grade de Eventos, consulte [Segurança e autenticação da Grade de Eventos](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Criar tópicos e assinaturas

O serviço Grade de Eventos cria e gerencia automaticamente o tópico correspondente em um domínio com base na chamada para criar uma assinatura de evento para um tópico de domínio. Não há uma etapa separada para criar um tópico em um domínio. Da mesma forma, quando a última assinatura de evento de um tópico é excluída, o tópico também é excluído.

Assinar um tópico em um domínio é o mesmo que assinar qualquer outro recurso do Azure. Para a ID do recurso de origem, especifique a ID de domínio do evento retornado ao criar o domínio anteriormente. Para especificar o tópico que você quer assinar, adicione `/topics/<my-topic>` ao final da ID do recurso de origem. Para criar uma assinatura de evento de escopo de domínio que receba todos os eventos no domínio, especifique a ID de domínio do evento sem especificar nenhum tópico.

Normalmente, o usuário a quem você concedeu acesso na seção anterior criaria a assinatura. Para simplificar este artigo, você cria a assinatura. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Se você precisar de um endpoint de teste para assinar seus eventos, poderá sempre implantar um aplicativo da Web [pré-criado](https://github.com/Azure-Samples/azure-event-grid-viewer) que exiba os eventos de entrada. Você pode enviar seus eventos para o website de teste em `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>

As permissões definidas para um tópico são armazenadas no Azure Active Directory e devem ser excluídas explicitamente. A exclusão de uma assinatura de evento não revogará o acesso de um usuário para criar assinaturas de evento se eles tiverem acesso de gravação em um tópico.


## <a name="publish-events-to-an-event-grid-domain"></a>Publicar eventos em um domínio da grade de eventos

Publicar eventos em um domínio é o mesmo que [publicar em um tópico personalizado](./post-to-custom-topic.md). No entanto, em vez de publicar para o tópico personalizado, publique todos os eventos para o ponto de extremidade de domínio. Nos dados de evento do JSON, especifique o tópico que deseja que os eventos sejam destinados. A seguinte matriz de eventos resultaria em evento com `"id": "1111"`para o tópico`demotopic1`, enquanto o evento com `"id": "2222"`seria enviado para o tópico`demotopic2`:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)
Para obter o ponto de extremidade de domínio com a CLI do Azure, use

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Para obter as chaves para um domínio, use:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Para obter o ponto de extremidade de domínio com o PowerShell, use

```azurepowershell-interactive
Get-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Para obter as chaves para um domínio, use:

```azurepowershell-interactive
Get-AzEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

E, em seguida, use seu método favorito de fazer um HTTP POST para publicar os eventos no domínio da Grade de Eventos.

## <a name="search-lists-of-topics-or-subscriptions"></a>Pesquisar listas de tópicos ou assinaturas

Para pesquisar e gerenciar um grande número de tópicos ou assinaturas, as APIs da grade de eventos dão suporte à listagem e à paginação.

### <a name="using-cli"></a>Usando a CLI
Por exemplo, o comando a seguir lista todos os tópicos com nome que contém `mytopic` . 

```azurecli-interactive
az eventgrid topic list --odata-query "contains(name, 'mytopic')"
```

Para obter mais informações sobre esse comando, consulte [`az eventgrid topic list`](/cli/azure/eventgrid/topic?#az_eventgrid_topic_list) . 


## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre conceitos de alto nível em domínios de eventos e por que eles são úteis, consulte [visão geral conceitual dos Domínios de Eventos](event-domains.md).
