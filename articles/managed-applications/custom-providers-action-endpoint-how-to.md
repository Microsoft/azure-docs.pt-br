---
title: Adicionar ações personalizadas a API REST do Azure
description: Aprenda a adicionar ações personalizadas à API REST do Azure. Este artigo explica os requisitos e práticas recomendadas para os pontos de extremidade que desejam implementar ações personalizadas.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795293"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Adicionar ações personalizadas a API REST do Azure

Este artigo irá percorrer os requisitos e práticas recomendadas para a criação de pontos de extremidade de provedor de recursos personalizado do Azure que implementam ações personalizadas. Se você estiver familiarizado com os provedores de recursos personalizado do Azure, consulte [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Como definir um ponto de extremidade de ação

Uma **ponto de extremidade** é uma URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto de extremidade é definido no provedor de recursos personalizado e pode ser qualquer URL acessível publicamente. O exemplo a seguir tem um **ação** chamado `myCustomAction` implementada pelo `endpointURL`.

Amostra **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>Criação de um ponto de extremidade de ação

Uma **ponto de extremidade** que implementa um **ação** deve tratar a solicitação e resposta para a nova API do Azure. Quando um provedor de recursos personalizado com um **ação** é criado, ele irá gerar um novo conjunto de APIs no Azure. Nesse caso, a ação irá gerar uma nova ação do Azure API para `POST` chamadas:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Solicitação de entrada de API do Azure:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Essa solicitação, em seguida, será encaminhada para o **ponto de extremidade** no formulário:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada para o cliente. A resposta do ponto de extremidade deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem estar aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Chamar uma ação personalizada

Há duas maneiras principais de chamar uma ação personalizada em um provedor de recursos personalizadas:

- CLI do Azure
- Modelos do Azure Resource Manager

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
action | *Sim* | O nome da ação definido na **ResourceProvider**.
ids | *Sim* | A ID de recurso do **ResourceProvider**.
corpo da solicitação | *no* | O corpo da solicitação que será enviado para o **ponto de extremidade**.

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> As ações têm suporte limitado em modelos do Azure Resource Manager. Para a ação a ser chamado dentro de um modelo, ele deve conter o [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) prefixo em seu nome.

Amostra **ResourceProvider** com ação de lista:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Exemplo modelo do Gerenciador de recursos do Azure:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
resourceIdentifier | *Sim* | A ID de recurso do **ResourceProvider**.
apiVersion | *Sim* | A versão de API do tempo de execução do recurso. Isso sempre deve ser "2018-09-01-preview".
functionValues | *no* | O corpo da solicitação que será enviado para o **ponto de extremidade**.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral sobre provedores de recursos personalizados do Azure](./custom-providers-overview.md)
- [Início Rápido: Criar provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações personalizadas e os recursos no Azure](./tutorial-custom-providers-101.md)
- [Como: Adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
