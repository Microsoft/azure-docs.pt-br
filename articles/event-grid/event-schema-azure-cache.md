---
title: Cache do Azure para Redis como origem da grade de eventos
description: Descreve as propriedades fornecidas para o cache do Azure para eventos Redis com a grade de eventos do Azure
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055633"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Cache do Azure para Redis como uma fonte de grade de eventos

Este artigo fornece as propriedades e o esquema para o cache do Azure para eventos Redis. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). 

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Lista de eventos para o cache do Azure para APIs REST do Redis

Esses eventos são disparados quando um cliente exporta, importa ou dimensiona chamando o cache do Azure para APIs REST do Redis. O evento de aplicação de patch é disparado pela atualização do Redis.

 |Nome do evento |Descrição|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |Disparado quando os dados do cache são exportados. |
 |**Microsoft. cache. ImportRDBCompleted** |Disparado quando os dados do cache são importados. |
 |**Microsoft. cache. PatchingCompleted** |Disparado quando a aplicação de patch é concluída. |
 |**Microsoft. cache. ScalingCompleted** |Disparado quando o dimensionamento é concluído. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é disparado, o serviço de Grade de Eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.

Esta seção contém um exemplo de como os dados seriam para cada evento do cache do Azure para Redis.

### <a name="microsoftcachepatchingcompleted-event"></a>Evento Microsoft. cache. PatchingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Evento Microsoft. cache. ImportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Evento Microsoft. cache. ExportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Cache do Azure para dados de evento Redis. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| timestamp | string | A hora em que o evento ocorreu. |
| name | string | O nome do evento. |
| status | string | O status do evento. Com falha ou com êxito. |


## <a name="quickstarts"></a>Inícios rápidos

Se você quiser experimentar o cache do Azure para eventos Redis, consulte qualquer um destes artigos de início rápido:

|Se você quiser usar essa ferramenta:    |Consulte este artigo: |
|--|-|
|Portal do Azure    |[Início rápido: encaminhar o cache do Azure para eventos Redis para o ponto de extremidade da Web com o portal do Azure](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Início rápido: encaminhe o cache do Azure para eventos Redis para o ponto de extremidade da Web com o PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|CLI do Azure    |[Início rápido: encaminhar o cache do Azure para eventos Redis para o ponto de extremidade da Web com CLI do Azure](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).

