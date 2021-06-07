---
title: Guia de Início Rápido – Definir e recuperar um segredo do Key Vault usando o PowerShell
description: Neste início rápido, saiba como criar, recuperar e excluir segredos de um Azure Key Vault usando o Azure PowerShell.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 5e3b78dfb8f08f1a596ad12884ec088cd36c4a98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582436"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Início Rápido: definir e recuperar um segredo do Azure Key Vault usando o PowerShell

O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). Neste início rápido, você usará o PowerShell para criar um cofre de chaves. Em seguida, você armazenará o segredo no cofre recém-criado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.0.0 ou posterior. Digite `$PSVersionTable.PSVersion` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Dê aos usuários da conta permissões para gerenciar segredos no Key Vault

Use o cmdlet Azure PowerShell Set-AzKeyVaultAccessPolicy para atualizar a política de acesso ao Key Vault e conceder permissões secretas à sua conta de usuário.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Adicionando um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas. Nesse caso, adicione uma senha que pode ser usada por um aplicativo. A senha é chamada **ExamplePassword** e armazena o valor **hVFkk965BuUv** nela.

Primeiro, converta o valor **hVFkk965BuUv** em uma cadeia de caracteres segura digitando:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

Em seguida, use o cmdlet [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) do Azure PowerShell para criar um segredo no Key Vault chamado **ExamplePassword** com o valor **hVFkk965BuUv**:


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Recuperar um segredo do Key Vault

Para exibir o valor contido no segredo como texto sem formatação:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Agora, você criou um Key Vault, armazenou um segredo e o recuperou.

## <a name="clean-up-resources"></a>Limpar os recursos

 Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com outros tutoriais e inícios rápidos, deixe esses recursos onde estão.

Quando não for mais necessário, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o Key Vault e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Saiba como [armazenar segredos de várias linhas no Key Vault](multiline-secrets.md)
- Confira a referência dos [cmdlets do Key Vault do Azure PowerShell](/powershell/module/az.keyvault/#key_vault)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
