---
title: Armazenar um segredo multilinha no Azure Key Vault
description: Um tutorial mostrando de que modo definir segredos multilinha do Azure Key Vault usando a CLI do Azure e o PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610190"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Armazenar um segredo multilinha no Azure Key Vault

O [Guia de início rápido da CLI do Azure](quick-create-cli.md) e o [Guia de início rápido do Azure PowerShell](quick-create-powershell.md) demonstram de que modo armazenar um segredo multilinha.   Também é possível usar o Key Vault para armazenar um segredo multilinha, como um arquivo JSON ou uma chave privada RSA.

Os segredos multilinha não podem ser passados para o comando [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) da CLI do Azure nem para o cmdlet [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) do Azure PowerShell por meio da linha de comando. Em vez disso, primeiro será necessário armazenar o segredo multilinha como um arquivo de texto. 

Por exemplo, será possível criar um arquivo de texto chamado "secretfile.txt" contendo as seguintes linhas:

```bash
This is my
multi-line
secret
```

Depois será possível passar esse arquivo para o comando [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) da CLI do Azure usando o parâmetro `--file`.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Primeiro, será necessário ler o arquivo usando o cmdlet [Get-Content](/powershell/module/microsoft.powershell.management/get-content) com o Azure PowerShell. Depois converter o arquivo em uma cadeia de caracteres segura usando o comando [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Por fim, armazene o segredo usando o cmdlet [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret).

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

Nos dois casos, será possível exibir o segredo armazenado usando o comando [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) da CLI do Azure ou o cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) do Azure PowerShell.

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

O segredo será retornado com novas linhas inseridas:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Próximas etapas

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Conferir o [Guia de início rápido da CLI do Azure](quick-create-cli.md)
- Conferir os [Comandos az keyvault da CLI do Azure](/cli/azure/keyvault)
- Conferir o [Guia de início rápido do Azure PowerShell](quick-create-powershell.md)
- Conferir os [Cmdlets Az.KeyVault do Azure PowerShell](/powershell/module/az.keyvault#key-vault)
