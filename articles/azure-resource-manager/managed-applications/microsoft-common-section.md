---
title: Elemento de interface do usuário da seção
description: Descreve o elemento de interface do usuário Microsoft.Common.Section para o Portal do Azure. Use o para agrupar elementos no portal para a implantação de aplicativos gerenciados.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063958"
---
# <a name="microsoftcommonsection-ui-element"></a>Elemento de interface do usuário Microsoft.Common.Section

Um controle que agrupa um ou mais elementos em um título.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Comentários

- `elements`deve ter pelo menos um elemento e pode ter todos os tipos de elemento, exceto`Microsoft.Common.Section`.
- Esse elemento não dá suporte à propriedade `toolTip`.

## <a name="sample-output"></a>Saída de exemplo
Para acessar os valores de saída de elementos em `elements`, use as funções [basics()](create-ui-definition-referencing-functions.md#basics) ou [steps()](create-ui-definition-referencing-functions.md#steps) e a notação de ponto:

```json
steps('configuration').section1.text1
```

Elementos do tipo `Microsoft.Common.Section` não têm nenhum valor de saída próprio.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
