---
title: Usar a Grade de Eventos do Azure com eventos no esquema CloudEvents
description: Descreve como usar o esquema de CloudEvents para eventos na Grade de Eventos do Azure. O serviço oferece suporte a eventos na implementação JSON de CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 484f118791d57c082a9f4383b1af4a22c04849c4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737895"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Usar o esquema CloudEvents v1.0 com a Grade de Eventos
Além do seu [esquema de evento padrão](event-schema.md), a Grade de Eventos do Azure oferece suporte nativo a eventos na [implementação JSON do CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [ligação ao protocolo HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade fornecendo um esquema de evento comum para publicar e consumir eventos baseados em nuvem. Esse esquema permite ferramentas uniformes, maneiras padrão de roteamento e tratamento de eventos e maneiras universais de desserializar o esquema de evento externo. Com um esquema comum, você pode integrar facilmente mais trabalho entre plataformas.

O CloudEvents está sendo criado por vários [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, por meio da [Cloud Native Computing Foundation](https://www.cncf.io/). Ele está disponível como versão 1.0.

Este artigo descreve como usar o esquema de CloudEvents com a Grade de Eventos.

## <a name="cloudevent-schema"></a>Esquema de CloudEvent

Aqui está um exemplo de um evento de armazenamento de BLOBs do Azure no formato CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Para obter uma descrição detalhada dos campos disponíveis, seus tipos e definições, consulte [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores dos cabeçalhos para eventos entregues no esquema CloudEvents e no esquema da Grade de Eventos são os mesmos, exceto para `content-type`. Para o esquema CloudEvents, esse valor de cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"` . Para o esquema da grade de eventos, esse valor de cabeçalho é `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Configurar a Grade de Eventos para CloudEvents

Você pode usar a grade de eventos para entrada e saída de eventos no esquema CloudEvents. A tabela a seguir descreve as possíveis transformações:

 Recurso de grade de eventos | Esquema de entrada       | Esquema de entrega
|---------------------|-------------------|---------------------
| Tópicos do sistema       | Esquema da Grade de Eventos | Esquema de grade de eventos ou esquema CloudEvents
| Tópicos/domínios do usuário | Esquema da Grade de Eventos | Esquema de grade de eventos ou esquema CloudEvents
| Tópicos/domínios do usuário | Esquema CloudEvents | Esquema CloudEvents
| Tópicos/domínios do usuário | Esquema personalizado     | Esquema personalizado, esquema de grade de eventos ou esquema CloudEvents
| PartnerTopics       | Esquema CloudEvents | Esquema CloudEvents

Para todos os esquemas de evento, a grade de eventos requer validação quando você está publicando em um tópico de grade de eventos e quando você está criando uma assinatura de evento.

Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

### <a name="input-schema"></a>Esquema de entrada

Quando você cria o tópico personalizado, você definir o esquema de entrada para um tópico personalizado.

Para o CLI do Azure, use:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Para o PowerShell, use:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Esquema de saída

Quando você cria a assinatura de evento, você definir o esquema de saída.

Para o CLI do Azure, use:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Para o PowerShell, use:
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 No momento, você não pode usar um gatilho de Grade de Eventos para um aplicativo do Azure Functions, quando o evento é entregue no esquema do CloudEvents. Use um gatilho HTTP. Para obter exemplos de implementação de um gatilho HTTP que recebe eventos no esquema CloudEvents, consulte [Usando CloudEvents com Azure Functions](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto de extremidade com CloudEvents v 1.0

Se você já estiver familiarizado com a grade de eventos, talvez esteja ciente do handshake de validação do ponto de extremidade para evitar abusos. O CloudEvents v 1.0 implementa sua própria [semântica de proteção de abuso](webhook-event-delivery.md) usando o método de opções http. Para ler mais sobre isso, confira [ganchos da Web HTTP 1,1 para entrega de eventos-versão 1,0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Quando você usa o esquema CloudEvents para saída, a grade de eventos usa a proteção de abuso do CloudEvents v 1.0 em vez do mecanismo de evento de validação da grade de eventos.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Usar com o Azure Functions

A [associação da grade de eventos Azure Functions](../azure-functions/functions-bindings-event-grid.md) não oferece suporte nativo a CloudEvents, portanto as funções disparadas por http são usadas para ler mensagens CloudEvents. Ao usar um gatilho HTTP para ler CloudEvents, você precisa escrever código para o que o gatilho de grade de eventos faz automaticamente:

* Envia uma resposta de validação para uma [solicitação de validação de assinatura](../event-grid/webhook-event-delivery.md)
* Invoca a função uma vez por elemento da matriz de eventos contida no corpo da solicitação

Para obter informações sobre a URL a ser usada para invocar a função localmente ou quando ela é executada no Azure, consulte a [documentação de referência de associação de gatilho http](../azure-functions/functions-bindings-http-webhook.md).

O código C# de exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos. Use este exemplo para eventos entregues no esquema CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

O código JavaScript de exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos. Use este exemplo para eventos entregues no esquema CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Incentivamos você a testar, comentar e [contribuir com o CloudEvents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
