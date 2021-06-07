---
title: Gerenciar identidade gerenciada atribuída pelo usuário-CLI do Azure-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando o CLI do Azure.
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 68f305156645d69049519cd383f06b28ab9b5e03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184859"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando o CLI do Azure


Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando o CLI do Azure.

Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com as identidades gerenciadas dos recursos do Azure, confira [O que são as identidades gerenciadas dos recursos do Azure?](overview.md). Para saber mais sobre tipos de identidade gerenciada atribuída pelo sistema e pelo usuário, confira [Tipos de identidade gerenciada](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Para modificar as permissões de usuário ao usar uma entidade de serviço de aplicativo usando a CLI, você deve fornecer as permissões adicionais da entidade de serviço no Azure AD API do Graph como partes da CLI executam solicitações GET em relação ao API do Graph. Caso contrário, você pode acabar recebendo uma mensagem ' privilégios insuficientes para concluir a operação '. Para fazer isso, você precisará entrar no registro do aplicativo em Azure Active Directory, selecionar seu aplicativo, clicar em permissões de API, rolar para baixo e selecionar Azure Active Directory grafo. A partir daí, selecione permissões do aplicativo e, em seguida, adicione as permissões apropriadas. 

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário 

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função [Administrador de identidade gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Use o comando [az identity create](/cli/azure/identity#az-identity-create) para criar uma identidade gerenciada atribuída ao usuário. O parâmetro `-g` especifica o grupo de recursos no qual a identidade gerenciada atribuída ao usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas ao usuário

Para listar/ler uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Colaborador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para listar as identidades gerenciadas atribuídas ao usuário, use o comando [az identity list](/cli/azure/identity#az-identity-list). Substitua o `<RESOURCE GROUP>` por seu próprio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

Na resposta json, as identidades gerenciadas atribuídas ao usuário têm valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` retornado para a chave, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída ao usuário

Para excluir uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para excluir uma identidade gerenciada atribuída ao usuário, use o comando [az identity delete](/cli/azure/identity#az-identity-delete).  O parâmetro -n especifica o nome e o parâmetro -g especifica o grupo de recursos em que a identidade gerenciada atribuída ao usuário foi criada. Substitua os valores de parâmetros `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` pelos seus próprios valores:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída ao usuário não removerá a referência de nenhum recurso ao qual foi atribuída. Remova-as da VM/VMSS usando o comando `az vm/vmss identity remove`

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista completa de comandos de identidade da CLI do Azure, consulte [az identity](/cli/azure/identity).

Para obter informações sobre como atribuir uma identidade gerenciada atribuída ao usuário a uma VM do Azure, consulte, [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
