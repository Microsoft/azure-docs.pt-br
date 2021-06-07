---
title: Status de operações assíncronas
description: Descreve como rastrear operações assíncronas no Azure. Mostra os valores que você pode usar para obter o status de uma operação de longa execução.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: e2c5ba137d5277466cf1b382d2b0b1bc02259f00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88723445"
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

Consulte a [Documentação da API REST](/rest/api/azure/) para ver as respostas para a operação que você está executando.

Depois de obter o código de resposta 201 ou 202, você estará pronto para monitorar o status da operação.

## <a name="url-to-monitor-status"></a>URL para monitorar o status

Há duas maneiras diferentes de monitorar o status da operação assíncrona. Você determina a abordagem correta examinando os valores de cabeçalho que são retornados de sua solicitação original. Primeiro, procure por:

* `Azure-AsyncOperation` ‑ A URL para verificar o status da operação em andamento. Se a operação retornar esse valor, use-o para acompanhar o status da operação.
* `Retry-After` ‑ O número de segundos de espera antes de verificar o status da operação assíncrona.

Se `Azure-AsyncOperation` não for um dos valores de cabeçalho, procure por:

* `Location` ‑ A URL para determinar quando uma operação foi concluída. Só use esse valor quando Azure-AsyncOperation não for retornado.
* `Retry-After` ‑ O número de segundos de espera antes de verificar o status da operação assíncrona.

## <a name="azure-asyncoperation-request-and-response"></a>Solicitação e resposta de Azure-AsyncOperation

Se você tiver uma URL do `Azure-AsyncOperation` valor do cabeçalho, envie uma solicitação get para essa URL. Use o valor de `Retry-After` para agendar a frequência de verificação do status. Você obterá um objeto de resposta que indica o status da operação. Uma resposta diferente é retornada ao verificar o status da operação com a `Location` URL. Para obter mais informações sobre a resposta de uma URL de local, consulte [criar conta de armazenamento (202 com local e tentar novamente-após)](#create-storage-account-202-with-location-and-retry-after).

As propriedades de resposta podem variar, mas sempre incluem o status da operação assíncrona.

```json
{
    "status": "{status-value}"
}
```

O exemplo a seguir mostra outros valores que podem ser retornados da operação:

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

O objeto de erro é retornado quando o status é Falha ou Cancelado. Todos os outros valores são opcionais. A resposta recebida pode parecer diferente do exemplo.

## <a name="provisioningstate-values"></a>Valores de provisioningState

Operações que criam, atualizam ou excluem (PUT, PATCH, DELETE) um recurso geralmente retornam um valor `provisioningState`. Quando uma operação for concluída, um dos três valores a seguir será retornado:

* Com sucesso
* Com falha
* Canceled

Todos os outros valores indicam que a operação ainda está em execução. O provedor de recursos pode retornar um valor personalizado que indica o estado. Por exemplo, você pode receber **Aceito** quando a solicitação é recebida e está em execução.

## <a name="example-requests-and-responses"></a>Exemplo de solicitações e respostas

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Iniciar a máquina virtual (202 com Azure-AsyncOperation)

Este exemplo mostra como determinar o status da [operação de inicialização para máquinas virtuais](/rest/api/compute/virtualmachines/start). A solicitação inicial está no seguinte formato:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

Ele retorna um código de status 202. Entre os valores de cabeçalho, você verá:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Para verificar o status da operação assíncrona, envie outra solicitação para a URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
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

Este exemplo mostra como determinar o status da [operação de implantações para implantar recursos](/rest/api/resources/deployments/createorupdate) no Azure. A solicitação inicial está no seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

Ele retorna um código de status 201. O corpo da resposta inclui:

```json
"provisioningState":"Accepted",
```

Entre os valores de cabeçalho, você verá:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Para verificar o status da operação assíncrona, envie outra solicitação para a URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

O corpo da resposta contém o status da operação:

```json
{
    "status": "Running"
}
```

Quando a implantação for concluída, a resposta conterá:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Criar conta de armazenamento (202 com Location e Retry-After)

Este exemplo mostra como determinar o status da operação de [criação para contas de armazenamento](/rest/api/storagerp/storageaccounts/create). A solicitação inicial está no seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

E o corpo da solicitação contém as propriedades da conta de armazenamento:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Ele retorna um código de status 202. Entre os valores de cabeçalho, você verá os dois valores a seguir:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Depois de aguardar o número de segundos especificado em Retry-After, verifique o status da operação assíncrona enviando outra solicitação para a URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

Se a solicitação ainda estiver em execução, você receberá um código de status 202. Se a solicitação tiver sido concluída, você receberá um código de status 200 e o corpo da resposta conterá as propriedades da conta de armazenamento que foi criada.

## <a name="next-steps"></a>Próximas etapas

* Para ver a documentação sobre cada operação REST, consulte [Documentação da API REST](/rest/api/azure/).
* Para obter informações sobre como implantar modelos por meio da API REST do Gerenciador de recursos, consulte [implantar recursos com modelos do Resource Manager e API REST do Gerenciador de recursos](../templates/deploy-rest.md).