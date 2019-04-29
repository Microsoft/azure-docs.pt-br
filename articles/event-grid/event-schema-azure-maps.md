---
title: Esquema de eventos do Azure Mapas da Grade de Eventos do Azure
description: Descreve as propriedades e o esquema fornecidos para eventos do Azure Mapas com a Grade de Eventos do Azure
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861847"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Esquema de eventos da Grade de Eventos do Azure para Azure Mapas

Este artigo fornece as propriedades e o esquema dos eventos do Azure Mapas. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta do Azure Mapas emite os seguintes tipos de eventos:

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando as coordenadas recebidas são movidas de fora de uma determinada cerca geográfica para dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando as coordenadas recebidas são movidas de dentro de uma determinada cerca geográfica para fora |
| Microsoft.Maps.GeofenceResult | Gerado sempre que uma consulta de delimitação geográfica retorna um resultado, independentemente do estado |

## <a name="event-examples"></a>Exemplos de eventos

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

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento de delimitação geográfica. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| apiCategory | string | Categoria da API do evento. |
| apiName | string | Nome da API do evento. |
| issues | objeto | Lista os problemas encontrados durante o processamento. Se algum problema for retornado, não haverá geometrias retornadas com a resposta. |
| responseCode | número | Código de resposta HTTP |
| geometries | objeto | Lista as geometrias de delimitação que contêm a posição da coordenada ou sobrepõem o searchBuffer em torno da posição. |

O objeto de erro é retornado quando ocorre um erro na API de Mapas. O objeto de erro tem as seguintes propriedades:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| error | ErrorDetails |Esse objeto é retornado quando ocorre um erro na API de Mapas  |

O objeto ErrorDetails é retornado quando ocorre um erro na API de Mapas. O ErrorDetails ou objeto tem as seguintes propriedades:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| código | string | O código de status do HTTP. |
| message | string | Se disponível, uma descrição do erro em formato legível por humanos. |
| innererror | InnerError | Se disponível, um objeto contendo informações específicas do serviço sobre o erro. |

O InnerError é um objeto que contém informações específicas do serviço sobre o erro. O objeto InnerError tem as seguintes propriedades: 

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| código | string | A mensagem de erro. |

O objeto de geometrias lista as IDs de geometria das cercas geográficas que expiraram em relação ao tempo do usuário na solicitação. O objeto de geometrias contém itens de geometria com as seguintes propriedades: 

| Propriedade | Type | DESCRIÇÃO |
|:-------- |:---- |:----------- |
| deviceid | string | ID do dispositivo. |
| distância | string | <p>Distância da coordenada até a borda mais próxima da cerca geográfica. Positivo significa que a coordenada está fora da cerca geográfica. Se a coordenada estiver fora da cerca geográfica, mas for maior que o valor de searchBuffer distante da borda da cerca geográfica mais próxima, o valor será 999. Negativo significa que a coordenada está dentro da cerca geográfica. Se a coordenada estiver dentro do polígono, mas for maior que o valor de searchBuffer distante da borda de delimitação geográfica mais próxima, o valor será -999. Um valor de 999 significa que há grande confiança de que a coordenada esteja bem fora da cerca geográfica. Um valor de -999 significa que há uma grande confiança de que a coordenada esteja bem dentro da cerca geográfica.<p> |
| geometryid |string | A ID exclusiva identifica a geometria da cerca geográfica. |
| nearestlat | número | Latitude do ponto mais próximo da geometria. |
| nearestlon | número | Longitude do ponto mais próximo da geometria. |
| udId | string | A ID exclusiva retornada do serviço de upload do usuário ao carregar uma cerca geográfica. Não será incluída na API de postagem de delimitação geográfica. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Listas da ID de geometria da cerca geográfica que é expirada em relação ao tempo do usuário na solicitação. |
| geometries | geometries[] |Lista as geometrias de delimitação que contêm a posição da coordenada ou sobrepõem o searchBuffer em torno da posição. |
| invalidPeriodGeofenceGeometryId | string[]  | Listas da ID de geometria da cerca geográfica que está em período inválido em relação ao tempo do usuário na solicitação. |
| isEventPublished | boolean | True se pelo menos um evento for publicado no assinante de evento do Azure Mapas e false se nenhum evento for publicado no assinante de evento do Azure Mapas. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).