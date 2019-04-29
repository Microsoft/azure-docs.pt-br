---
title: Segurança e autenticação da Grade de Eventos do Azure
description: Descreve a Grade de Eventos do Azure e seus conceitos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: babanisa
ms.openlocfilehash: 2d56a7cda88f96a6728dc1c3e4af8e9ad0bf946f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822859"
---
# <a name="event-grid-security-and-authentication"></a>Segurança e autenticação da Grade de Eventos 

A Grade de Eventos do Azure tem três tipos de autenticação:

* Entrega de eventos do WebHook
* Assinaturas de evento
* Publicação de tópico personalizado

## <a name="webhook-event-delivery"></a>Entrega de eventos do WebHook

Webhooks são uma dentre várias maneiras de receber eventos da Grade de Eventos do Azure. Quando um novo evento estiver pronto, o serviço da Grade de Eventos do Azure POSTs uma solicitação HTTP para o ponto de extremidade configurado com o evento o corpo da solicitação.

Como muitos outros serviços que dão suporte a webhooks, a Grade de Eventos do Azure exige que você comprovar a "propriedade" de seu ponto de extremidade do Webhook antes de começar a entrega de eventos para esse ponto de extremidade. Esse requisito impede que um usuário mal-intencionado inunde seu ponto de extremidade com eventos. Quando você usa qualquer um dos três serviços do Azure listado abaixo, a infraestrutura do Azure trata automaticamente essa validação:

* Aplicativos Lógicos do Azure com [Conector da Grade de Eventos](https://docs.microsoft.com/connectors/azureeventgrid/)
* Automação do Azure por meio de [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions com [Gatilho de Grade de Eventos](../azure-functions/functions-bindings-event-grid.md)

Se você estiver usando qualquer outro tipo de ponto de extremidade, como uma função do Azure baseada no gatilho HTTP, o código do ponto de extremidade precisará participar de um handshake de validação com o EventGrid. A Grade de Eventos dá suporte a duas maneiras de validar a assinatura.

1. **Handshake de ValidationCode (programático)**: Se você controlar o código-fonte para o ponto de extremidade, esse método é recomendado. No momento da criação da assinatura, a Grade de Eventos posta um Evento de Validação de Assinatura para o ponto de extremidade de destino. O esquema desse evento é semelhante a qualquer outro evento da Grade de Eventos. A parte de dados desse evento inclui um `validationCode` propriedade. Seu aplicativo verifica se a solicitação de validação é para uma assinatura de evento esperado e retorna o código de validação à Grade de Eventos. Esse mecanismo de handshake é compatível com todas as versões da Grade de Eventos.

2. **Handshake ValidationURL (manual)**: Em alguns casos, você não pode acessar o código-fonte do ponto de extremidade para implementar o handshake ValidationCode. Por exemplo, se você usar um serviço de terceiros (como [Zapier](https://zapier.com) ou [IFTTT](https://ifttt.com/)), não será possível responder de volta programaticamente com o código de validação.

   A partir da versão 2018-05-01-preview, a Grade de Eventos dá suporte a um handshake de validação manual. Se você estiver criando uma inscrição de evento com um SDK ou ferramenta que usa a versão da API 2018-05-01-preview ou posterior, a Grade de Eventos envia uma propriedade `validationUrl` na parte de dados do evento de validação da assinatura. Para concluir o handshake, encontre essa URL nos dados do evento e manualmente enviar uma solicitação GET para ela. Você pode usar um cliente REST ou o navegador da web.

   A URL fornecida é válida por 5 minutos. Durante esse tempo, o estado de fornecimento da assinatura do evento é `AwaitingManualAction`. Se você não concluir a validação manual dentro de 5 minutos, o estado de provisionamento é definido como `Failed`. Você terá que criar a inscrição do evento novamente antes de iniciar a validação manual.

    Esse mecanismo de autenticação também requer o ponto de extremidade de webhook para retornar um código de status HTTP de 200 para que ele saiba que o POST para o evento de validação foi aceito antes que ele possa ser colocado no modo de validação manual. Em outras palavras, se o ponto de extremidade retorna 200, mas não retorna uma resposta de validação por meio de programação, o modo é transferido para o modo de validação manual. Se houver um GET na URL de validação dentro de 5 minutos, o handshake de validação é considerado bem-sucedida.

### <a name="validation-details"></a>Detalhes da validação

* No momento da criação/atualização da assinatura, a Grade de Eventos posta um Evento de Validação de Assinatura para o ponto de extremidade de destino. 
* O evento contém um valor de cabeçalho "aeg-event-type: SubscriptionValidation".
* O corpo do evento tem o mesmo esquema que outros eventos da Grade de Eventos.
* A propriedade eventType do evento é `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Os dados de propriedade do evento incluem uma propriedade `validationCode` com uma cadeia de caracteres gerada aleatoriamente. Por exemplo, "validationCode: acb13…".
* Os dados do evento também incluem uma propriedade `validationUrl` com uma URL para validar manualmente a assinatura.
* A matriz contém apenas o evento de validação. Outros eventos serão enviados em uma solicitação separada, após retornar o código de validação.
* Os SDKs do plano de dados EventGrid têm classes correspondentes para os dados de evento de validação de assinatura e a resposta de validação de assinatura.

Um SubscriptionValidationEvent de exemplo é mostrado no exemplo a seguir:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Para provar a propriedade do pronto de extremidade, retorne o código de validação na propriedade validationResponse, conforme mostrado no exemplo a seguir:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Você deve retornar um código de status de resposta HTTP 200 Okey. HTTP 202 aceito não é reconhecido como uma resposta de validação de assinatura de Grade de Eventos válida.

Ou então, você pode validar a assinatura manualmente, enviando uma solicitação GET para a URL de validação. A inscrição do evento permanece em um estado pendente até que validada.

Para obter um exemplo de lidar com o handshake de validação de assinatura, consulte uma [ amostra C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Lista de verificação

Durante a criação de assinatura de evento, se você estiver vendo uma mensagem de erro como "ao tentar validar o ponto de extremidade fornecido https:\//your-endpoint-here falhou. Para obter mais detalhes, visite https:\//aka.ms/esvalidation ", ele indica que há uma falha no handshake de validação. Para resolver esse erro, verifique os seguintes aspectos:

* Você tem o controle do código do aplicativo no ponto de extremidade de destino? Por exemplo, se você está escrevendo um Azure Function com base em gatilho HTTP, você tem acesso ao código do aplicativo para fazer alterações nele?
* Se você tiver acesso ao código do aplicativo, implemente o mecanismo de handshake com base em ValidationCode como mostrado no exemplo acima.

* Se você não tiver acesso ao código do aplicativo (por exemplo, se você estiver usando um serviço de terceiros compatível com webhooks), poderá usar o mecanismo de handshake manual. Certifique-se de usar a versão de API 2018-05-01-preview ou posterior (instale a extensão da CLI do Azure da Grade de Eventos) para receber o validationUrl no evento de validação. Para concluir o handshake de validação manual, obtenha o valor da propriedade `validationUrl` e visite a URL no navegador da Web. Se a validação for bem-sucedida, você verá uma mensagem em seu navegador da Web indicando que a validação foi bem-sucedida. Você verá que o provisioningState da assinatura do evento mostrará "Êxito". 

### <a name="event-delivery-security"></a>Segurança de entrega de evento

É possível proteger o ponto de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma Assinatura de Evento. Defina um desses parâmetros de consulta para ser um segredo, como um [token de acesso](https://en.wikipedia.org/wiki/Access_token). O webhook pode usar o segredo para reconhecer que o evento é proveniente da Grade de Eventos com permissões válidas. A Grade de Eventos inclui esses parâmetros de consulta em cada entrega de evento para o webhook.

Ao editar a Assinatura de Evento, os parâmetros de consulta não serão exibidos nem retornados, a menos que o parâmetro [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) seja usado na [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) do Azure.

Por fim, é importante observar que a Grade de Eventos do Azure oferece suporte apenas a ponto de extremidade do webhook HTTPS.

## <a name="event-subscription"></a>Assinatura do evento

Para assinar um evento, você deve provar que tem acesso à origem do evento e ao manipulador. A prova de que você tem um WebHook foi abordada na seção anterior. Se você estiver usando um manipulador de eventos que não seja um WebHook (como um armazenamento de fila ou hub de eventos), será necessário acesso de gravação a esse recurso. Essa verificação de permissões impede que um usuário não autorizado envie eventos para seu recurso.

Você deve ter a permissão **Microsoft.EventGrid/EventSubscriptions/Write** no recurso que é a origem do evento. Essa permissão é necessária porque está gravando uma nova assinatura no escopo do recurso. O recurso necessário varia de acordo com se você estiver assinando um tópico de sistema ou um tópico personalizado. Ambos os tipos são descritos nesta seção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (publicadores de serviço do Azure)

Para tópicos do sistema, você precisa de permissão para gravar uma nova assinatura de evento no escopo do recurso que está publicando o evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para assinar um evento em uma conta de armazenamento chamada **myacct**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Para tópicos personalizados, você precisa de permissão para gravar uma nova assinatura de evento no escopo do tópico da grade de eventos. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para assinar um tópico personalizado chamado **mytopic**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publicação de tópico personalizado

Os tópicos personalizados usam a SAS (Assinatura de Acesso Compartilhado) ou a autenticação de chave. Recomendamos a SAS, mas a autenticação de chave fornece programação simples e é compatível com vários publicadores de webhook existentes. 

Você pode incluir o valor da autenticação no cabeçalho HTTP. Para SAS, use **aeg-sas-token** para o valor do cabeçalho. Para autenticação de chave, use **aeg-sas-token** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

A autenticação de chave é a forma mais simples de autenticação. Use o formato: `aeg-sas-key: <your key>`

Por exemplo, você pode passar uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Os tokens SAS para a Grade de Eventos incluem o recurso, um tempo de expiração e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico da grade de eventos para o qual você está enviando eventos. Por exemplo, um caminho de recurso válido é:`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

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

A Grade de Eventos do Azure permite que você controle o nível de acesso concedido a usuários diferentes para execução de várias operações de gerenciamento, como listar assinaturas de evento, criar novos e gerar chaves. O Event Grid usa o controle de acesso baseado em função (RBAC) do Azure.

### <a name="operation-types"></a>Tipos de operação

Grade de eventos suporta as seguintes ações:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

As últimas três operações retornam informações possivelmente secretas, as quais são filtradas dentre operações de leitura normais. É recomendável que você restrinja o acesso a essas operações. 

### <a name="built-in-roles"></a>Funções internas

Grade de eventos fornece duas funções internas para gerenciar assinaturas de evento. Eles são importantes na implementação de [domínios de eventos](event-domains.md), pois fornecem aos usuários as permissões necessárias para se inscreverem em tópicos do domínio de eventos. Essas funções são focadas em assinaturas de eventos e não concedem acesso a ações como a criação de tópicos.

Você pode [atribua essas funções para um usuário ou grupo](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Grade de Eventos do Azure EventSubscription colaborador (visualização)**: gerenciar operações de assinatura de grade de eventos

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**Grade de Eventos do Azure EventSubscription Reader (versão prévia)**: ler assinaturas de grade de eventos

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Funções personalizadas

Se você precisar especificar permissões que são diferentes de funções internas, você pode criar funções personalizadas.

A seguir estão definições de função da Grade de Eventos de exemplo que permitem aos usuários executar diferentes ações. Essas funções personalizadas são diferentes das funções internas porque elas concedem acesso mais amplo do que apenas assinaturas de eventos.

**EventGridReadOnlyRole.json**: Permitir apenas operações somente leitura.

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

**EventGridNoDeleteListKeysRole.json**: Permitir ações restritas posteriores, mas impedir ações de exclusão.

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

**EventGridContributorRole.json**: Permite todas as ações de grade de eventos.

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

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [Sobre a Grade de Eventos](overview.md)
