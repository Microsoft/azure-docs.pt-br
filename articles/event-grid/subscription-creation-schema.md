---
title: Esquema de assinatura de Grade de Eventos do Azure
description: Descreve as propriedades para assinar um evento com a grade de eventos do Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 9464ab89e08f53f61cb6f5a4b1e91da35b785af0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822795"
---
# <a name="event-grid-subscription-schema"></a>Esquema de assinatura de Grade de Eventos

Para criar uma assinatura de grade de eventos, você envia uma solicitação para a operação de assinatura Create Event. Use o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Por exemplo, para criar uma inscrição de evento para uma conta de armazenamento denominada `examplestorage` em um grupo de recursos denominado `examplegroup`, use o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

O nome da assinatura de evento deve ter 3 a 64 caracteres de comprimento e só pode conter a-z, A-Z, 0 a 9, e "-". O artigo descreve as propriedades e o esquema para o corpo da solicitação.
 
## <a name="event-subscription-properties"></a>Propriedades da assinatura do evento

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| destino | objeto | O objeto que define o ponto de extremidade. |
| filtro | objeto | Um campo opcional para filtrar os tipos de eventos. |

### <a name="destination-object"></a>objeto de destino

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| endpointType | string | O tipo de ponto de extremidade para a assinatura (webhook/HTTP, o Hub de evento ou fila). | 
| endpointUrl | string | A URL de destino para eventos nesta assinatura de evento. | 

### <a name="filter-object"></a>objeto filter

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| includedEventTypes | matriz | Correspondência quando o tipo de evento na mensagem de evento é uma correspondência exata para esses nomes de tipo de evento. Gera um erro quando o nome do evento não coincide com os nomes de tipo de evento registrados para a origem do evento. O padrão corresponde a todos os tipos de evento. |
| subjectBeginsWith | string | Uma correspondência de prefixo de filtro para o campo de assunto no evento mensagem. A cadeia de caracteres padrão ou vazia corresponde a tudo. | 
| subjectEndsWith | string | Uma correspondência de sufixo de filtro para o campo de assunto no evento mensagem. A cadeia de caracteres padrão ou vazia corresponde a tudo. |
| isSubjectCaseSensitive | string | Controla a correspondência que diferencia maiúsculas e minúsculas para filtros. |


## <a name="example-subscription-schema"></a>Esquema de assinatura de exemplo

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [O que é uma Grade de eventos?](overview.md)