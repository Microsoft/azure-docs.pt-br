---
title: Elemento de IU da caixa de seleção
description: Descreve o elemento da interface do usuário Microsoft. Common. CheckBox para portal do Azure. Permite que os usuários selecionem para marcar ou desmarcar uma opção.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87095789"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Elemento de interface do usuário Microsoft. Common. CheckBox

O controle caixa de seleção permite que os usuários marquem ou desmarquem uma opção. O controle retorna **true** quando o controle é marcado ou **falso** quando não marcado.

## <a name="ui-sample"></a>Exemplo de interface do usuário

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. Common. CheckBox":::

## <a name="schema"></a>Esquema

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
true
```

## <a name="remarks"></a>Comentários

Quando você define **Required** como **true**, o usuário deve marcar a caixa de seleção. Se o usuário não marcar a caixa de seleção, a mensagem de validação será exibida.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
