---
title: Criar funções de referência de definição de interface do usuário
description: Descreve as funções a serem usadas ao construir definições de interface do usuário para portal do Azure que fazem referência a outros objetos.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095723"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition referenciando funções

As funções a serem usadas ao referenciar saídas das propriedades ou do contexto de um arquivo CreateUiDefinition.

## <a name="basics"></a>conceitos básicos

Retorna os valores de saída de um elemento que é definido na etapa [básico](create-uidefinition-overview.md#basics) . Passe o nome do elemento como um parâmetro para esta função.

Para obter os valores de saída dos elementos em outras etapas, use a função [Steps ()](#steps) .

O seguinte exemplo retorna a saída do elemento chamado `clusterName` na etapa Conceitos básicos:

```json
"[basics('clusterName')]"
```

Os valores retornados variam de acordo com o tipo de elemento que é recuperado.

## <a name="location"></a>local

Retorna a localização selecionada na etapa Conceitos básicos ou o contexto atual.

O exemplo a seguir retorna um valor como `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Retorna detalhes sobre o resourcegroup selecionado na etapa básico ou no contexto atual.

O exemplo a seguir:

```json
"[resourceGroup()]"
```

Retorna as seguintes propriedades:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

Você pode obter qualquer valor específico com notação de ponto.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>etapas

Retorna os elementos em uma etapa especificada. Passe o nome da etapa como um parâmetro para essa função. Nos elementos retornados, você pode obter valores de propriedade específicos.

Para obter os valores de saída dos elementos na etapa básico, use a função [Basics ()](#basics) .

O exemplo a seguir retorna a etapa chamada `vmParameters` . Nessa etapa, há um elemento chamado `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>subscription

Retorna as propriedades da assinatura selecionada na etapa básico ou no contexto atual.

O exemplo a seguir:

```json
"[subscription()]"
```

Retorna as seguintes propriedades:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao desenvolvimento da interface do portal, consulte [CreateUiDefinition.json para a experiência de criação do aplicativo gerenciado do Azure](create-uidefinition-overview.md).
