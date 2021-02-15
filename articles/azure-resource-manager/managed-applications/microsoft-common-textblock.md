---
title: Elemento de interface do usuário do TextBlock
description: Descreve o elemento de interface do usuário Microsoft.Common.TextBlock para o Portal do Azure. Use para adicionar texto à interface.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063869"
---
# <a name="microsoftcommontextblock-ui-element"></a>Elemento de interface do usuário Microsoft.Common.TextBlock

Um controle que pode ser usado para adicionar texto à interface do portal.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
