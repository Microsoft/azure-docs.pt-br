---
title: "Segurança e autenticação da Grade de Eventos do Azure"
description: Descreve a Grade de Eventos do Azure e seus conceitos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: e2f48b6e72072ce6bf019b3adc138ae83c162f25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-security-and-authentication"></a>Segurança e autenticação da Grade de Eventos 

A Grade de Eventos do Azure tem três tipos de autenticação:

* Assinaturas de evento
* Publicação de evento
* Entrega de eventos do WebHook

## <a name="webhook-event-delivery"></a>Entrega de eventos do WebHook

Webhooks são uma dentre várias maneiras de receber eventos em tempo real da Grade de Eventos do Azure. Sempre que houver um novo evento pronto para ser entregue, o Webhook da Grade de Eventos enviará uma solicitação HTTP para o ponto de extremidade HTTP configurado com o evento no corpo.

Quando você registra seu próprio ponto de extremidade de WebHook com a Grade de Eventos, ele envia uma solicitação POST com um código de validação simples para comprovar a propriedade do ponto de extremidade. Seu aplicativo precisa responder retornando o código de validação como eco. A Grade de Eventos não fornece eventos para pontos de extremidade do WebHook que não foram aprovados na validação.

### <a name="validation-details"></a>Detalhes da validação

* No momento da criação/atualização da assinatura, a Grade de Eventos posta um evento "SubscriptionValidationEvent" para o ponto de extremidade de destino.
* O evento contém um valor de cabeçalho "Aeg-Event-Type: SubscriptionValidation".
* O corpo do evento tem o mesmo esquema que outros eventos da Grade de Eventos.
* Os dados do evento incluem uma propriedade "validationCode" com uma cadeia de caracteres gerada aleatoriamente. Por exemplo, "validationCode: acb13…".

Um SubscriptionValidationEvent de exemplo é mostrado no exemplo a seguir:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}]
```

Para provar a propriedade do ponto de extremidade, retorne o código de validação na propriedade validationResponse como eco, como mostrado no exemplo a seguir:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Por fim, é importante observar que a Grade de Eventos do Azure oferece suporte apenas a pontos de extremidade HTTPS do webhook.

## <a name="event-subscription"></a>Assinatura do evento

Para assinar um evento, você deve ter a permissão **Microsoft.EventGrid/EventSubscriptions/Write** no recurso exigido. Essa permissão é necessária porque você está escrevendo uma nova assinatura no escopo do recurso. O recurso necessário varia de acordo com se você estiver assinando um tópico de sistema ou um tópico personalizado. Ambos os tipos são descritos nesta seção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (publicadores de serviço do Azure)

Para tópicos do sistema, você precisa de permissão para gravar uma nova assinatura de evento no escopo do recurso que está publicando o evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para assinar um evento em uma conta de armazenamento chamada **myacct**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Para tópicos personalizados, você precisa de permissão para gravar uma nova assinatura de evento no escopo do tópico da Grade de Evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para assinar um tópico personalizado chamado **mytopic**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Publicação de tópico

Os tópicos usam a Assinatura de Acesso Compartilhado (SAS) ou a autenticação de chave. Recomendamos a SAS, mas a autenticação de chave fornece programação simples e é compatível com vários publicadores de webhook existentes. 

Você pode incluir o valor da autenticação no cabeçalho HTTP. Para SAS, use **aeg-sas-token** para o valor do cabeçalho. Para autenticação de chave, use **aeg-sas-token** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

A autenticação de chave é a forma mais simples de autenticação. Use o formato: `aeg-sas-key: <your key>`

Por exemplo, você pode passar uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Os tokens SAS para a Grade de Eventos incluem o recurso, um tempo de expiração e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico ao qual você está enviando eventos. Por exemplo, um caminho de recurso válido é:`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Você gera a assinatura de uma chave.

Por exemplo, um valor válido de **aeg-sas-token** é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

O exemplo a seguir cria um token SAS para uso com a Grade de Eventos:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Controle de acesso de gerenciamento

A Grade de Eventos do Azure permite que você controle o nível de acesso concedido a usuários diferentes para execução de várias operações de gerenciamento, como listar assinaturas de evento, criar novos e gerar chaves. A Grade de Eventos utiliza a verificação RBAC (Verificação de acesso com base em função) do Azure.

### <a name="operation-types"></a>Tipos de operação

A grade de eventos do Azure oferece suporte às seguintes ações:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

As últimas três operações retornam informações possivelmente secretas, as quais são filtradas dentre operações de leitura normais. É uma prática recomendada restringir o acesso a essas operações. É possível criar funções personalizadas usando o [Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md), a [interface de linha de comando (CLI) do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md) e a [API REST](../active-directory/role-based-access-control-manage-access-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Aplicação da Verificação RBAC (Verificação de acesso com base em função)

Use as etapas a seguir para impor o RBAC para usuários diferentes:

#### <a name="create-a-custom-role-definition-file-json"></a>Criar um arquivo de definição de função personalizado (.json)

Veja a seguir as definições de função da Grade de Eventos de exemplo que permitem aos usuários executar diferentes conjuntos de ações.

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

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Criar e atribuir uma função personalizada com a CLI do Azure

Para criar uma função personalizada, use:

```azurecli
az role definition create --role-definition @<file path>
```

Para atribuir a função a um usuário, use:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [Sobre a Grade de Eventos](overview.md)
