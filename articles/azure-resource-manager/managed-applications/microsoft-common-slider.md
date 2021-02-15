---
title: Elemento de interface do usuário do slider
description: Descreve o elemento Microsoft. Common. Slider da interface do usuário para portal do Azure. Permite que os usuários definam um valor de uma variedade de opções.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095787"
---
# <a name="microsoftcommonslider-ui-element"></a>Elemento de interface do usuário Microsoft. Common. Slider

O controle deslizante permite que os usuários selecionem um intervalo de valores permitidos.

## <a name="ui-sample"></a>Exemplo de interface do usuário

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft. Common. Slider":::

## <a name="schema"></a>Esquema

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
26
```

## <a name="remarks"></a>Comentários

- Os `min` `max` valores e são obrigatórios. Eles definem os pontos inicial e final do controle deslizante.
- A `showStepMarkers` propriedade assume true como padrão. Os marcadores de etapa são mostrados apenas quando o intervalo de mínimo a máx é de 100 ou menos.


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
