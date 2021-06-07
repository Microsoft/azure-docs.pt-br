---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011112"
---
| Propriedade | Descrição |
|:--- |:---|
|**time** | A hora universal coordenada (UTC) quando a solicitação foi recebida pelo armazenamento. Por exemplo: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | A ID do recurso da conta de armazenamento. Por exemplo: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | A categoria da operação solicitada. Por exemplo: `StorageRead`, `StorageWrite` ou `StorageDelete`|
|**operationName** | O tipo de operação REST executado. <br> Para obter uma lista completa de operações, consulte o tópico [Análise de Armazenamento operações registradas e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | A versão do serviço de armazenamento especificada quando a solicitação foi feita. Isso é equivalente ao valor do cabeçalho **x-ms-version**. Por exemplo: `2017-04-17`.|
|**schemaVersion** | A versão do esquema do registro. Por exemplo: `1.0`.|
|**statusCode** | O código de status HTTP da solicitação. Se a solicitação for interrompida, esse valor poderá ser definido como `Unknown`. <br> Por exemplo: `206` |
|**statusText** | O status da operação solicitada.  Para obter uma lista completa de mensagens de status, consulte o tópico [Análise de Armazenamento operações registradas e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Na versão 2017-04-17 e posterior, a mensagem de status `ClientOtherError` não é usada. Em vez disso, esse campo contém um código de erro. Por exemplo: `SASSuccess`  |
|**durationMs** | O tempo total, expressado em milissegundos para executar a operação solicitada. Isso inclui o tempo de leitura da solicitação de entrada e envio da resposta ao solicitante. Por exemplo: `12`.|
|**callerIpAddress** | O endereço IP do solicitante, incluindo o número da porta. Por exemplo: `192.100.0.102:4362`. |
|**correlationId** | A ID usada para correlacionar os logs entre os recursos. Por exemplo: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**local** | O local da conta de armazenamento. Por exemplo: `North Europe`. |
|**protocol**|O protocolo usado na operação. Por exemplo: `HTTP`, `HTTPS`, `SMB` ou `NFS`|
| **uri** | Identificador de recurso uniforme que é solicitado. |