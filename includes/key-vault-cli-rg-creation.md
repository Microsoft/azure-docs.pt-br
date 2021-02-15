---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070236"
---
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Use o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos chamado *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
