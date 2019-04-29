---
title: Esquema de evento grupo de recursos da Grade de Eventos do Azure
description: Descreve as propriedades que são fornecidas para eventos de grupos de recursos com a Grade de Eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 6cbfc06f380d7c4818ca82e858c23bb18849fb7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561686"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Esquema de eventos para assinatura da Grade de Eventos do Azure

Este artigo fornece as propriedades e o esquema de eventos de grupo de recursos. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

Grupos de recursos e as assinaturas do Azure emitem os mesmos tipos de evento. Os tipos de eventos estão relacionados a alterações de recursos ou ações. A principal diferença é que grupos de recursos de emissão de eventos para os recursos no grupo de recursos e as assinaturas do Azure emitem eventos de recursos entre a assinatura.

Eventos de recursos são criados para PUT, PATCH, POST e excluir operações que são enviadas para `management.azure.com`. Operações GET não criam eventos. Operações enviadas para o plano de dados (como `myaccount.blob.core.windows.net`) não criar eventos. Os eventos de ação fornecem dados de evento para operações como listar as chaves para um recurso.

Quando você assina eventos para um grupo de recursos, seu ponto de extremidade recebe todos os eventos para esse grupo de recursos. Os eventos podem incluir eventos que você deseja ver, como a atualização de uma máquina virtual, mas também eventos que talvez não são importantes para você, como gravar uma nova entrada no histórico de implantação. Você pode receber todos os eventos no seu ponto de extremidade e escrever um código que processa os eventos que você deseja manipular. Ou, você pode definir um filtro ao criar a assinatura de evento.

Para manipular programaticamente os eventos, você pode classificar eventos, observando o `operationName` valor. Por exemplo, o ponto de extremidade de evento apenas pode processar eventos para operações que são iguais a `Microsoft.Compute/virtualMachines/write` ou `Microsoft.Storage/storageAccounts/write`.

O assunto do evento é a ID de recurso do recurso que é o destino da operação. Para filtrar eventos para um recurso, forneça esse recurso ao criar a assinatura de evento da ID.  Para filtrar por um tipo de recurso, use um valor no seguinte formato: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Para obter uma lista de scripts de exemplo e tutoriais, consulte [Origem do evento do grupo de recursos](event-sources.md#resource-groups).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Os Grupos de Recursos agora podem emitir eventos de gerenciamento do Azure Resource Manager, como quando uma VM é criada ou uma conta de armazenamento é excluída.

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Gerado quando a ação no recurso é cancelada. |
| Microsoft.Resources.ResourceActionFailure | Gerado quando ocorre falha na ação no recurso. |
| Microsoft.Resources.ResourceActionSuccess | Gerado quando a ação no recurso terá êxito. |
| Microsoft.Resources.ResourceDeleteCancel | Gerado quando excluir operação seja cancelada. Esse evento ocorre quando a implantação de um modelo é cancelada. |
| Microsoft.Resources.ResourceDeleteFailure | Gerado quando ocorre falha de operação na exclusão. |
| Microsoft.Resources.ResourceDeleteSuccess | Gerado quando operação de exclusão for bem-sucedida. |
| Microsoft.Resources.ResourceWriteCancel | Gerado quando criar ou operação de atualização é cancelada. |
| Microsoft.Resources.ResourceWriteFailure | Gerado quando criar ou atualizar a operação falhará. |
| Microsoft.Resources.ResourceWriteSuccess | Gerado quando cria ou operação de atualização é bem-sucedida. |

## <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema para um evento **ResourceWriteSuccess**. O mesmo esquema é usado para os eventos **ResourceWriteFailure** e **ResourceWriteCancel** com valores diferentes para `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

O exemplo a seguir mostra o esquema para um evento **ResourceDeleteSuccess**. O mesmo esquema é usado para os eventos **ResourceDeleteFailure** e **ResourceDeleteCancel** com valores diferentes para `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

O exemplo a seguir mostra o esquema para um **ResourceActionSuccess** eventos. O mesmo esquema é usado para **ResourceActionFailure** e **ResourceActionCancel** eventos com valores diferentes para `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}" 
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento do grupo de recursos. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| autorização | objeto | A autorização solicitada para a operação. |
| declarações | objeto | As propriedades da declaração. Para obter mais informações, consulte [especificação JWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | string | Uma ID de operação para solução de problemas. |
| httpRequest | objeto | Os detalhes da operação. Esse objeto é apenas incluído ao atualizar um recurso existente ou excluir um recurso. |
| ResourceProvider | string | O provedor de recursos para a operação. |
| resourceUri | string | O URI do recurso na operação. |
| operationName | string | A operação que foi feita. |
| status | string | O status da operação. |
| subscriptionId | string | A ID da assinatura do recurso. |
| tenantId | string | A ID do locatário do recurso. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
