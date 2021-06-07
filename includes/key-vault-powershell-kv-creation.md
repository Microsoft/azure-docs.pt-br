---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d19c656946817b06cd620d8a48073bed8299af7d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502242"
---
Use o cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) do Azure PowerShell para criar um Key Vault no grupo de recursos da etapa anterior. Você precisará fornecer algumas informações:

- Nome do cofre de chaves: uma cadeia de 3 a 24 caracteres contendo somente números (0-9), letras (a-z, A-Z) e hifens (-)

  > [!Important]
  > Cada cofre de chaves deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

A saída desse cmdlet mostra as propriedades do cofre de chaves criado recentemente. Anote as duas propriedades listadas abaixo:

- **Nome do cofre**: o nome que você forneceu ao parâmetro --name acima.
- **URI do cofre**: No exemplo, ele é https://&lt;nome-exclusivo-do-cofre-de-chaves&gt;.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.
