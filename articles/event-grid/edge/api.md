---
title: API REST – IoT Edge da grade de eventos do Azure | Microsoft Docs
description: API REST na grade de eventos na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171747"
---
# <a name="rest-api"></a>API REST
Este artigo descreve as APIs REST da grade de eventos do Azure no IoT Edge

## <a name="common-api-behavior"></a>Comportamento comum da API

### <a name="base-url"></a>URL base
A grade de eventos em IoT Edge tem as seguintes APIs expostas sobre HTTP (porta 5888) e HTTPS (porta 4438).

* URL base para HTTP: http://eventgridmodule:5888
* URL base para HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Cadeia de consulta de solicitação
Todas as solicitações de API exigem o seguinte parâmetro de cadeia de caracteres de consulta:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Tipo de conteúdo da solicitação
Todas as solicitações de API devem ter um **tipo de conteúdo**.

No caso de **EventGridSchema** ou **CustomSchema**, o valor de Content-Type pode ser um dos seguintes valores:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

No caso de **CloudEventSchemaV1_0** no modo estruturado, o valor de Content-Type pode ser um dos seguintes valores:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

No caso de **CloudEventSchemaV1_0** no modo binário, consulte a [documentação](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) para obter detalhes.

### <a name="error-response"></a>Resposta de erro
Todas as APIs retornam um erro com a seguinte carga:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Gerenciar tópicos

### <a name="put-topic-create--update"></a>Put tópico (criar/atualizar)

**Solicitação**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Carga**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Resposta**: http 200

**Carga**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Obter tópico

**Solicitação**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Resposta**: http 200

**Carga**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Obter todos os tópicos

**Solicitação**: ``` GET /topics?api-version=2019-01-01-preview ```

**Resposta**: http 200

**Carga**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Excluir tópico

**Solicitação**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Resposta**: http 200, carga vazia

## <a name="manage-event-subscriptions"></a>Gerenciar assinaturas de evento
Os exemplos nesta seção usam o `EndpointType=Webhook;` . Os exemplos de JSON para `EndpointType=EdgeHub / EndpointType=EventGrid` estão na próxima seção. 

### <a name="put-event-subscription-create--update"></a>Inserir assinatura de evento (criar/atualizar)

**Solicitação**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Carga**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Resposta**: http 200

**Carga**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Obter assinatura de evento

**Solicitação**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Resposta**: http 200

**Carga**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Obter assinaturas de evento

**Solicitação**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Resposta**: http 200

**Carga**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Excluir assinatura de evento

**Solicitação**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Resposta**: http 200, sem carga


## <a name="publish-events-api"></a>API de eventos de publicação

### <a name="send-batch-of-events-in-event-grid-schema"></a>Enviar lote de eventos (no esquema de grade de eventos)

**Solicitação**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Resposta**: http 200, carga vazia


**Descrições de campos de carga**
- ```Id``` é obrigatório. Pode ser qualquer valor de cadeia de caracteres que é preenchido pelo chamador. A grade de eventos não realiza nenhuma detecção de duplicidades nem impõe semânticas nesse campo.
- ```Topic``` é opcional, mas se especificado deve corresponder ao topic_name da URL de solicitação
- ```Subject``` é obrigatório, pode ser qualquer valor de cadeia de caracteres
- ```EventType``` é obrigatório, pode ser qualquer valor de cadeia de caracteres
- ```EventTime``` é obrigatório, não é validado, mas deve ser um DateTime adequado.
- ```DataVersion``` é obrigatório
- ```MetadataVersion``` é opcional, se especificado, deve ser uma cadeia de caracteres com o valor ```"1"```
- ```Data``` é opcional e pode ser qualquer token JSON (número, Cadeia de caracteres, booliano, matriz, objeto)

### <a name="send-batch-of-events-in-custom-schema"></a>Enviar lote de eventos (no esquema personalizado)

**Solicitação**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Resposta**: http 200, carga vazia


**Restrições de carga**
- DEVE ser uma matriz de eventos.
- Cada entrada de matriz deve ser um objeto JSON.
- Nenhuma outra restrição (que não seja o tamanho da carga).

## <a name="examples"></a>Exemplos

### <a name="set-up-topic-with-eventgrid-schema"></a>Configurar o tópico com o esquema EventGrid
Configura um tópico para exigir que os eventos sejam publicados em **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Configurar o tópico com esquema personalizado
Configura um tópico para exigir que os eventos sejam publicados no `customschema` .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Configurar o tópico com o esquema de evento de nuvem
Configura um tópico para exigir que os eventos sejam publicados no `cloudeventschema` .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Configurar webhook como destino, eventos a serem entregues em eventgridschema
Use esse tipo de destino para enviar eventos para qualquer outro módulo (que hospede um ponto de extremidade HTTP) ou para qualquer ponto de extremidade endereçável HTTP na rede/Internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Restrições no `endpointUrl` atributo:
- Ele deve ser não nulo.
- Ele deve ser uma URL absoluta.
- Se outbound__webhook__httpsOnly for definido como true nas configurações de EventGridModule, ele deverá ser somente HTTPS.
- Se outbound__webhook__httpsOnly definido como false, ele poderá ser HTTP ou HTTPS.

Restrições na `eventDeliverySchema` Propriedade:
- Ele deve corresponder ao esquema de entrada do tópico de assinatura.
- Pode ser nula. O padrão é o esquema de entrada do tópico.

### <a name="set-up-iot-edge-as-destination"></a>Configurar IoT Edge como destino

Use esse destino para enviar eventos para IoT Edge Hub e estar sujeito ao subsistema de roteamento/filtragem/encaminhamento do hub de borda.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Configurar a nuvem da grade de eventos como destino

Use esse destino para enviar eventos para a grade de eventos na nuvem (Azure). Você precisará primeiro configurar um tópico de usuário na nuvem para o qual os eventos devem ser enviados, antes de criar uma assinatura de evento na borda.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Ele deve ser não nulo.
- Ele deve ser uma URL absoluta.
- O caminho `/api/events` deve ser definido no caminho da URL de solicitação.
- Ele deve ter `api-version=2018-01-01` na cadeia de caracteres de consulta.
- Se outbound__eventgrid__httpsOnly for definido como true nas configurações de EventGridModule (true por padrão), ele deverá ser somente HTTPS.
- Se outbound__eventgrid__httpsOnly for definido como false, ele poderá ser HTTP ou HTTPS.
- Se outbound__eventgrid__allowInvalidHostnames for definido como false (false por padrão), ele deverá ter como destino um dos seguintes pontos de extremidade:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Deve ser não nulo.

Topicname:
- Se a assinatura. EventDeliverySchema for definida como EventGridSchema, o valor desse campo será colocado no campo de tópico de cada evento antes de ser encaminhado para a grade de eventos na nuvem.
- Se a assinatura. EventDeliverySchema for definida como CustomEventSchema, essa propriedade será ignorada e a carga do evento personalizado será encaminhada exatamente como foi recebida.

## <a name="set-up-event-hubs-as-a-destination"></a>Configurar os hubs de eventos como um destino

Para publicar em um hub de eventos, defina `endpointType` como `eventHub` e forneça:

* connectionString: cadeia de conexão para o Hub de eventos específico que você pretende gerar por meio de uma política de acesso compartilhado.

    >[!NOTE]
    > A cadeia de conexão deve ser específica à entidade. Usar uma cadeia de conexão de namespace não funcionará. Você pode gerar uma cadeia de conexão específica da entidade navegando até o Hub de eventos específico no qual deseja publicar no portal do Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de caracteres connecection específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Configurar filas do barramento de serviço como um destino

Para publicar em uma fila do barramento de serviço, defina `endpointType` como `serviceBusQueue` e forneça:

* connectionString: cadeia de conexão para a fila específica do barramento de serviço que você está direcionando gerada por meio de uma política de acesso compartilhado.

    >[!NOTE]
    > A cadeia de conexão deve ser específica à entidade. Usar uma cadeia de conexão de namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando até a fila específica do barramento de serviço na qual você gostaria de publicar no portal do Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de caracteres connecection específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Configurar tópicos do barramento de serviço como um destino

Para publicar em um tópico do barramento de serviço, defina `endpointType` como `serviceBusTopic` e forneça:

* connectionString: cadeia de conexão para o tópico específico do barramento de serviço que você está direcionando gerado por uma política de acesso compartilhado.

    >[!NOTE]
    > A cadeia de conexão deve ser específica à entidade. Usar uma cadeia de conexão de namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando até o tópico específico do barramento de serviço que você deseja publicar no portal do Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de caracteres connecection específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Configurar filas de armazenamento como um destino

Para publicar em uma fila de armazenamento, defina  `endpointType` como `storageQueue` e forneça:

* QueueName: o nome da fila de armazenamento na qual você está publicando.
* connectionString: cadeia de conexão para a conta de armazenamento na qual a fila de armazenamento está.

    >[!NOTE]
    > Hubs de eventos unline, filas do barramento de serviço e tópicos do barramento de serviço, a cadeia de conexão usada para filas de armazenamento não é específica à entidade. Em vez disso, ele deve, mas a cadeia de conexão para a conta de armazenamento.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```