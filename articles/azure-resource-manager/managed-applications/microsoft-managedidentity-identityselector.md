---
title: Elemento de interface do usuário IdentitySelector
description: Descreve o elemento de interface do usuário Microsoft. ManagedIdentity. IdentitySelector para portal do Azure. Use para atribuir identidades gerenciadas a um recurso.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063352"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Elemento de interface do usuário Microsoft. ManagedIdentity. IdentitySelector

Um controle para atribuir [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md) para um recurso em uma implantação.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O controle consiste nos seguintes elementos:

![Primeira etapa de Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Quando o usuário seleciona **Adicionar**, o formulário a seguir é aberto. O usuário pode selecionar uma ou mais identidades atribuídas pelo usuário para o recurso.

![Microsoft. ManagedIdentity. IdentitySelector segunda etapa](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

As identidades selecionadas são exibidas na tabela. O usuário pode adicionar ou excluir itens desta tabela.

![Terceira etapa do Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Comentários

- Use **defaultValue.systemAssignedIdentity** para definir um valor inicial para o controle de opções de identidade atribuída pelo sistema. O valor padrão é **off**. Os seguintes valores são permitidos:
  - **Em** – uma identidade atribuída pelo sistema é atribuída ao recurso.
  - **Desativado** – uma identidade atribuída pelo sistema não é atribuída ao recurso.
  - **Somente** -uma identidade atribuída pelo sistema é atribuída ao recurso. Os usuários não podem editar esse valor durante a implantação.
  - **OffOnly** – uma identidade atribuída pelo sistema não é atribuída ao recurso. Os usuários não podem editar esse valor durante a implantação.

- Se **Options. hideSystemAssignedIdentity** for definido como **true**, a interface do usuário para configurar a identidade atribuída ao sistema não será exibida. O valor padrão dessa opção é **false**.
- Se **Options. hideUserAssignedIdentity** for definido como **true**, a interface de usuário para configurar a identidade atribuída ao usuário não será exibida. O recurso não é atribuído a uma identidade atribuída pelo usuário. O valor padrão dessa opção é **false**.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).