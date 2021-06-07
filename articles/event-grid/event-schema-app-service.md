---
title: Serviço de Aplicativo do Azure como origem da Grade de Eventos
description: Este artigo descreve como usar o Serviço de Aplicativo do Azure como uma origem do evento da Grade de Eventos. Ele fornece o esquema e os links para os artigos do tutorial e de instruções.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 03/06/2021
ms.author: jafreebe
ms.openlocfilehash: 404fac634a628da49dee72b10b52785820fe1bf6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443770"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Serviço de Aplicativo do Azure como uma origem da Grade de Eventos

Este artigo fornece as propriedades e o esquema dos eventos do Serviço de Aplicativo do Azure. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também fornece uma lista de inícios rápidos e de tutoriais para usar o Serviço de Aplicativo do Azure como uma origem do evento.

## <a name="available-event-types"></a>Tipos de evento disponíveis

O Serviço de Aplicativo do Azure emite os tipos de evento a seguir

|    Tipo de evento                                             |    Descrição                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    Disparado quando um backup é iniciado                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    Disparado quando um backup é concluído                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    Disparado quando há falha em um backup                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    Disparado quando uma restauração de um backup é iniciada        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    Disparado quando uma restauração de um backup é concluída      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    Disparado quando há falha na restauração de um backup         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    Disparado quando uma troca de slot é iniciada                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    Disparado quando uma troca de slot é concluída                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    Disparado quando há falha em uma troca de slot                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    Disparado quando uma troca de slot com versão prévia é iniciada           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    Disparado quando uma troca de slot com versão prévia é cancelada    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    Disparado quando um site é reiniciado                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    Disparado quando um site é interrompido                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    Disparado quando as configurações do aplicativo de um site são alteradas             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    Disparado quando um plano do Serviço de Aplicativo é atualizado                 |

## <a name="properties-common-to-all-events"></a>Propriedades comuns a todos os eventos

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)
Quando um evento é disparado, o serviço de Grade de Eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.
Esta seção mostra um exemplo de como os dados seriam para cada evento. Cada evento tem os seguintes dados de nível superior:

|     Propriedade          |     Type     |     Descrição                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    string    |    Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor.                                      |
|    `subject`            |    string    |    Caminho definido pelo fornecedor para o assunto do evento.                                                                                              |
|    `eventType`          |    string    |    Um dos tipos de evento registrados para a origem do evento.                                                                                  |
|    `eventTime`          |    string    |    A hora em que o evento é gerado com base na hora UTC do provedor.                                                                         |
|    `id`                 |    string    |    Identificador exclusivo do evento.                                                                                                            |
|    `data`               |    objeto    |    Dados de eventos do armazenamento de blob.                                                                                                                    |
|    `dataVersion`        |    string    |    A versão do esquema do objeto de dados. O fornecedor define a versão do esquema.                                                          |
|    `metadataVersion`    |    string    |    A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor.    |

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

Quando um evento é disparado, o serviço de Grade de Eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.
Esta seção mostra um exemplo de como os dados seriam para cada evento. Cada evento tem os seguintes dados de nível superior:

|     Propriedade          |     Type     |     Descrição                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    string    |    Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor.                                      |
|    `subject`            |    string    |    Caminho definido pelo fornecedor para o assunto do evento.                                                                                              |
|    `type`          |    string    |    Um dos tipos de evento registrados para a origem do evento.                                                                                  |
|    `time`          |    string    |    A hora em que o evento é gerado com base na hora UTC do provedor.                                                                         |
|    `id`                 |    string    |    Identificador exclusivo do evento.                                                                                                            |
|    `data`               |    objeto    |    Dados de eventos do armazenamento de blob.                                                                                                                    |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---

## <a name="example-events"></a>Eventos de exemplo

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    `action`                  |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que teve este evento                                                                          |
|    `clientRequestId`         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    `correlationRequestId`    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    `requestId`               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    `address`                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    `verb`                    |    string    |    Verbo HTTP desta operação                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    `action`                  |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que teve este evento                                                                          |
|    `clientRequestId`         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    `correlationRequestId`    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    `requestId`               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    `address`                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    `verb`                    |    string    |    Verbo HTTP desta operação                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    `action`                 |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que teve este evento                                                                          |
|    `clientRequestId`         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    `correlationRequestId`    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|   `requestId`               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    `address`                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    `verb`                    |    string    |    Verbo HTTP desta operação                                                                                       |
|    `sourceSlot`              |    string    |    O slot de origem da troca                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    `action`                 |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que teve este evento                                                                          |
|    `clientRequestId`         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    `correlationRequestId`    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    `requestId`               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    `address`                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    `verb`                    |    string    |    Verbo HTTP desta operação                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados tem as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    `action`                  |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que teve este evento                                                                          |
|    `clientRequestId`         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    `correlationRequestId`    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    `requestId`               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    `address`                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    `verb`                    |    string    |    Verbo HTTP desta operação                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados tem as seguintes propriedades:

|    Propriedade                         |    Type      |    Descrição                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    objeto    |    Detalhe da ação no plano do serviço de aplicativo                                                                          |
|    `stampKind`                        |    string    |    Tipo de ambiente em que está o plano do serviço de aplicativo                                                                     |
|    `action`                           |    string    |    Tipo da ação no plano do serviço de aplicativo                                                                            |
|    `status`                           |    string    |    Status da operação no plano do serviço de aplicativo                                                                   |
|    `sku`                              |    objeto    |    sku do plano de serviço de aplicativo                                                                                       |
|    `name`                             |    string    |    nome do plano de serviço de aplicativo                                                                                      |
|    `Tier`                             |    string    |    camada do plano de serviço de aplicativo                                                                                      |
|    `Size`                             |    string    |    tamanho do plano de serviço de aplicativo                                                                                      |
|    `Family`                           |    string    |    família do plano de serviço de aplicativo                                                                                        |
|    `Capacity`                         |    string    |    capacidade do plano de serviço de aplicativo                                                                                      |
|    `action`                           |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                             |    string    |    nome do site que teve este evento                                                                          |
|    `clientRequestId`                  |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    `correlationRequestId`             |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    `requestId`                        |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    `address`                         |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    `verb`                             |    string    |    Verbo HTTP desta operação                                                                                       |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md)