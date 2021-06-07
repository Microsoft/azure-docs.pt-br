---
title: Configuração de Azure App como fonte de grade de eventos
description: Este artigo descreve como usar a configuração de Azure App como uma origem de evento de grade de eventos. Ele fornece o esquema e os links para os artigos do tutorial e de instruções.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: a64c6fead5e6d95ba11bc98d7e9a52e3021c3be2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366765"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Configuração de Azure App como uma fonte de grade de eventos
Este artigo fornece as propriedades e o esquema para Azure App eventos de configuração. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também fornece uma lista de inícios rápidos e tutoriais para usar Azure App configuração como uma fonte de eventos.

## <a name="available-event-types"></a>Tipos de evento disponíveis

Azure App configuração emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Gerado quando um valor de chave é criado ou substituído. |
| Microsoft. AppConfiguration. KeyValueDeleted | Gerado quando um valor de chave é excluído. |

## <a name="example-event"></a>Exemplo de evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)
O exemplo a seguir mostra o esquema de um evento de chave-valor modificado: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento de chave-valor excluído é semelhante: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

O exemplo a seguir mostra o esquema de um evento de chave-valor modificado: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueModified",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

O esquema para um evento de chave-valor excluído é semelhante: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueDeleted",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
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
| `data` | object | Dados de evento de configuração de aplicativo. |
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
| `data` | object | Dados de evento de configuração de aplicativo. |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `key` | string | A chave do valor de chave que foi modificado ou excluído. |
| `label` | string | O rótulo, se houver, do valor de chave que foi modificado ou excluído. |
| `etag` | string | Para `KeyValueModified` a eTag do novo valor de chave. Para `KeyValueDeleted` a eTag do valor de chave que foi excluído. |


## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções

|Título | Descrição |
|---------|---------|
| [Reagir a Azure App eventos de configuração usando a grade de eventos](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração da configuração de Azure App com a grade de eventos. |
| [Usar a Grade de Eventos para notificações de alteração de dados](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Saiba como usar Azure App assinaturas de evento de configuração para enviar eventos de modificação de chave-valor para um ponto de extremidade da Web. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter uma introdução ao trabalho com Azure App eventos de configuração, consulte [usar a grade de eventos para notificações de alteração de dados](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 