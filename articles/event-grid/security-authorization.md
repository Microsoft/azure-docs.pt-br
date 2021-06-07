---
title: Segurança e autenticação da Grade de Eventos do Azure
description: Descreve a Grade de Eventos do Azure e seus conceitos.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601033"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizando o acesso aos recursos da grade de eventos
A grade de eventos do Azure permite que você controle o nível de acesso fornecido a diferentes usuários para realizar várias **operações de gerenciamento** , como listar assinaturas de evento, criar novos e gerar chaves. A grade de eventos usa o controle de acesso baseado em função do Azure (RBAC do Azure).

> [!NOTE]
> EventGrid não dá suporte ao RBAC do Azure para publicar eventos em domínios ou tópicos da grade de eventos. Use uma chave de SAS (assinatura de acesso compartilhado) ou um token para autenticar clientes que publicam eventos. Para obter mais informações, consulte [autenticar clientes de publicação](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Tipos de operação
Para obter uma lista de operações com suporte pela grade de eventos do Azure, execute o seguinte comando de CLI do Azure: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

As operações a seguir retornam informações potencialmente secretas, que são filtradas de operações de leitura normais. É recomendável que você restrinja o acesso a essas operações. 

* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action


## <a name="built-in-roles"></a>Funções internas
A grade de eventos fornece as três funções internas a seguir. 

As funções de colaborador do leitor de assinatura da grade de eventos e da grade de eventos são para o gerenciamento de assinaturas de evento. Eles são importantes ao implementar [domínios de evento](event-domains.md) porque fornecem aos usuários as permissões de que eles precisam para assinar tópicos em seu domínio de evento. Essas funções são focadas em assinaturas de eventos e não concedem acesso a ações como a criação de tópicos.

A função colaborador da grade de eventos permite que você crie e gerencie recursos da grade de eventos. 


| Função | Descrição |
| ---- | ----------- | 
| [Leitor de assinatura de grade de eventos](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Permite que você gerencie operações de assinatura de evento de grade de eventos. |
| [Colaborador de assinatura da grade de eventos](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Permite que você leia assinaturas de evento da grade de eventos. |
| [Colaborador da grade de eventos](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Permite criar e gerenciar recursos da grade de eventos. |


> [!NOTE]
> Selecione links na primeira coluna para navegar até um artigo que fornece mais detalhes sobre a função. Para obter instruções sobre como atribuir usuários ou grupos a funções de RBAC, consulte [Este artigo](../role-based-access-control/quickstart-assign-role-user-portal.md).


## <a name="custom-roles"></a>Funções personalizadas

Se você precisar especificar permissões que são diferentes de funções internas, você pode criar funções personalizadas.

A seguir estão definições de função da Grade de Eventos de exemplo que permitem aos usuários executar diferentes ações. Essas funções personalizadas são diferentes das funções internas porque elas concedem acesso mais amplo do que apenas assinaturas de eventos.

**EventGridReadOnlyRole.json**: permitir apenas operações somente leitura.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: permitir ações restritas posteriores, mas impedir ações de exclusão.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: permitir todas as ações da grade de eventos.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Você pode criar funções personalizadas com [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md) e [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Criptografia em repouso

Todos os eventos ou dados gravados no disco pelo serviço Grade de Eventos são criptografados por uma chave gerenciada pela Microsoft, garantindo que estejam criptografados quando inativos. Além disso, o período máximo de retenção dos eventos ou dados é de 24 horas, em conformidade com a [Política de repetição da Grade de Eventos](delivery-and-retry.md). A Grade de Eventos excluirá automaticamente todos os eventos ou dados após 24 horas ou a vida útil do evento, o que for menor.

## <a name="permissions-for-event-subscriptions"></a>Permissões para assinaturas de evento
Se você estiver usando um manipulador de eventos que não seja um WebHook (como um armazenamento de fila ou hub de eventos), será necessário acesso de gravação a esse recurso. Essa verificação de permissões impede que um usuário não autorizado envie eventos para seu recurso.

Você deve ter a permissão **Microsoft.EventGrid/EventSubscriptions/Write** no recurso que é a origem do evento. Essa permissão é necessária porque está gravando uma nova assinatura no escopo do recurso. O recurso necessário varia de acordo com se você estiver assinando um tópico de sistema ou um tópico personalizado. Ambos os tipos são descritos nesta seção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (publicadores de serviço do Azure)
Para tópicos do sistema, se você não for o proprietário ou colaborador do recurso de origem, precisará de permissão para gravar uma nova assinatura de evento no escopo do recurso que está publicando o evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para assinar um evento em uma conta de armazenamento chamada **myacct**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados
Para tópicos personalizados, você precisa de permissão para gravar uma nova assinatura de evento no escopo do tópico da grade de eventos. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para assinar um tópico personalizado chamado **mytopic**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [Sobre a Grade de Eventos](overview.md)
