---
title: Status de operações assíncronas – Azure Resource Manager
description: Descreve como rastrear operações assíncronas no Azure. Mostra os valores que você pode usar para obter o status de uma operação de longa execução.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1b05ed50f08ddbf2eb5da8e08f5bf623596e1f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061355"
---
# <a name="track-asynchronous-azure-operations"></a>Rastrear operações assíncronas no Azure
Algumas operações REST do Azure são executadas de forma assíncrona porque a operação não pode ser concluída com rapidez. Este artigo descreve como controlar o status das operações assíncronas por meio de valores retornados na resposta.  

## <a name="status-codes-for-asynchronous-operations"></a>Códigos de status de operações assíncronas
Uma operação assíncrona inicialmente retorna um dos seguintes códigos de status HTTP:

* 201 (Criado)
* 202 (Aceito) 

Quando a operação for concluída com êxito, ela retorna um dos seguintes:

* 200 (OK)
* 204 (Sem Conteúdo) 

Consulte a [Documentação da API REST](/rest/api/) para ver as respostas para a operação que você está executando.

## <a name="monitor-status-of-operation"></a>Monitorar o status da operação
As operações REST assíncronas retornam valores de cabeçalho, que você pode usar para determinar o status da operação. Há potencialmente três valores de cabeçalho a examinar:

* `Azure-AsyncOperation` ‑ A URL para verificar o status da operação em andamento. Se a operação retornar esse valor, sempre o use (em vez de Location) para acompanhar o status da operação.
* `Location` ‑ A URL para determinar quando uma operação foi concluída. Use esse valor somente quando Azure-AsyncOperation não for retornado.
* `Retry-After` ‑ O número de segundos de espera antes de verificar o status da operação assíncrona.

No entanto, nem toda operação assíncrona retorna todos esses valores. Por exemplo, você precisará avaliar o valor do cabeçalho Azure-AsyncOperation para uma operação e o valor do cabeçalho Location para outra operação. 

Recupere os valores de cabeçalho da mesma forma que faria com qualquer valor de cabeçalho para uma solicitação. Por exemplo, em C#, recupere o valor de cabeçalho de um objeto `HttpWebResponse` chamado `response` com o código a seguir:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Solicitação e resposta de Azure-AsyncOperation

Para obter o status da operação assíncrona, envie uma solicitação GET para a URL no valor do cabeçalho Azure-AsyncOperation.

O corpo da resposta dessa operação contém informações sobre a operação. O exemplo a seguir mostra os possíveis valores retornados da operação:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Somente `status` é retornado para todas as respostas. O objeto de erro é retornado quando o status é Falha ou Cancelado. Todos os outros valores são opcionais, portanto, a resposta recebida pode parecer diferente do exemplo.

## <a name="provisioningstate-values"></a>Valores de provisioningState

Operações que criam, atualizam ou excluem (PUT, PATCH, DELETE) um recurso geralmente retornam um valor `provisioningState`. Quando uma operação for concluída, um dos três valores a seguir será retornado: 

* Bem-sucedida
* Com falha
* Cancelado

Todos os outros valores indicam que a operação ainda está em execução. O provedor de recursos pode retornar um valor personalizado que indica o estado. Por exemplo, você pode receber **Aceito** quando a solicitação é recebida e está em execução.

## <a name="example-requests-and-responses"></a>Exemplo de solicitações e respostas

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Iniciar a máquina virtual (202 com Azure-AsyncOperation)
Este exemplo mostra como determinar o status da operação **start** para máquinas virtuais. A solicitação inicial está no seguinte formato:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Ele retorna um código de status 202. Entre os valores de cabeçalho, você verá:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Para verificar o status da operação assíncrona, envie outra solicitação para a URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

O corpo da resposta contém o status da operação:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Implantar recursos (201 com Azure-AsyncOperation)

Este exemplo mostra como determinar o status da operação **deployments** para implantação de recursos no Azure. A solicitação inicial está no seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Ele retorna um código de status 201. O corpo da resposta inclui:

```json
"provisioningState":"Accepted",
```

Entre os valores de cabeçalho, você verá:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Para verificar o status da operação assíncrona, envie outra solicitação para a URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

O corpo da resposta contém o status da operação:

```json
{"status":"Running"}
```

Quando a implantação for concluída, a resposta conterá:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Criar conta de armazenamento (202 com Location e Retry-After)

Este exemplo mostra como determinar o status da operação **create** para contas de armazenamento. A solicitação inicial está no seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

E o corpo da solicitação contém as propriedades da conta de armazenamento:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Ele retorna um código de status 202. Entre os valores de cabeçalho, você verá os dois valores a seguir:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Depois de aguardar o número de segundos especificado em Retry-After, verifique o status da operação assíncrona enviando outra solicitação para a URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Se a solicitação ainda estiver em execução, você receberá um código de status 202. Se a solicitação tiver sido concluída, você receberá um código de status 200 e o corpo da resposta conterá as propriedades da conta de armazenamento que foi criada.

## <a name="next-steps"></a>Próximas etapas

* Para ver a documentação sobre cada operação REST, consulte [Documentação da API REST](/rest/api/).
* Para obter informações sobre a implantação de modelos por meio da API REST do Resource Manager, consulte [Deploy resources with Resource Manager templates and Resource Manager REST API (Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager)](resource-group-template-deploy-rest.md).