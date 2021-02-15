---
title: Elemento de interface do usuário TagsByResource
description: Descreve o elemento de interface do usuário Microsoft. Common. TagsByResource para portal do Azure. Use para aplicar marcas a um recurso durante a implantação.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: e730201812005a9b469a964e4acd081ebe86b100
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063953"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Elemento de interface do usuário Microsoft. Common. TagsByResource

Um controle para associar [marcas](../management/tag-resources.md) com os recursos em uma implantação.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft-common-tagsbyresource.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Comentários

- Pelo menos um item na `resources` matriz deve ser especificado.
- Cada elemento em `resources` deve ser um tipo de recurso totalmente qualificado. Esses elementos aparecem na lista suspensa de **recursos** e são taggable pelo usuário.
- A saída do controle é formatada para facilitar a atribuição de valores de marca em um modelo de Azure Resource Manager. Para receber a saída do controle em um modelo, inclua um parâmetro em seu modelo, conforme mostrado no exemplo a seguir:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Para cada recurso que pode ser marcado, atribua a propriedade Tags ao valor do parâmetro para esse tipo de recurso:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Use a função [If](../templates/template-functions-logical.md#if) ao acessar o parâmetro tagsByResource. Ele permite que você atribua um objeto vazio quando nenhuma marca é atribuída ao tipo de recurso fornecido.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
