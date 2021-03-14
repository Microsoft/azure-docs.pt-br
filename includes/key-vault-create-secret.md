---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244756"
---
Vamos criar um segredo chamado **mySecret**, com um valor de **Êxito!** . Um segredo pode ser uma senha, uma cadeia de conexão SQL ou qualquer outra informação que você precise manter em segurança e disponível para o aplicativo. 

Para adicionar um segredo ao cofre de chaves recém-criado, use o comando [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) da CLI do Azure:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```