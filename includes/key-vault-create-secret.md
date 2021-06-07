---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6745d1f7d97a8f4d08078bf93b61762ab04aad46
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073282"
---
Vamos criar um segredo chamado **mySecret**, com um valor de **Êxito!** . Um segredo pode ser uma senha, uma cadeia de conexão SQL ou qualquer outra informação que você precise manter em segurança e disponível para o aplicativo. 

Para adicionar um segredo ao cofre de chaves recém-criado, use o seguinte comando:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
$secret = ConvertTo-SecureString -String 'Success!' -AsPlainText
Set-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret -SecretValue $secret
```
---