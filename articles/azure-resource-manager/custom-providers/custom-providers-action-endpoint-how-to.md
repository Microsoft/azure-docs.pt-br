---
title: Como adicionar ações personalizadas à API REST do Azure
description: Saiba como adicionar ações personalizadas à API REST do Azure. Este artigo abordará os requisitos e as práticas recomendadas para pontos de extremidade que desejam implementar ações personalizadas.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650390"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Adicionando ações personalizadas à API REST do Azure

Este artigo abordará os requisitos e as práticas recomendadas para a criação de pontos de extremidade do provedor de recursos personalizados do Azure que implementam ações personalizadas. Se você não estiver familiarizado com os provedores de recursos personalizados do Azure, consulte [a visão geral sobre provedores de recursos personalizados](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Como definir um ponto de extremidade de ação

Um **ponto de extremidade** é uma URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto de extremidade é definido no provedor de recursos personalizado e pode ser qualquer URL acessível publicamente. O exemplo a seguir tem uma **ação** chamada `myCustomAction` implementada pelo `endpointURL` .

Exemplo de **resourceprovider**:

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

## <a name="building-an-action-endpoint"></a>Criando um ponto de extremidade de ação

Um **ponto de extremidade** que implementa uma **ação** deve tratar a solicitação e a resposta para a nova API no Azure. Quando um provedor de recursos personalizado com uma **ação** é criado, ele irá gerar um novo conjunto de APIs no Azure. Nesse caso, a ação irá gerar uma nova API de ação do Azure para `POST` chamadas:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Solicitação de entrada da API do Azure:

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

Essa solicitação será então encaminhada para o **ponto de extremidade** no formulário:

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

Da mesma forma, a resposta do **ponto de extremidade** é então encaminhada de volta para o cliente. A resposta do ponto de extremidade deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e cadeias de caracteres devem ser aninhadas em um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "Application/JSON; charset = utf-8 ".

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

Resposta do provedor de recursos personalizados do Azure:

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

## <a name="calling-a-custom-action"></a>Chamando uma ação personalizada

Há duas maneiras principais de chamar uma ação personalizada a partir de um provedor de recursos personalizado:

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

Parâmetro | Obrigatório | Descrição
---|---|---
ação | *Ok* | O nome da ação definida no **resourceprovider**.
ids | *Ok* | A ID de recurso do **resourceprovider**.
corpo da solicitação | *Não* | O corpo da solicitação que será enviado ao **ponto de extremidade**.

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> As ações têm suporte limitado em modelos de Azure Resource Manager. Para que a ação seja chamada dentro de um modelo, ela deve conter o [`list`](../templates/template-functions-resource.md#list) prefixo em seu nome.

Exemplo de **resourceprovider** com ação de lista:

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

Modelo de exemplo do Azure Resource Manager:

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

Parâmetro | Obrigatório | Descrição
---|---|---
resourceIdentifier | *Ok* | A ID de recurso do **resourceprovider**.
apiVersion | *Ok* | A versão de API do tempo de execução do recurso. Isso deve ser sempre "2018-09-01-Preview".
functionValues | *Não* | O corpo da solicitação que será enviado ao **ponto de extremidade**.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos provedores de recursos personalizados do Azure](overview.md)
- [Início rápido: criar um provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
