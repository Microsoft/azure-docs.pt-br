---
title: Reagir a eventos do Azure Mapas usando a Grade de Eventos
description: Descubra como reagir aos eventos do Azure Maps que envolvem limites geográficos. Veja como escutar eventos de mapeamento e como usar a grade de eventos para redirecionar eventos para manipuladores de eventos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92889759"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagir a eventos do Azure Mapas usando a Grade de Eventos

O Azure Maps integra-se à grade de eventos do Azure, para que os usuários possam enviar notificações de eventos para outros serviços e disparar processos downstream. A finalidade deste artigo é ajudá-lo a configurar seus aplicativos de negócios para ouvir os eventos do Azure Maps. Isso permite que os usuários reajam a eventos críticos de maneira confiável, escalonável e segura. Por exemplo, os usuários podem criar um aplicativo para atualizar um banco de dados, criar um tíquete e entregar uma notificação por email, toda vez que um dispositivo entrar em uma cerca geográfica.

A Grade de Eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação-assinatura. A Grade de Eventos tem suporte interno para serviços do Azure, como [Azure Functions](../azure-functions/functions-overview.md) e [Aplicativos Lógicos do Azure](../azure-functions/functions-overview.md). Ela pode entregar alertas de eventos para serviços que não são do Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md).


![Modelo funcional da Grade de Eventos do Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos do Azure Mapas

A Grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. Uma conta do Azure Mapas emite os seguintes tipos de eventos: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando as coordenadas recebidas foram movidas de fora de uma determinada limite geopara dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando as coordenadas recebidas foram movidas de dentro de uma determinada cerca geográfica para externa |
| Microsoft.Maps.GeofenceResult | Gerado sempre que uma consulta de delimitação geográfica retorna um resultado, independentemente do estado |

## <a name="event-schema"></a>Esquema do evento

O exemplo a seguir mostra o esquema para GeofenceResult:

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

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicativos que manipulam eventos de limite geográfico do Azure Mapas devem seguir algumas práticas recomendadas:

* Configure várias assinaturas para rotear eventos para o mesmo manipulador de eventos. É importante não presumir que os eventos são de uma fonte específica. Sempre verifique o tópico da mensagem para garantir que a mensagem provém da origem esperada.
* Use o `X-Correlation-id` campo no cabeçalho de resposta para entender se suas informações sobre objetos estão atualizadas. As mensagens podem ser recebidas fora de ordem ou após um atraso.
* Quando uma solicitação GET ou uma POST na API de isolamento geométrico é chamada com o parâmetro mode definido como `EnterAndExit` , um evento Enter ou Exit é gerado para cada geometria no limite geográfico para o qual o status foi alterado da chamada à API de limite geográfico anterior.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o isolamento geográfico para operações de controle em um local de construção, confira:

> [!div class="nextstepaction"] 
> [Configurar um limite geográfico usando o Azure Mapas](tutorial-geofence.md)