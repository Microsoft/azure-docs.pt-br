---
title: Como adicionar recursos personalizados à API REST do Azure
description: Saiba como adicionar recursos personalizados à API REST do Azure. Este artigo abordará os requisitos e as práticas recomendadas para pontos de extremidade que desejam implementar recursos personalizados.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650520"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Adicionando recursos personalizados à API REST do Azure

Este artigo abordará os requisitos e as práticas recomendadas para a criação de pontos de extremidade do provedor de recursos personalizados do Azure que implementam recursos personalizados. Se você não estiver familiarizado com os provedores de recursos personalizados do Azure, consulte [a visão geral sobre provedores de recursos personalizados](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Como definir um ponto de extremidade de recurso

Um **ponto de extremidade** é uma URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto de extremidade é definido no provedor de recursos personalizado e pode ser qualquer URL acessível publicamente. O exemplo a seguir tem um **ResourceType** chamado `myCustomResource` implementado pelo `endpointURL` .

Exemplo de **resourceprovider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Criando um ponto de extremidade de recurso

Um **ponto de extremidade** que implementa um **ResourceType** deve lidar com a solicitação e a resposta para a nova API no Azure. Quando um provedor de recursos personalizado com um **ResourceType** é criado, ele irá gerar um novo conjunto de APIs no Azure. Nesse caso, o **ResourceType** gerará uma nova API de recurso do Azure para `PUT` , `GET` e `DELETE` para executar CRUD em um único recurso, bem como `GET` para recuperar todos os recursos existentes:

Manipular recurso único ( `PUT` , `GET` e `DELETE` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recuperar todos os recursos ( `GET` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Para recursos personalizados, os provedores de recursos personalizados oferecem dois tipos de **routingTypes**: " `Proxy` " e " `Proxy, Cache` ".

### <a name="proxy-routing-type"></a>tipo de roteamento de proxy

Os `Proxy` métodos de solicitação "" proxies de **RoutingType** todos para o **ponto de extremidade** especificado no provedor de recursos personalizado. Quando usar " `Proxy` ":

- O controle total sobre a resposta é necessário.
- Integração de sistemas com recursos existentes.

Para saber mais sobre `Proxy` os recursos "", consulte [a referência de proxy de recurso personalizado](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>tipo de roteamento do cache de proxy

O " `Proxy, Cache` " somente proxies de **RoutingType** `PUT` e `DELETE` métodos de solicitação para o **ponto de extremidade** especificado no provedor de recursos personalizado. O provedor de recursos personalizado retornará `GET` solicitações automaticamente com base no que ele armazenou em seu cache. Se um recurso personalizado for marcado com cache, o provedor de recursos personalizado também adicionará/substituirá campos na resposta para tornar as APIs em conformidade com o Azure. Quando usar " `Proxy, Cache` ":

- Criando um novo sistema que não tem recursos existentes.
- Trabalhe com o ecossistema do Azure existente.

Para saber mais sobre `Proxy, Cache` os recursos "", consulte [a referência de cache de recurso personalizado](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Criando um recurso personalizado

Há duas maneiras principais de criar um recurso personalizado a partir de um provedor de recursos personalizado:

- CLI do Azure
- Modelos do Azure Resource Manager

### <a name="azure-cli"></a>CLI do Azure

Criar um recurso personalizado:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parâmetro | Obrigatório | Descrição
---|---|---
is-full-object | *Ok* | Indica que o objeto de propriedades inclui outras opções, como localização, tags, SKU e/ou plano.
id | *Ok* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora do **resourceprovider**
properties | *Ok* | O corpo da solicitação que será enviado ao **ponto de extremidade**.

Excluir um Recurso Personalizado do Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | Descrição
---|---|---
id | *Ok* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora do **resourceprovider**.

Recuperar um Recurso Personalizado do Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | Descrição
---|---|---
id | *Ok* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora do **resourceprovider**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> Os recursos exigem que a resposta contenha um `id` , `name` e `type` do **ponto de extremidade** apropriado.

Os modelos de Azure Resource Manager exigem que `id` , `name` e `type` sejam retornados corretamente do ponto de extremidade downstream. Uma resposta de recurso retornada deve estar no formato:

Exemplo de resposta de **ponto de extremidade** :

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Modelo de exemplo do Azure Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parâmetro | Obrigatório | Descrição
---|---|---
resourceTypeName | *Ok* | O **nome** do **ResourceType** definido no provedor personalizado.
resourceProviderName | *Ok* | O nome da instância do provedor de recursos personalizado.
customResourceName | *Ok* | O nome do recurso personalizado.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos provedores de recursos personalizados do Azure](overview.md)
- [Início rápido: criar um provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: referência de proxy de recurso personalizado](proxy-resource-endpoint-reference.md)
- [Referência: referência de cache de recurso personalizado](proxy-cache-resource-endpoint-reference.md)
