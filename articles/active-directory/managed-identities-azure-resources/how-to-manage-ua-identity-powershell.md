---
title: Criar, listar & excluir identidade gerenciada atribuída pelo usuário usando Azure PowerShell-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir identidades gerenciadas atribuídas pelo usuário usando o Azure PowerShell.
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
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548208"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando Azure PowerShell

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando o Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Executar os scripts localmente com o Azure PowerShell, conforme descrito na próxima seção.

### <a name="configure-azure-powershell-locally"></a>Configurar o Azure PowerShell localmente

Para usar o Azure PowerShell localmente para este artigo (em vez de usar o Cloud Shell), realize as seguintes etapas:

1. Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

1. Entrar no Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Talvez seja necessário `Exit` da sessão atual do PowerShell depois de executar esse comando para a próxima etapa.

1. Instale a versão de pré-lançamento do módulo `Az.ManagedServiceIdentity` para executar as operações de identidade gerenciada atribuídas ao usuário neste artigo:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função [Administrador de identidade gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para criar uma identidade gerenciada atribuída pelo usuário, use o comando `New-AzUserAssignedIdentity`. O parâmetro `ResourceGroupName` especifica o grupo de recursos no qual a identidade gerenciada atribuída ao usuário é criada e o parâmetro `-Name` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas ao usuário

Para listar/ler uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Colaborador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para listar as identidades gerenciadas atribuídas pelo usuário, use o comando [Get-AzUserAssigned].  O parâmetro `-ResourceGroupName` especifica o grupo de recursos em que a identidade gerenciada atribuída ao usuário foi criada. Substitua o `<RESOURCE GROUP>` por seu próprio valor:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Na resposta, as identidades gerenciadas atribuídas ao usuário têm `"Microsoft.ManagedIdentity/userAssignedIdentities"` valor retornado para a chave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída ao usuário

Para excluir uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para excluir uma identidade gerenciada atribuída pelo usuário, use o comando `Remove-AzUserAssignedIdentity`.  O parâmetro `-ResourceGroupName` especifica o grupo de recursos em que a identidade atribuída ao usuário foi criada e o parâmetro `-Name` especifica o nome. Substitua os valores de parâmetros `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída ao usuário não removerá a referência de nenhum recurso ao qual foi atribuída. Atribuições de identidade devem ser removidas separadamente.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista completa e mais detalhes sobre as identidades gerenciadas do Azure PowerShell para os comandos de recursos do Azure, confira [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
