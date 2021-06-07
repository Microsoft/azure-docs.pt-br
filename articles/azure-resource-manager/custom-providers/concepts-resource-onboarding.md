---
title: Integração de recursos
description: Saiba mais sobre como executar a integração de recursos usando os provedores personalizados do Azure para aplicar o gerenciamento ou a configuração a outros tipos de recursos do Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650403"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Visão geral da integração de recursos de provedores personalizados do Azure

A integração de recursos de provedores personalizados do Azure é um modelo de extensibilidade para tipos de recursos do Azure. Ele permite que você aplique operações ou gerenciamento entre recursos existentes do Azure em escala. Para obter mais informações, consulte [como os provedores personalizados do Azure podem estender o Azure](overview.md). Este artigo descreve:

- O que a integração de recursos pode fazer.
- Noções básicas de integração de recursos e como usá-lo.
- Onde encontrar guias e exemplos de código para começar.

> [!IMPORTANT]
> Os provedores personalizados estão atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter recursos restritos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>O que a integração de recursos pode fazer?

Semelhante aos [recursos personalizados dos provedores personalizados do Azure](./custom-providers-resources-endpoint-how-to.md), a integração de recursos define um contrato que fará o proxy de solicitações de "integração" a um ponto de extremidade. Ao contrário dos recursos personalizados, a integração de recursos não cria um novo tipo de recurso. Em vez disso, ele permite a extensão de tipos de recursos existentes. E a integração de recursos funciona com o Azure Policy, portanto, o gerenciamento e a configuração de recursos podem ser feitos em escala. Alguns exemplos de fluxos de trabalho de integração de recursos:

- Instale e gerencie em extensões de máquina virtual.
- Carregar e configurar padrões em contas de armazenamento do Azure.
- Habilite as configurações de diagnóstico de linha de base em escala.

## <a name="resource-onboarding-basics"></a>Noções básicas de integração de recursos

Você configura a integração de recursos por meio de provedores personalizados do Azure usando os tipos de recursos Microsoft. CustomProviders/resourceProviders e Microsoft. CustomProviders/Associations. Para habilitar a integração de recursos para um provedor personalizado, durante o processo de configuração, crie um **ResourceType** chamado "associações" com um **RoutingType** que inclua "extensão". O Microsoft. CustomProviders/Associations e o Microsoft. CustomProviders/resourceProviders não precisam pertencer ao mesmo grupo de recursos.

Veja um exemplo de provedor personalizado do Azure:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Propriedade | Necessário? | Descrição
---|---|---
name | Sim | O nome da definição do ponto de extremidade. Para integração de recursos, o nome deve ser "associações".
routingType | Sim | Determina o tipo de contrato com o ponto de extremidade. Para integração de recursos, os **routingTypes** válidos são "proxy, cache, extensão" e "webhook, cache, extensão".
endpoint | Sim | O ponto de extremidade para o qual rotear as solicitações. Isso tratará a resposta e os efeitos colaterais da solicitação.

Depois de criar o provedor personalizado com o tipo de recurso de associações, você pode direcionar usando Microsoft. CustomProviders/Associations. Microsoft. CustomProviders/Associations é um recurso de extensão que pode estender qualquer outro recurso do Azure. Quando uma instância de Microsoft. CustomProviders/Associations for criada, ela usará uma propriedade **targetResourceId**, que deve ser uma ID de recurso válida Microsoft. CustomProviders/ResourceProviders ou Microsoft. Solutions/Applications. Nesses casos, a solicitação será encaminhada para o tipo de recurso de associações na instância Microsoft. CustomProviders/resourceProviders que você criou.

> [!NOTE]
> Se uma ID de recurso Microsoft. Solutions/Applications for fornecida como o **targetResourceId**, deverá haver um Microsoft. CustomProviders/resourceProviders implantado no grupo de recursos gerenciados com o nome "Public".

Exemplo de associação de provedores personalizados do Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Propriedade | Necessário? | Descrição
---|---|---
targetResourceId | Sim | A ID de recurso do Microsoft. CustomProviders/resourceProviders ou Microsoft. Solutions/Applications.

## <a name="how-to-use-resource-onboarding"></a>Como usar a integração de recursos

A integração de recursos funciona estendendo outros recursos com o recurso de extensão Microsoft. CustomProviders/Associations. No exemplo a seguir, a solicitação é feita para uma máquina virtual, mas qualquer recurso pode ser estendido.

Primeiro, você precisa criar um recurso de provedor personalizado com um tipo de recurso de associações. Isso irá declarar a URL de retorno de chamada que será usada quando um recurso Microsoft. CustomProviders/Associations correspondente for criado, o qual se destina ao provedor personalizado.

Exemplo de solicitação de criação de Microsoft. CustomProviders/resourceProviders:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Depois de criar o provedor personalizado, você pode direcionar outros recursos e aplicar os efeitos colaterais do provedor personalizado a eles.

Exemplo de solicitação de criação de Microsoft. CustomProviders/Associations:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Essa solicitação será então encaminhada para o ponto de extremidade especificado no provedor personalizado que você criou, que é referenciado pelo **targetResourceId** neste formulário:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

O ponto de extremidade deve responder com um aplicativo/JSON `Content-Type` e um corpo de resposta JSON válido. Os campos retornados no objeto de **Propriedades** do JSON serão adicionados à resposta de retorno da associação.

## <a name="getting-help"></a>Obtendo ajuda

Se você tiver dúvidas sobre o desenvolvimento de provedores de recursos personalizados do Azure, tente solicitá-los em [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante já pode ter sido respondida, portanto, verifique-a primeiro antes de postá-la. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre provedores personalizados. Confira estes artigos para saber mais:

- [Tutorial: integração de recursos com provedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Início rápido: criar um provedor de recursos personalizado e implantar recursos personalizados](./create-custom-provider.md)
- [Como adicionar ações personalizadas a uma API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados a uma API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
