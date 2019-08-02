---
title: Visão geral dos provedores de recursos personalizados do Azure
description: Saiba mais sobre provedores de recursos personalizado do Azure e como estender o plano de API do Azure de acordo com seus fluxos de trabalho.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796074"
---
# <a name="azure-custom-resource-providers-overview"></a>Visão geral dos provedores de recurso personalizada do Azure

Provedores de recursos personalizado do Azure é uma plataforma de extensibilidade para o Azure. Ele permite que você definir para APIs personalizadas que podem ser usadas para enriquecer o padrão a experiência do Azure. Esta documentação descreve:

- Como criar e implantar um provedor de recursos personalizado do Azure.
- Como utilizar provedores de recursos do Azure personalizado para estender os fluxos de trabalho existentes.
- Onde encontrar os guias e exemplos de código para começar a usar.

![Visão geral do provedor personalizado](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Provedores personalizados está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>O que podem fazer de provedores de recursos personalizados

Aqui estão alguns exemplos de como o que você pode obter com provedores de recursos do Azure personalizado:

- Estenda a REST API do Azure Resource Manager para incluir serviços internos e externos.
- Habilite cenários personalizados na parte superior de fluxos de trabalho existentes do Azure.
- Personalize o controle de modelos do Azure Resource Manager e o efeito.

## <a name="what-is-a-custom-resource-provider"></a>O que é um provedor de recursos personalizado

Provedores de recursos personalizado do Azure são feitos com a criação de um contrato entre o Azure e um ponto de extremidade. Este contrato define uma lista de novos recursos e ações por meio de um novo recurso, **Microsoft.CustomProviders/resourceProviders**. O provedor de recursos personalizado, em seguida, irá expor essas novas APIs no Azure. Provedores de recurso personalizada do Azure são compostos de três partes: o provedor de recursos personalizado **pontos de extremidade**e os recursos personalizados.

## <a name="how-to-build-custom-resource-providers"></a>Como criar provedores de recursos personalizados

Provedores de recursos personalizados são uma lista de contratos entre o Azure e pontos de extremidade. Este contrato descreve como o Azure deve interagir com um ponto de extremidade. O Age de provedor de recursos, como um proxy e para encaminhar solicitações e respostas de e para especificado **ponto de extremidade**. Um provedor de recursos pode especificar dois tipos de contratos: [ **resourceTypes** ](./custom-providers-resources-endpoint-how-to.md) e [ **ações**](./custom-providers-action-endpoint-how-to.md). Esses são habilitados por meio das definições de ponto de extremidade. Uma definição de ponto de extremidade é composta por três campos: **nome**, **routingType**, e **ponto de extremidade**.

Ponto de extremidade de exemplo:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Propriedade | Obrigatório | DESCRIÇÃO
---|---|---
name | *Sim* | O nome da definição do ponto de extremidade. Azure vai expor esse nome por meio de sua API em ' /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Determina o tipo de contrato com o **ponto de extremidade**. Se não for especificado, o padrão será "Proxy".
ponto de extremidade | *Sim* | O ponto de extremidade para encaminhar as solicitações. Isso irá manipular a resposta, bem como os efeitos colaterais da solicitação.

### <a name="building-custom-resources"></a>Criando recursos personalizados

**ResourceTypes** descrevem novos recursos personalizados que são adicionados ao Azure. Eles expõem métodos básicos de CRUD RESTful. Consulte [mais sobre como criar recursos personalizados](./custom-providers-resources-endpoint-how-to.md)

Provedor de recursos personalizado com de exemplo **resourceTypes**:

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
  "location": "eastus"
}
```

APIs adicionadas para o Azure para o exemplo acima:

HttpMethod | URI de exemplo | DESCRIÇÃO
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada de API REST do Azure para criar um novo recurso.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada de API REST do Azure para excluir um recurso existente.
OBTER | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada de API REST do Azure para recuperar um recurso existente.
OBTER | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | A chamada de API REST do Azure para recuperar a lista de recursos existentes.

### <a name="building-custom-actions"></a>Criação de ações personalizadas

**Ações** descrevem novas ações que são adicionadas ao Azure. Eles podem ser expostos sobre o provedor de recursos ou aninhados em uma **resourceType**. Consulte [mais sobre a criação de ações personalizadas](./custom-providers-action-endpoint-how-to.md)

Provedor de recursos personalizado com de exemplo **ações**:

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
  "location": "eastus"
}
```

APIs adicionadas para o Azure para o exemplo acima:

HttpMethod | URI de exemplo | DESCRIÇÃO
---|---|---
POSTAR | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | A chamada de API REST do Azure para ativar a ação.

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver perguntas para o desenvolvimento do provedor de recursos do Azure personalizada, experimente perguntar [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já foram solicitada e respondida, portanto, verifique primeiro antes de lançamento. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre provedores personalizados. Vá para o próximo artigo para criar um provedor personalizado.

- [Início Rápido: Criar provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações personalizadas e os recursos no Azure](./tutorial-custom-providers-101.md)
- [Como: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
