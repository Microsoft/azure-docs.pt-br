---
title: Esquema do CloudEvents v 1.0 com a grade de eventos do Azure
description: Descreve como usar o esquema CloudEvents v 1.0 para eventos na grade de eventos do Azure. O serviço é compatível com eventos na implementação JSON do CloudEvents.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324171"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>Esquema do CloudEvents v 1.0 com a grade de eventos do Azure

Além do seu [esquema de evento padrão](event-schema.md), a Grade de Eventos do Azure oferece suporte nativo a eventos na [implementação JSON do CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [ligação ao protocolo HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade, fornecendo um esquema comum do evento para publicar e consumir eventos com base em nuvem. Esse esquema permite ferramentas uniforme, formas padrão de roteamento e manipulação de eventos e maneiras universais de desserializar o esquema de evento externo. Com um esquema comum, você pode integrar facilmente mais trabalho entre plataformas.

O CloudEvents está sendo criado por vários [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, por meio da [Cloud Native Computing Foundation](https://www.cncf.io/). Ele está disponível como versão 1.0.

Este artigo descreve o esquema CloudEvents com a grade de eventos.

## <a name="sample-event-using-cloudevents-schema"></a>Exemplo de evento usando o esquema CloudEvents

Aqui está um exemplo de um evento de Armazenamento de Blob do Azure no formato CloudEvents:

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

Uma descrição detalhada dos campos disponíveis, seus tipos e definições no CloudEvents v1.0 está [disponível aqui](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores dos cabeçalhos para eventos entregues no esquema CloudEvents e no esquema da Grade de Eventos são os mesmos, exceto para `content-type`. Para o esquema CloudEvents, esse valor de cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"`. Para o esquema Grade de Eventos, esse valor de cabeçalho é `"content-type":"application/json; charset=utf-8"`.

## <a name="event-grid-for-cloudevents"></a>Grade de eventos para CloudEvents

Você pode usar a Grade de Eventos para entrada e saída de eventos no esquema CloudEvents. Você pode usar o CloudEvents para eventos do sistema, como eventos do Armazenamento de Blob e eventos do Hub IoT e eventos personalizados. Também pode transformar esses eventos durante a transmissão para trás e para frente.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato de CloudEvents | Formato de CloudEvents
| Formato da Grade de Eventos  | Formato de CloudEvents
| Formato da Grade de Eventos  | Formato da Grade de Eventos

Para todos os esquemas de evento, a Grade de Eventos requer validação ao publicar em um tópico de grade de eventos e ao criar uma inscrição de evento. Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

## <a name="next-steps"></a>Próximas etapas
Consulte [como usar o esquema do CloudEvents v 1.0 com a grade de eventos](cloudevents-schema.md).  
