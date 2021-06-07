---
title: API de execução prolongada do Azure Mapas
description: Saiba mais sobre o processamento em segundo plano assíncrono prolongado no Azure Mapas
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f5fb7c8059c8b98e8ec514a4159e96f48db7b1ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906192"
---
# <a name="creator-preview-long-running-operation-api"></a>API de operação do criador (versão prévia) Long-Running

> [!IMPORTANT]
> Os serviços do Criador do Azure Mapas estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Algumas APIs no Azure Maps usam um [padrão de Request-Reply assíncrona](/azure/architecture/patterns/async-request-reply). Esse padrão permite que o Azure Mapas forneça serviços altamente disponíveis e responsivos. Este artigo explica a implementação específica do processamento em segundo plano assíncrono de execução prolongada do Azure Mapa.

## <a name="submitting-a-request"></a>Enviando uma solicitação

Um aplicativo cliente inicia uma operação de execução prolongada por meio de uma chamada síncrona para uma API HTTP. Normalmente, essa chamada está na forma de uma solicitação HTTP POST. Quando uma carga de trabalho assíncrona é criada, a API retorna um código de status `202` do HTTP, indicando que a solicitação foi aceita. Essa resposta contém um cabeçalho `Location` apontando para um ponto de extremidade que o cliente pode sondar para verificar o status da operação de execução prolongada.

### <a name="example-of-a-success-response"></a>Exemplo de uma resposta de êxito

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Se a chamada não passar na validação, a API retornará uma resposta `400` de HTTP para uma solicitação inadequada. O corpo da resposta fornecerá ao cliente mais informações sobre o motivo de a solicitação ser inválida.

### <a name="monitoring-the-operation-status"></a>Monitorando o status da operação

O ponto de extremidade de localização fornecido nos cabeçalhos de resposta aceitos pode ser sondado para verificar o status da operação de execução prolongada. O corpo da resposta da solicitação de status da operação sempre conterá as propriedades `status` e `createdDateTime`. A propriedade `status` mostra o estado atual da operação de execução prolongada. Os estados possíveis incluem `"NotStarted"`, `"Running"`, `"Succeeded"` e `"Failed"`. A propriedade `createdDateTime` mostra a hora em que a solicitação inicial foi feita para iniciar a operação de execução prolongada. Quando o estado for `"NotStarted"` ou `"Running"`, um cabeçalho `Retry-After` também será fornecido com a resposta. O cabeçalho `Retry-After`, medido em segundos, pode ser usado para determinar quando deve ser feita a próxima chamada de sondagem para a API de status de operação.

### <a name="example-of-running-a-status-response"></a>Exemplo de execução de uma resposta de status

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Processando a conclusão da operação

Após a conclusão da operação de execução prolongada, o status da resposta será `"Succeeded"` ou `"Failed"`. Quando um novo recurso for criado por uma operação de execução prolongada, a resposta de êxito retornará um código de status HTTP `201 Created`. A resposta também conterá um cabeçalho `Location` que aponta para os metadados sobre o recurso. Quando nenhum novo recurso for criado, a resposta de êxito retornará um código de status HTTP `200 OK`. Após uma falha, o código de status de resposta também será o código `200 OK`. No entanto, a resposta terá uma propriedade `error` no corpo. Os dados de erro aderem à especificação de erro do OData (Protocolo Open Data).

### <a name="example-of-success-response"></a>Exemplo de uma resposta de êxito

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Exemplo de resposta de falha

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```