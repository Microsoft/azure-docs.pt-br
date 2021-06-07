---
title: Elemento de interface do usuário ResourceSelector
description: Descreve o elemento de interface do usuário Microsoft. Solutions. ResourceSelector para portal do Azure. Usado para obter uma lista de recursos existentes.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87095874"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Elemento de interface do usuário Microsoft. Solutions. ResourceSelector

ResourceSelector permite que os usuários selecionem um recurso existente de uma assinatura.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft. Solutions. ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>Esquema

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>Comentários

Na `resourceType` propriedade, forneça o namespace do provedor de recursos e o nome do tipo de recurso para o recurso que você deseja mostrar na lista.

A `filter` propriedade restringe as opções disponíveis para os recursos. Você pode restringir os resultados por localização ou assinatura. Para mostrar apenas os recursos que correspondem à seleção em Noções básicas, use `onBasics` . Para mostrar todos os recursos, use `all` . O valor padrão é `all`.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
