---
title: Gerenciar identidades gerenciadas atribuídas pelo usuário usando REST-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade gerenciada atribuída pelo usuário para fazer chamadas à API REST.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546685"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando chamadas à API REST

Identidades gerenciadas para recursos do Azure fornecem aos serviços do Azure a capacidade de autenticar para serviços que dão suporte à autenticação do Azure AD, sem precisar de credenciais em seu código. 

Neste artigo, você aprenderá como criar, listar e excluir uma identidade gerenciada atribuída ao usuário usando CURL para fazer chamadas à API REST.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Você pode executar todos os comandos deste artigo na nuvem ou localmente:
    - Para executar na nuvem, use o [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Para executar localmente, instale a [rotação](https://curl.haxx.se/download.html) e a [CLI do Azure](/cli/azure/install-azure-cli).

## <a name="obtain-a-bearer-access-token"></a>Obter um token de acesso de portador

1. Se estiver em execução localmente, entre no Azure por meio do CLI do Azure:

    ```
    az login
    ```

1. Obter um token de acesso usando [AZ Account Get-Access-token](/cli/azure/account#az_account_get_access_token)

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário 

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função [Administrador de identidade gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
|*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        |

**Corpo da solicitação**

|Nome  |Descrição  |
|---------|---------|
|local     | Obrigatórios. Local do recurso.        |

## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas ao usuário

Para listar/ler uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Colaborador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
|*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída ao usuário

Para excluir uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída ao usuário não removerá a referência de nenhum recurso ao qual foi atribuída. Para remover uma identidade gerenciada atribuída ao usuário de uma VM usando CURL, consulte [Remover uma identidade atribuída ao usuário de uma VM do Azure](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
|*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        |

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como atribuir uma identidade gerenciada atribuída ao usuário a uma VM/VMSS do Azure usando CURL, consulte, [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando chamadas à API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) e [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando chamadas à API REST](qs-configure-rest-vmss.md#user-assigned-managed-identity).
