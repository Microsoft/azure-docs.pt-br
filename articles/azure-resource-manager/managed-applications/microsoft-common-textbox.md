---
title: Elemento de IU da caixa de texto
description: Descreve o elemento de interface do usuário Microsoft.Common.TextBox para o Portal do Azure. Use para adicionar texto não formatado.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474301"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interface do usuário Microsoft.Common.TextBox

Um controle que pode ser usado para editar texto não formatado.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Esquema

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"contoso123"
```

## <a name="remarks"></a>Comentários

- Se `constraints.required` estiver definido como **true**, a caixa de texto deve ter um valor para validar com êxito. O valor padrão é **false**.
- A `validations` propriedade é uma matriz na qual você adiciona condições para verificar o valor fornecido na caixa de texto.
- A `regex` propriedade é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é **NULL**.
- A `isValid` propriedade contém uma expressão que é avaliada como true ou false. Dentro da expressão, você define a condição que determina se a caixa de texto é válida.
- A `message` propriedade é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha na validação.
- É possível especificar um valor para `regex` quando `required` é definido como **false**. Nesse cenário, um valor não é necessário para a caixa de texto para validar com êxito. Se um valor for especificado, ele deve corresponder ao padrão da expressão regular.
- A `placeholder` propriedade é um texto de ajuda que desaparece quando o usuário começa a edição. Se `placeholder` e `defaultValue` forem definidos, o `defaultValue` terá precedência e será mostrado.

## <a name="example"></a>Exemplo

O exemplo a seguir usa uma caixa de texto com o controle [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) para verificar a disponibilidade de um nome de recurso.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
