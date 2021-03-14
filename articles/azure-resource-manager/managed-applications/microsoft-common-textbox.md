---
title: Elemento de IU da caixa de texto
description: Descreve o elemento de interface do usuário Microsoft.Common.TextBox para o Portal do Azure. Use para adicionar texto não formatado.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124322"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interface do usuário Microsoft.Common.TextBox

Um elemento de interface do usuário que pode ser usado para adicionar texto não formatado. O `Microsoft.Common.TextBox` elemento é um campo de entrada de linha única, mas dá suporte à entrada de várias linhas com a `multiLine` propriedade.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O `TextBox` elemento usa uma caixa de texto de linha única ou de várias linhas.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Caixa de texto de linha única de elemento Microsoft. Common. TextBox.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Caixa de texto de várias linhas do elemento Microsoft. Common. TextBox.":::

## <a name="schema"></a>Esquema

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
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

- Use a `toolTip` propriedade para exibir texto sobre o elemento quando o cursor do mouse estiver focalizado sobre o símbolo de informações.
- A `placeholder` propriedade é um texto de ajuda que desaparece quando o usuário começa a edição. Se `placeholder` e `defaultValue` forem definidos, o `defaultValue` terá precedência e será mostrado.
- A `multiLine` propriedade é booliana `true` ou `false` . Para usar uma caixa de texto de várias linhas, defina a propriedade como `true` . Se uma caixa de texto de várias linhas não for necessária, defina a propriedade como `false` ou exclua a propriedade. Para novas linhas, a saída JSON é mostrada `\n` para o feed de linha. A caixa de texto de várias linhas aceita `\r` um CR (retorno de carro) e `\n` uma alimentação de linha (LF). Por exemplo, um valor padrão pode incluir `\r\n` para especificar CRLF.
- Se `constraints.required` é definido como `true` , a caixa de texto deve ter um valor para validar com êxito. O valor padrão é `false`.
- A `validations` propriedade é uma matriz na qual você adiciona condições para verificar o valor fornecido na caixa de texto.
- A `regex` propriedade é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é `null`. Para obter mais informações sobre a sintaxe RegEx, consulte [referência rápida de expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference).
- A `isValid` propriedade contém uma expressão que é avaliada como `true` ou `false` . Dentro da expressão, você define a condição que determina se a caixa de texto é válida.
- A `message` propriedade é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha na validação.
- É possível especificar um valor para `regex` quando `required` é definido como `false` . Nesse cenário, um valor não é necessário para a caixa de texto para validar com êxito. Se um valor for especificado, ele deve corresponder ao padrão da expressão regular.

## <a name="examples"></a>Exemplos

Os exemplos mostram como usar uma caixa de texto de linha única e uma caixa de texto de várias linhas.

### <a name="single-line"></a>Linha única

O exemplo a seguir usa uma caixa de texto com o controle [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) para verificar a disponibilidade de um nome de recurso.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Várias linhas

Este exemplo usa a `multiLine` propriedade para criar uma caixa de texto de várias linhas com texto de espaço reservado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [CreateUiDefinition.json para a experiência de criação do aplicativo gerenciado do Azure](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
