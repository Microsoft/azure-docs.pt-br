---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512783"
---
Vamos criar um segredo chamado **mySecret**, com um valor de **Êxito!** . Um segredo pode ser uma senha, uma cadeia de conexão SQL ou qualquer outra informação que você precise manter em segurança e disponível para o aplicativo. 

Para adicionar um segredo ao cofre de chaves recém-criado, use o comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) da CLI do Azure:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```