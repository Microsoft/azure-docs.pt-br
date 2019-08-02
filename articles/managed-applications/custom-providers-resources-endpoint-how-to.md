---
title: Adicionar recursos personalizados à API REST do Azure
description: Saiba como adicionar recursos personalizados à API REST do Azure. Este artigo explica os requisitos e práticas recomendadas para os pontos de extremidade que desejam implementar recursos personalizados.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: a3cd1fe69a0d99f9faf3a451f76a3a420d713711
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795208"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Adicionar recursos personalizados à API REST do Azure

Este artigo irá percorrer os requisitos e práticas recomendadas para a criação de pontos de extremidade de provedor de recursos do Azure personalizado que implementa recursos personalizados. Se você estiver familiarizado com os provedores de recursos personalizado do Azure, consulte [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Como definir um ponto de extremidade de recursos

Uma **ponto de extremidade** é uma URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto de extremidade é definido no provedor de recursos personalizado e pode ser qualquer URL acessível publicamente. O exemplo a seguir tem um **resourceType** chamado `myCustomResource` implementada pelo `endpointURL`.

Amostra **ResourceProvider**:

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

## <a name="building-a-resource-endpoint"></a>Criação de um ponto de extremidade de recursos

Uma **ponto de extremidade** que implementa um **resourceType** deve tratar a solicitação e resposta para a nova API do Azure. Quando um provedor de recursos personalizado com um **resourceType** é criado, ele irá gerar um novo conjunto de APIs no Azure. Nesse caso, o **resourceType** irá gerar um novo recurso do Azure API para `PUT`, `GET`, e `DELETE` para executar o CRUD em um único recurso, bem como `GET` para recuperar todos os recursos existentes:

Manipular o único recurso (`PUT`, `GET`, e `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recuperar todos os recursos (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Para recursos personalizados, provedores de recursos personalizados oferecem dois tipos de **routingTypes**: "`Proxy`"e"`Proxy, Cache`".

### <a name="proxy-routing-type"></a>tipo de roteamento de proxy

O "`Proxy`" **routingType** todos os métodos de solicitação de proxies a **ponto de extremidade** especificado no provedor de recursos personalizado. Quando usar "`Proxy`":

- Controle total sobre a resposta é necessária.
- Integração de sistemas com recursos existentes.

Para saber mais sobre "`Proxy`" os recursos, consulte [a referência de proxy de recurso personalizado](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>tipo de roteamento de cache de proxy

O "`Proxy, Cache`" **routingType** proxies apenas `PUT` e `DELETE` solicitar métodos para o **ponto de extremidade** especificado no provedor de recursos personalizado. O provedor de recursos personalizado retornará automaticamente `GET` solicitações com base no que ele armazenou em seu cache. Se um recurso personalizado está marcado com o cache, o provedor de recursos personalizado também adicionar / substituir campos na resposta para tornar as APIs do Azure em conformidade. Quando usar "`Proxy, Cache`":

- Criando um novo sistema que não tem nenhum recurso existente.
- Trabalhar com o ecossistema do Azure existente.

Para saber mais sobre "`Proxy, Cache`" os recursos, consulte [a referência de cache do recurso personalizado](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Criar um recurso personalizado

Há duas maneiras principais de criação de um recurso personalizado em um provedor de recursos personalizadas:

- CLI do Azure
- Modelos do Azure Resource Manager

### <a name="azure-cli"></a>CLI do Azure

Crie um recurso personalizado:

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

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
is-full-object | *Sim* | Indica que o objeto de propriedades inclui outras opções, como local, marcas, sku e/ou plano.
id | *Sim* | A ID de recurso do recurso personalizado. Isso deve existir fora do **ResourceProvider**
properties | *Sim* | O corpo da solicitação que será enviado para o **ponto de extremidade**.

Exclua um recurso personalizado do Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
id | *Sim* | A ID de recurso do recurso personalizado. Isso deve existir fora do **ResourceProvider**.

Recupere um recurso personalizado do Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
id | *Sim* | A ID de recurso do recurso personalizado. Isso deve existir fora do **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> Recursos exigem que a resposta contenha apropriado `id`, `name`, e `type` da **ponto de extremidade**.

Modelos do Azure Resource Manager requer que `id`, `name`, e `type` são retornados corretamente do ponto de extremidade downstream. Uma resposta do recurso retornada deve estar no formato:

Amostra **ponto de extremidade** resposta:

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

Exemplo modelo do Gerenciador de recursos do Azure:

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

Parâmetro | Obrigatório | DESCRIÇÃO
---|---|---
resourceTypeName | *Sim* | O **nome** da **resourceType** definido no provedor personalizado.
resourceProviderName | *Sim* | O nome de instância do provedor de recurso personalizado.
customResourceName | *Sim* | O nome de recurso personalizado.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral sobre provedores de recursos personalizados do Azure](./custom-providers-overview.md)
- [Início Rápido: Criar provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações personalizadas e os recursos no Azure](./tutorial-custom-providers-101.md)
- [Como: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de Proxy de recurso personalizado](./custom-providers-proxy-resource-endpoint-reference.md)
- [Referência: Referência de Cache do recurso personalizado](./custom-providers-proxy-cache-resource-endpoint-reference.md)
