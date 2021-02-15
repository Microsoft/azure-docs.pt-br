---
title: Referência de proxy de recurso personalizado
description: Referência de proxy de recurso personalizado para provedores de recursos personalizados do Azure. Este artigo abordará os requisitos para pontos de extremidade que implementam recursos personalizados de proxy.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650455"
---
# <a name="custom-resource-proxy-reference"></a>Referência de proxy de recurso personalizado

Este artigo abordará os requisitos para pontos de extremidade que implementam recursos personalizados de proxy. Se você não estiver familiarizado com os provedores de recursos personalizados do Azure, consulte [a visão geral sobre provedores de recursos personalizados](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Como definir um ponto de extremidade de recurso de proxy

Um recurso de proxy pode ser criado especificando o **RoutingType** como "proxy".

Provedor de recursos personalizados de exemplo:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
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

## <a name="building-proxy-resource-endpoint"></a>Criando ponto de extremidade de recurso de proxy

Um **ponto de extremidade** que implementa um ponto de **extremidade** de recurso de "proxy" deve manipular a solicitação e a resposta para a nova API no Azure. Nesse caso, o **ResourceType** gerará uma nova API de recurso do Azure para `PUT` , `GET` e `DELETE` para executar CRUD em um único recurso, bem como `GET` para recuperar todos os recursos existentes.

> [!NOTE]
> Os `id` `name` campos, e `type` não são necessários, mas são necessários para integrar o recurso personalizado com o ecossistema do Azure existente.

Recurso de exemplo:

``` JSON
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

Referência de parâmetro:

Propriedade | Amostra | Descrição
---|---|---
name | '{myCustomResourceName}' | O nome do recurso personalizado.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | O namespace do tipo de recurso.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | A ID do recurso.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Solicitação de entrada da API do Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

Essa solicitação será então encaminhada para o **ponto de extremidade** no formulário:

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

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada de volta para o cliente. A resposta do ponto de extremidade deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem ser aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "Application/JSON; charset = utf-8 ".

**Ponto de extremidade** Responde

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

Resposta do provedor de recursos personalizados do Azure:

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

Solicitação de entrada da API do Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Essa solicitação será então encaminhada para o **ponto de extremidade** no formulário:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada de volta para o cliente. A resposta do ponto de extremidade deve retornar:

- Documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem ser aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "Application/JSON; charset = utf-8 ".

**Ponto de extremidade** Responde

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Resposta do provedor de recursos personalizados do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

Solicitação de entrada da API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Essa solicitação será então encaminhada para o **ponto de extremidade** no formulário:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada de volta para o cliente. A resposta do ponto de extremidade deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem ser aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "Application/JSON; charset = utf-8 ".

**Ponto de extremidade** Responde

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

Resposta do provedor de recursos personalizados do Azure:

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

Solicitação de entrada da API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Essa solicitação será então encaminhada para o **ponto de extremidade** no formulário:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada de volta para o cliente. A resposta do ponto de extremidade deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem ser aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "Application/JSON; charset = utf-8 ".
- A lista de recursos deve ser colocada sob a propriedade de nível superior `value` .

**Ponto de extremidade** Responde

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

Resposta do provedor de recursos personalizados do Azure:

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

- [Visão geral dos provedores de recursos personalizados do Azure](overview.md)
- [Início rápido: criar um provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: referência de cache de recurso personalizado](proxy-cache-resource-endpoint-reference.md)
