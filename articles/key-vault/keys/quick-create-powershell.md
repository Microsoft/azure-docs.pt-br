---
title: Criar e recuperar atributos de uma chave no Azure Key Vault – Azure PowerShell
description: Início Rápido que mostra como definir e recuperar uma chave do Azure Key Vault usando o Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: adbf3080367e54147c981c8ccf0bb6236111b8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071198"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Início Rápido: Definir e recuperar uma chave do Azure Key Vault usando o Azure PowerShell

Neste início rápido, você criará um cofre de chaves no Azure Key Vault com o Azure PowerShell. O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). O Azure PowerShell é usado para criar e gerenciar recursos do Azure usando comandos ou scripts. Depois de concluir isso, você armazenará uma chave.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Digite `$PSVersionTable.PSVersion` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzAccount` para criar uma conexão com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Adicionar uma chave ao Key Vault

Para adicionar uma chave ao cofre, basta executar algumas etapas adicionais. Essa chave pode ser usada por um aplicativo. 

Digite os comandos abaixo para criar um chamado **ExampleKey**:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

Agora, você pode referenciar essa chave que foi adicionada ao Azure Key Vault usando o respectivo URI. Use **"https://<nome-exclusivo-do-cofre-de-chaves>.vault.azure.net/keys/ExampleKey"** para obter a versão atual. 

Para ver a chave armazenada anteriormente:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

Agora, você criou um Key Vault, armazenou uma chave e a recuperou.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira a referência dos [cmdlets do Key Vault do Azure PowerShell](/powershell/module/az.keyvault/)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
