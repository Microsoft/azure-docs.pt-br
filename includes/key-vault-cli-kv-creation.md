---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070237"
---
Use o comando [az keyvault create](/cli/azure/keyvault#az_keyvault_create) da CLI do Azure para criar um Key Vault no grupo de recursos da etapa anterior. Você precisará fornecer algumas informações:

- Nome do cofre de chaves: uma cadeia de 3 a 24 caracteres contendo somente números (0-9), letras (a-z, A-Z) e hifens (-)

  > [!Important]
  > Cada cofre de chaves deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

A saída deste comando mostra as propriedades do cofre de chaves recém-criado. Anote as duas propriedades listadas abaixo:

- **Nome do cofre**: o nome que você forneceu ao parâmetro --name acima.
- **URI do cofre**: No exemplo, ele é https://&lt;nome-exclusivo-do-cofre-de-chaves&gt;.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.
