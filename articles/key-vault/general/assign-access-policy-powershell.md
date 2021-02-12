---
title: Atribuir uma política de acesso de Azure Key Vault
description: Como usar o portal do Azure, CLI do Azure ou Azure PowerShell para atribuir uma política de acesso de Key Vault a uma entidade de serviço ou a uma identidade de aplicativo.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 4cf0393cdb0aae1b352b81df4893f874f34970cc
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934519"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Atribuir uma política de acesso de Key Vault usando Azure PowerShell

Uma política de acesso Key Vault determina se uma determinada entidade de serviço, ou seja, um aplicativo ou grupo de usuários, pode executar operações diferentes em Key Vault [segredos](../secrets/index.yml), [chaves](../keys/index.yml)e [certificados](../certificates/index.yml). Você pode atribuir políticas de acesso usando o [portal do Azure](assign-access-policy-portal.md), o [CLI do Azure](assign-access-policy-cli.md)ou Azure PowerShell (este artigo).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para obter mais informações sobre como criar grupos em Azure Active Directory usando Azure PowerShell, consulte [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) e [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Configurar o PowerShell e entrar

1. Para executar comandos localmente, instale [Azure PowerShell](/powershell/azure/) se ainda não tiver feito isso.

    Para executar comandos diretamente na nuvem, use o [Azure cloud Shell](../../cloud-shell/overview.md).

1. Somente PowerShell local:

    1. Instalar o [módulo do Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureAD).

    1. Entrar no Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Adquirir a ID de objeto

Determine a ID de objeto do aplicativo, do grupo ou do usuário ao qual você deseja atribuir a política de acesso:

- Aplicativos e outras entidades de serviço: Use o cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) com o `-SearchString` parâmetro para filtrar os resultados para o nome da entidade de serviço desejada:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Grupos: Use o cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) com o `-SearchString` parâmetro para filtrar os resultados para o nome do grupo desejado:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    Na saída, a ID do objeto é listada como `Id` .

- Usuários: Use o cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) , passando o endereço de email do usuário para o `-UserPrincipalName` parâmetro.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    Na saída, a ID do objeto é listada como `Id` .

## <a name="assign-the-access-policy"></a>Atribuir a política de acesso

Use o cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para atribuir a política de acesso:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Você só precisa incluir `-PermissionsToSecrets` , `-PermissionsToKeys` e `-PermissionsToCertificates` ao atribuir permissões a esses tipos específicos. Os valores permitidos para `<secret-permissions>` , `<key-permissions>` e `<certificate-permissions>` são fornecidos na documentação [set-AzKeyVaultAccessPolicy-Parameters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) .

## <a name="next-steps"></a>Próximas etapas

- [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](security-overview.md#identity-management)
- [Proteja seu cofre de chaves](secure-your-key-vault.md).
- [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)