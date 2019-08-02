---
title: Referência de cache de recurso personalizado
description: Referência de cache do recurso personalizado para provedores de recursos do Azure personalizado. Este artigo percorrer os requisitos para implementar recursos personalizados de cache de pontos de extremidade.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795238"
---
# <a name="custom-resource-cache-reference"></a>Referência de Cache do recurso personalizado

Este artigo percorrer os requisitos para implementar recursos personalizados de cache de pontos de extremidade. Se você estiver familiarizado com os provedores de recursos personalizado do Azure, consulte [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Como definir um ponto de extremidade de recursos de cache

Um recurso de proxy pode ser criado, especificando o **routingType** para Cache de "Proxy".

Provedor de recursos personalizado de exemplo:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>O ponto de extremidade de recursos de proxy construção

Uma **ponto de extremidade** que implementa um recurso de "Proxy, Cache" **ponto de extremidade** deve tratar a solicitação e resposta para a nova API do Azure. Nesse caso, o **resourceType** irá gerar um novo recurso do Azure API para `PUT`, `GET`, e `DELETE` para executar o CRUD em um único recurso, bem como `GET` para recuperar todos os recursos existentes:

> [!NOTE]
> A API do Azure irá gerar os métodos de solicitação `PUT`, `GET`, e `DELETE`, mas o cache **ponto de extremidade** só precisa lidar com `PUT` e `DELETE`.
> É recomendável que o **ponto de extremidade** também implementa `GET`.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Solicitação de entrada de API do Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Essa solicitação, em seguida, será encaminhada para o **ponto de extremidade** no formulário:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada para o cliente. A resposta do ponto de extremidade deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem estar aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".
- O provedor de recursos personalizado substituirá o `name`, `type`, e `id` campos para a solicitação.
- O provedor de recursos personalizado retornará apenas os campos sob o `properties` objeto para um ponto de extremidade do cache.

**Ponto de extremidade** resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

O `name`, `id`, e `type` campos serão automaticamente gerados para o recurso personalizado pelo provedor de recursos personalizado.

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Remover um recurso personalizado

Solicitação de entrada de API do Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Essa solicitação, em seguida, será encaminhada para o **ponto de extremidade** no formulário:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada para o cliente. A resposta do ponto de extremidade deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem estar aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".
- O provedor de recursos do Azure personalizado só removerá o item do cache se uma resposta de nível 200 será retornada. Mesmo se o recurso não existir, o **ponto de extremidade** deve retornar 204.

**Ponto de extremidade** resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

Solicitação de entrada de API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

A solicitação será **não** ser encaminhado para o **ponto de extremidade**.

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Enumerar todos os recursos personalizados

Solicitação de entrada de API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Essa solicitação será **não** ser encaminhado para o **ponto de extremidade**.

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral sobre provedores de recursos personalizados do Azure](./custom-providers-overview.md)
- [Início Rápido: Criar provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações personalizadas e os recursos no Azure](./tutorial-custom-providers-101.md)
- [Como: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de Proxy de recurso personalizado](./custom-providers-proxy-resource-endpoint-reference.md)
