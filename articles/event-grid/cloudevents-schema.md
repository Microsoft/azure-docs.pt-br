---
title: Usar a Grade de Eventos do Azure com eventos no esquema CloudEvents
description: Descreve como configurar o esquema de CloudEvents para eventos na Grade de Eventos do Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 0195ce82396a7b05335242a38a2881e1b2d1afb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436589"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Usar o esquema CloudEvents com a Grade de Eventos

Além do seu [esquema de evento padrão](event-schema.md), a Grade de Eventos do Azure oferece suporte nativo a eventos no [esquema JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/master/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade, fornecendo um esquema comum do evento para publicar e consumir eventos com base em nuvem. Esse esquema permite ferramentas uniforme, formas padrão de roteamento e manipulação de eventos e maneiras universais de desserializar o esquema de evento externo. Com um esquema comum, você pode integrar facilmente mais trabalho entre plataformas.

O CloudEvents está sendo criado por vários [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, por meio da [Cloud Native Computing Foundation](https://www.cncf.io/). Ele está disponível como versão 0.1.

Este artigo descreve como usar o esquema de CloudEvents com a Grade de Eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar versão prévia do recurso

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Esquema de CloudEvent

Aqui está um exemplo de um evento de Armazenamento de Blob do Azure no formato CloudEvents:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 tem as seguintes propriedades disponíveis:

| CloudEvents        | Type     | Valor JSON de exemplo             | DESCRIÇÃO                                                        | Mapeamento de Grade de Eventos
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | Cadeia de caracteres   | "com.example.someevent"          | Tipo de ocorrência que ocorreram                                   | eventType
| eventTypeVersion   | Cadeia de caracteres   | "1.0"                            | A versão do eventType (Opcional)                            | dataVersion
| cloudEventsVersion | Cadeia de caracteres   | “0.1”                            | A versão da especificação CloudEvents que o evento usa        | *passed through*
| fonte             | URI      | “/mycontext”                     | Descreve o produtor de evento                                       | topic#subject
| eventID            | Cadeia de caracteres   | “1234-1234-1234”                 | ID do evento                                                    | ID
| eventTime          | Timestamp| "2018-04-05T17:31:00Z"           | Carimbo de hora de quando ocorreu o evento (opcional)                    | eventTime
| schemaURL          | URI      | "https:\//myschema.com"           | Um link para o esquema que cumpre o atributo de dados (opcional) | *não usado*
| contentType        | Cadeia de caracteres   | “application/json”               | Descrever o formato de codificação de dados (opcional)                       | *não usado*
| extensions         | Mapear      | { "extA": "vA", "extB", "vB" }  | Quaisquer metadados adicionais (opcional)                                 | *não usado*
| data               | Object   | { "objA": "vA", "objB", "vB" }  | A carga do evento (opcional)                                       | data

Para obter mais informações, confira a [especificação CloudEvents](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

Os valores dos cabeçalhos para eventos entregues no esquema CloudEvents e no esquema da Grade de Eventos são os mesmos, exceto para `content-type`. Para o esquema CloudEvents, esse valor de cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"`. Para o esquema Grade de Eventos, esse valor de cabeçalho é `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Configurar a Grade de Eventos para CloudEvents

Você pode usar a Grade de Eventos para entrada e saída de eventos no esquema CloudEvents. Você pode usar o CloudEvents para eventos do sistema, como eventos do Armazenamento de Blob e eventos do Hub IoT e eventos personalizados. Também pode transformar esses eventos durante a transmissão para trás e para frente.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato de CloudEvents | Formato de CloudEvents
| Formato da Grade de Eventos  | Formato de CloudEvents
| Formato de CloudEvents | Formato da Grade de Eventos
| Formato da Grade de Eventos  | Formato da Grade de Eventos

Para todos os esquemas de evento, a Grade de Eventos requer validação ao publicar em um tópico de grade de eventos e ao criar uma inscrição de evento. Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

### <a name="input-schema"></a>Esquema de entrada

Quando você cria o tópico personalizado, você definir o esquema de entrada para um tópico personalizado.

Para a CLI do Azure, use:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Para o PowerShell, use:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventV01Schema
```

A versão atual do CloudEvents não dá suporte a envio em lote de eventos. Para publicar eventos com o esquema do CloudEvent para um tópico, publique cada evento individualmente.

### <a name="output-schema"></a>Esquema de saída

Quando você cria a assinatura de evento, você definir o esquema de saída.

Para a CLI do Azure, use:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Para o PowerShell, use:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

A versão atual do CloudEvents não dá suporte a envio em lote de eventos. Uma assinatura de evento que está configurada para o esquema de CloudEvent recebe cada evento individualmente. No momento, você não pode usar um gatilho de Grade de Eventos para um aplicativo do Azure Functions, quando o evento é entregue no esquema do CloudEvents. Use um gatilho HTTP. Para obter exemplos de implementação de um gatilho HTTP que recebe eventos no esquema CloudEvents, consulte [Use um gatilho HTTP como um gatilho de Grade de Eventos](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Incentivamos você a testar, comentar e [contribuir](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) para CloudEvents.
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
