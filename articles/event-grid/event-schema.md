---
title: Esquema de eventos da Grade de Eventos do Azure
description: Descreve as propriedades e o esquema que estão presentes para todos os eventos.Os eventos consistem em um conjunto de cinco propriedades de cadeia de caracteres obrigatórias e um objeto data obrigatório.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: 7ddc7c78c5a9e5ba2a57b21c45fb9fab65056ee9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105873"
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos do Azure

Este artigo descreve as propriedades e esquema que estão presentes para todos os eventos.Os eventos consistem em um conjunto de cinco propriedades de cadeia de caracteres obrigatórias e um objeto data obrigatório. As propriedades são comuns a todos os eventos de qualquer fornecedor. O objeto de dados tem propriedades que são específicas de cada fornecedor. Para tópicos do sistema, essas propriedades são específicas ao provedor de recursos, como Armazenamento do Azure ou Hub de Eventos do Azure.

As fontes de eventos enviam eventos para o Grade de Eventos do Azure em uma matriz, a qual pode ter vários objetos de eventos. Ao postar eventos em um tópico da grade de eventos, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz é limitado a 1 MB. Se um evento ou a matriz for maior do que os limites de tamanho, você receberá a resposta **O conteúdo 413 é muito grande**. No entanto, as operações são cobradas em incrementos de 64 KB. Assim, os eventos com mais de 64 KB incorrerão em encargos de operações como se fossem vários eventos. Por exemplo, um evento com 130 KB incorreria em operações como se fosse três eventos separados.

A Grade de Eventos envia os eventos aos assinantes em uma matriz que tem um único evento. Esse comportamento poderá alterar no futuro.

Você pode encontrar o esquema JSON para o evento de Grade de Eventos e a carga de dados de cada publicador Azure no [armazenamento do Esquema de Evento](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Esquema do evento

O exemplo a seguir mostra as propriedades que são usadas por todos os editores de eventos:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Por exemplo, o esquema publicado para um evento de armazenamento de Blob do Azure é:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
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
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Propriedades do evento

Todos os eventos terão os mesmos dados de nível superior a seguir:

| Propriedade | Type | Obrigatório | Descrição |
| -------- | ---- | -------- | ----------- |
| topic | string | Não, mas se incluído, deve corresponder exatamente ao tópico da grade de eventos Azure Resource Manager ID. Se não estiver incluído, a grade de eventos será carimbada no evento. | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Sim | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Sim | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | Sim | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Sim | Identificador exclusivo do evento. |
| data | objeto | Não | Dados do evento específicos ao provedor de recursos. |
| dataVersion | string | Não, mas será carimbado com um valor vazio. | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | Não obrigatório, mas se incluído, deve corresponder exatamente ao esquema da grade de eventos `metadataVersion` (atualmente, somente `1` ). Se não estiver incluído, a grade de eventos será carimbada no evento. | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |

Para saber mais sobre as propriedades no objeto de dados, consulte a origem do evento:

* [Assinaturas do Azure (operações de gerenciamento)](event-schema-subscriptions.md)
* [Registro de Contêiner](event-schema-container-registry.md)
* [Armazenamento de Blobs](event-schema-blob-storage.md)
* [Hubs de Evento](event-schema-event-hubs.md)
* [Hub IoT](event-schema-iot-hub.md)
* [Serviços de Mídia](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupos de recursos (operações de gerenciamento)](event-schema-resource-groups.md)
* [Barramento de Serviço](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Para tópicos personalizados, o publicador do evento determina o objeto de dados. Os dados de nível superior devem ter os mesmos campos do que os eventos definidos pelo recurso padrão.

Ao publicar eventos em tópicos personalizados, crie assuntos para os eventos que tornem mais fácil aos assinantes reconhecer se estão interessados no evento. Os assinantes usam o assunto para filtrar e rotear eventos. Forneça o caminho do acontecimento do evento para que os assinante possam filtrar por segmentos desse caminho. O caminho permite que os assinantes filtrem eventos de maneira restrita ou ampla. Por exemplo, se você fornecer um caminho de três segmentos como `/A/B/C` no assunto, os assinantes poderão filtrar pelo primeiro segmento `/A` para obter um conjunto amplo de eventos. Esses assinantes recebem eventos com assuntos como `/A/B/C` ou `/A/D/E`. Outros assinantes podem filtrar por `/A/B` para obter um conjunto de eventos mais restrito.

Às vezes, o assunto precisa apresentar mais detalhes sobre o acontecimento. Por exemplo, o publicador da **Conta de Armazenamento** fornece o assunto `/blobServices/default/containers/<container-name>/blobs/<file>` quando um arquivo é adicionado a um contêiner. Um assinante pode filtrar pelo caminho `/blobServices/default/containers/testcontainer` para obter todos os eventos para esse contêiner, mas não para outros contêineres na conta de armazenamento. Um assinante também pode filtrar ou rotear pelo sufixo `.txt` para trabalhar apenas com arquivos de texto.

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
