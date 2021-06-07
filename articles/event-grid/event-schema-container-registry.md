---
title: Registro de contêiner do Azure como origem da grade de eventos
description: Descreve as propriedades que são fornecidas para eventos de registro de contêiner com a grade de eventos do Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 7e19b223e43f30a532c1cd8bdc86f9a29220f5aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363569"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Registro de contêiner do Azure como uma fonte de grade de eventos

Este artigo apresenta as propriedades e o esquema para eventos de Registro de Contêiner. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

## <a name="available-event-types"></a>Tipos de evento disponíveis

O registro de contêiner do Azure emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Gerado quando é efetuado o push de uma imagem. |
| Microsoft.ContainerRegistry.ImageDeleted | Gerado quando uma imagem é excluída. |
| Microsoft. ContainerRegistry. ChartPushed | Gerado quando um gráfico de Helm é enviado por push. |
| Microsoft. ContainerRegistry. ChartDeleted | Gerado quando um gráfico de Helm é excluído. |

## <a name="example-event"></a>Exemplo de evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

O exemplo a seguir mostra o esquema de um evento de efetuar push de uma imagem: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento de exclusão de imagem é semelhante:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento de gráfico enviado é semelhante ao esquema para um evento enviado por push de imagem, mas não inclui um objeto de solicitação:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento de gráfico excluído é semelhante ao esquema para um evento excluído por imagem, mas não inclui um objeto de solicitação:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

O exemplo a seguir mostra o esquema de um evento de efetuar push de uma imagem: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "type": "ImagePushed",
  "time": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "specversion": "1.0"
}]
```

O esquema para um evento de exclusão de imagem é semelhante:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "type": "ImageDeleted",
  "time": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "specversion": "1.0"
}]
```

O esquema para um evento de gráfico enviado é semelhante ao esquema para um evento enviado por push de imagem, mas não inclui um objeto de solicitação:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "type": "Microsoft.ContainerRegistry.ChartPushed",
  "time": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "specversion": "1.0"
}]
```

O esquema para um evento de gráfico excluído é semelhante ao esquema para um evento excluído por imagem, mas não inclui um objeto de solicitação:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "type": "Microsoft.ContainerRegistry.ChartDeleted",
  "time": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
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
| `data` | objeto | Dados de eventos do armazenamento de blob. |
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
| `data` | objeto | Dados de eventos do armazenamento de blob. |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `id` | string | A ID do evento. |
| `timestamp` | string | A hora em que o evento ocorreu. |
| `action` | string | A ação que abrange o evento fornecido. |
| `target` | object | O destino do evento. |
| `request` | object | A solicitação que gerou o evento. |


O objeto de destino tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `mediaType` | string | O tipo MIME do objeto referenciado. |
| `size` | inteiro | O número de bytes do conteúdo. Mesmo que o campo de Comprimento. |
| `digest` | string | O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2. |
| `length` | inteiro | O número de bytes do conteúdo. O mesmo que o campo Tamanho. |
| `repository` | string | Nome do repositório. |
| `tag` | string | O nome da marcação. |
| `name` | string | O nome do gráfico. |
| `version` | string | A versão do gráfico. |

O objeto solicitado tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `id` | string | A ID da solicitação que iniciou o evento. |
| `addr` | string | O IP ou nome de host e, possivelmente, a porta da conexão do cliente que iniciou o evento. Esse valor é o RemoteAddr da solicitação http padrão. |
| `host` | string | O nome de host acessível externamente da instância de registro, conforme especificado pelo cabeçalho do host http em solicitações de entrada. |
| `method` | string | O método de solicitação que gerou o evento. |
| `useragent` | string | O cabeçalho do agente de usuário da solicitação. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Título |Descrição  |
|---------|---------|
| [Início Rápido: enviar eventos de registro de contêiner](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de Registro de Contêiner. |


## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
