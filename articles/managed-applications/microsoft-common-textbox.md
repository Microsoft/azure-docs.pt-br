---
title: Elemento de interface do usuário TextBox UI do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft.Common.TextBox para o Portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: b06e8b49efe8b6de720fa9bb819d4720e4f80fb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61044507"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interface do usuário Microsoft.Common.TextBox
Um controle que pode ser usado para editar texto não formatado.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- Se `constraints.required` estiver definido como **true**, a caixa de texto deve ter um valor para validar com êxito. O valor padrão é **false**.
- `constraints.regex` é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é **null**.
- `constraints.validationMessage` é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha na validação. Se não for especificado, as mensagens de validação internas da caixa de texto serão usadas. O valor padrão é **null**.
- É possível especificar um valor para `constraints.regex` quando `constraints.required` é definido como **false**. Nesse cenário, um valor não é necessário para a caixa de texto para validar com êxito. Se um valor for especificado, ele deve corresponder ao padrão da expressão regular.

## <a name="sample-output"></a>Saída de exemplo

```json
"my text value"
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
