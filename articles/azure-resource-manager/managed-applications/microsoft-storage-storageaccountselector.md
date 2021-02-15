---
title: Elemento de interface do usuário StorageAccountSelector
description: Descreve o elemento de interface do usuário Microsoft.Storage.StorageAccountSelector para o Portal do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: fe70c42387e9dedea8943b5dcce04a1f9ded5190
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033268"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Elemento de interface do usuário Microsoft.Storage.StorageAccountSelector

Um controle para selecionar uma conta de armazenamento nova ou existente.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O controle mostra o valor padrão.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft-storage-storageaccountselector.png)

O controle possibilita que o usuário crie uma nova conta de armazenamento ou selecione uma conta de armazenamento existente.

![Microsoft.Storage.StorageAccountSelector new](./media/managed-application-elements/microsoft-storage-storageaccountselector-new.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Comentários

- Se especificado, `defaultValue.name` é validados automaticamente por exclusividade. Se o nome da conta de armazenamento não for exclusivo, o usuário deverá especificar um nome diferente ou escolher uma conta de armazenamento existente.
- O valor padrão para `defaultValue.type` é **Premium_LRS**.
- Os tipos não especificados em `constraints.allowedTypes` ficam ocultos e os tipos não especificados em `constraints.excludedTypes` são mostrados. `constraints.allowedTypes` e `constraints.excludedTypes` são opcionais, mas não podem ser usados simultaneamente.
- Se `options.hideExisting` é **true**, o usuário não pode escolher uma conta de armazenamento existente. O valor padrão é **false**.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
