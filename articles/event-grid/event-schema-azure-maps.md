---
title: Mapas do Azure como origem da grade de eventos
description: Descreve as propriedades e o esquema fornecidos para eventos do Azure Mapas com a Grade de Eventos do Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 88cf0c8274d685a45862bc7b7884b5e4a686c22d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363671"
---
# <a name="azure-maps-as-an-event-grid-source"></a>O Azure mapeia como uma fonte de grade de eventos

Este artigo fornece as propriedades e o esquema dos eventos do Azure Mapas. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](./event-schema.md). Ele também fornece uma lista de inícios rápidos e tutoriais para usar o Azure Maps como uma fonte de eventos.

## <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta do Azure Mapas emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando as coordenadas recebidas são movidas de fora de uma determinada cerca geográfica para dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando as coordenadas recebidas são movidas de dentro de uma determinada cerca geográfica para fora |
| Microsoft.Maps.GeofenceResult | Gerado sempre que uma consulta de delimitação geográfica retorna um resultado, independentemente do estado |

## <a name="example-events"></a>Eventos de exemplo

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)
O exemplo a seguir mostra o esquema de um evento **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

O exemplo a seguir mostra o esquema para **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)
O exemplo a seguir mostra o esquema de um evento **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceEntered", 
   "time":"2018-11-08T00:54:17.6408601Z", 
   "specversion":"1.0" 
}
```

O exemplo a seguir mostra o esquema para **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceResult", 
   "time":"2018-11-08T00:52:08.0954283Z", 
   "specversion":"1.0" 
}
```
---

## <a name="event-properties"></a>Propriedades do evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)
Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `topic` | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `eventType` | string | Um dos tipos de evento registrados para a origem do evento. |
| `eventTime` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `id` | string | Identificador exclusivo do evento. |
| `data` | object | Dados de evento de delimitação geográfica. |
| `dataVersion` | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| `metadataVersion` | string | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)
Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `source` | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `type` | string | Um dos tipos de evento registrados para a origem do evento. |
| `time` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `id` | string | Identificador exclusivo do evento. |
| `data` | object | Dados de evento de delimitação geográfica. |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `apiCategory` | string | Categoria da API do evento. |
| `apiName` | string | Nome da API do evento. |
| `issues` | object | Lista problemas ocorridos durante o processamento. Se algum problema for retornado, não haverá geometrias retornadas com a resposta. |
| `responseCode` | número | Código de resposta HTTP |
| `geometries` | object | Lista as geometrias de delimitação que contêm a posição da coordenada ou sobrepõem o searchBuffer em torno da posição. |

O objeto de erro é retornado quando ocorre um erro na API de Mapas. O objeto de erro tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `error` | ErrorDetails |Esse objeto é retornado quando ocorre um erro na API de Mapas  |

O objeto ErrorDetails é retornado quando ocorre um erro na API de Mapas. O ErrorDetails ou objeto tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `code` | string | O código de status HTTP. |
| `message` | string | Se disponível, uma descrição do erro em formato legível por humanos. |
| `innererror` | InnerError | Se disponível, um objeto contendo informações específicas do serviço sobre o erro. |

O InnerError é um objeto que contém informações específicas do serviço sobre o erro. O objeto InnerError tem as seguintes propriedades: 

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `code` | string | A mensagem de erro. |

O objeto de geometrias lista as IDs de geometria das cercas geográficas que expiraram em relação ao tempo do usuário na solicitação. O objeto de geometrias contém itens de geometria com as seguintes propriedades: 

| Propriedade | Type | Descrição |
|:-------- |:---- |:----------- |
| `deviceid` | string | ID do dispositivo. |
| `distance` | string | <p>Distância da coordenada até a borda mais próxima da cerca geográfica. Positivo significa que a coordenada está fora da cerca geográfica. Se a coordenada estiver fora da cerca geográfica, mas for maior que o valor de searchBuffer distante da borda da cerca geográfica mais próxima, o valor será 999. Negativo significa que a coordenada está dentro da cerca geográfica. Se a coordenada estiver dentro do polígono, mas for maior que o valor de searchBuffer distante da borda de delimitação geográfica mais próxima, o valor será -999. Um valor de 999 significa que há grande confiança de que a coordenada esteja bem fora da cerca geográfica. Um valor de -999 significa que há uma grande confiança de que a coordenada esteja bem dentro da cerca geográfica.<p> |
| `geometryid` |string | A ID exclusiva identifica a geometria de cerca geográfica. |
| `nearestlat` | número | Latitude do ponto mais próximo da geometria. |
| `nearestlon` | número | Longitude do ponto mais próximo da geometria. |
| `udId` | string | A ID exclusiva retornada do serviço de carregamento do usuário ao carregar uma cerca geográfica. Não será incluído na API pós de isolamento geográfico. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `expiredGeofenceGeometryId` | string[] | Listas da ID de geometria da cerca geográfica que é expirada em relação ao tempo do usuário na solicitação. |
| `geometries` | geometries[] |Lista as geometrias de delimitação que contêm a posição da coordenada ou sobrepõem o searchBuffer em torno da posição. |
| `invalidPeriodGeofenceGeometryId` | string[]  | Listas da ID de geometria da cerca geográfica que está em período inválido em relação ao tempo do usuário na solicitação. |
| `isEventPublished` | boolean | True se pelo menos um evento for publicado no assinante de evento do Azure Mapas e false se nenhum evento for publicado no assinante de evento do Azure Mapas. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Título  |Descrição  |
|---------|---------|
| [Reagir a eventos do Azure Mapas usando a Grade de Eventos](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração do Azure Mapas com a Grade de Eventos. |
| [Tutorial: configurar uma cerca geográfica](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Este tutorial orienta você pelas etapas básicas para configurar uma cerca geográfica usando o Azure Mapas. Você usa a Grade de Eventos do Azure para transmitir os resultados da cerca geográfica e configurar uma notificação de acordo com os resultados desse limite. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
