---
title: Elemento de interface do usuário EditableGrid
description: Descreve o elemento de interface do usuário Microsoft. Common. EditableGrid para portal do Azure. Permite que os usuários coletem a entrada tabular.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88893665"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Elemento de interface do usuário Microsoft. Common. EditableGrid

Um controle para coletar a entrada de tabela. Todos os campos dentro da grade são editáveis e o número de linhas pode variar.

## <a name="ui-sample"></a>Exemplo de interface do usuário

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft. Common. EditableGrid":::

## <a name="schema"></a>Esquema

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Comentários

- Os únicos controles válidos dentro da matriz de colunas são a [caixa de texto](microsoft-common-textbox.md), o botão de [Opções](microsoft-common-optionsgroup.md)e a [lista suspensa](microsoft-common-dropdown.md).
- A `$rowIndex` variável só é válida em expressões contidas em filhos das colunas da grade. É um inteiro que representa o índice de linha relativo do elemento e a contagem começa em um e é incrementado em um. Conforme mostrado na seção do esquema `"columns":` , o `$rowIndex` é usado para validação.
- Quando validações são executadas usando a `$rowIndex` variável, é possível obter o valor da linha atual combinando os `last()` `take()` comandos e.

  Por exemplo:

  `last(take(<reference_to_grid>, $rowIndex))`

- A `label` propriedade não aparece como parte do controle, mas é exibida no resumo da guia final.
- A `ariaLabel` propriedade é o rótulo de acessibilidade para a grade. Especifique um texto útil para os usuários que usam leitores de tela.
- A `constraints.width` propriedade é usada para definir a largura geral da grade. As opções são _Full_, _Medium_, _Small_. O padrão é _Full_.
- A `width` propriedade em filhos de colunas determina a largura da coluna. As larguras são especificadas usando unidades fracionárias, como _3fr_, com espaço total alocado para colunas proporcionais às suas unidades. Se nenhuma largura de coluna for especificada, o padrão será _1FR_.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
