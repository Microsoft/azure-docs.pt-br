---
title: Mapear o campo personalizado para o esquema de Grade de Eventos do Azure
description: Descreve como converter seu esquema personalizado para o esquema de Grade de Eventos do Azure.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: a0e054be3ab7d4818ac323eb5fb93968f57eca4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565490"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapear campos personalizados para o esquema de Grade de Eventos

Se os dados do evento não corresponderem ao esperado [esquema de Grade de Eventos](event-schema.md), você ainda poderá usar a Grade de Eventos para rotear o evento para os assinantes. Este artigo descreve como mapear seu esquema para o esquema de Grade de Eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar versão prévia do recurso

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Esquema de evento original

Vamos supor que você tenha um aplicativo que envia eventos no seguinte formato:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Embora esse formato não corresponda ao esquema requerido, a Grade de Eventos permite que você mapeie os campos para o esquema. Ou, você pode receber os valores no esquema original.

## <a name="create-custom-topic-with-mapped-fields"></a>Criar tópico personalizado com campos mapeados

Ao criar um tópico personalizado, especifique como mapear campos de seu evento original para o esquema de grade de eventos. Existem três valores que você usa para personalizar o mapeamento:

* O valor do **esquema de entrada** especifica o tipo de esquema. As opções disponíveis são o esquema CloudEvents, o esquema de eventos personalizados ou o esquema de Grade de Eventos do Azure. O valor padrão é o esquema de Grade de Eventos do Azure. Ao criar um mapeamento personalizado entre seu esquema e o esquema de grade do evento, use o esquema de evento personalizado. Quando os eventos estão no esquema CloudEvents, use o esquema Cloudevents.

* O **mapeamento de valores padrão** propriedade especifica valores padrão para campos no esquema de Grade de Eventos do Azure. É possível definir valores padrão para `subject`, `eventtype` e `dataversion`. Normalmente, você usará esse parâmetro quando o esquema personalizado não incluir um campo que corresponder a um desses três campos. Por exemplo, você pode especificar que a versão dos dados é sempre definida como **1.0**.

* O valor dos **campos de mapeamento** mapeia os campos do seu esquema para o esquema de grade do evento. Você especifica valores em pares chave/valor separados por espaços. Para o nome da chave, use o nome do campo de grade de eventos. O valor, use o nome do seu campo. Você pode usar nomes de chaves para `id`, `topic`, `eventtime`, `subject`, `eventtype` e `dataversion`.

Para criar um tópico personalizado com CLI do Azure, use:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Para o PowerShell, use:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Inscrever-se para o tópico de grade de eventos

Ao inscrever-se para o tópico personalizado, você pode especificar o esquema que você deseja usar para receber os eventos. Você especifica o esquema CloudEvents, o esquema de eventos personalizado ou o esquema de Grade de Eventos do Azure. O valor padrão é o esquema de Grade de Eventos do Azure.

O exemplo a seguir assina um tópico da grade de eventos e usa o esquema Grade de Eventos do Azure. Para a CLI do Azure, use:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

O exemplo a seguir usa o esquema de entrada do evento:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

O exemplo a seguir assina um tópico da grade de eventos e usa o esquema Grade de Eventos do Azure. Para o PowerShell, use:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

O exemplo a seguir usa o esquema de entrada do evento:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publicar evento para tópico

Agora você está pronto para enviar um evento ao tópico personalizado e ver o resultado do mapeamento. O script a seguir para publicar um evento de [esquema de exemplo](#original-event-schema):

Para a CLI do Azure, use:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Para o PowerShell, use:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Agora, observe o ponto de extremidade do WebHook. As duas assinaturas entregaram eventos em esquemas diferentes.

A primeira assinatura usou o esquema de Grade de Eventos. O formato do evento fornecido é:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Esses campos contêm os mapeamentos do tópico personalizado. **myEventTypeField** é mapeado para **EventType**. Os valores padrão para **DataVersion** e **Assunto** são usados. O objeto **dados** contém os campos de esquema do evento original.

A segunda assinatura usou o esquema de evento de entrada. O formato do evento fornecido é:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Observe que os campos originais foram entregues.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
