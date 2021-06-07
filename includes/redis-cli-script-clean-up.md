---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67171726"
---
## <a name="clean-up-deployment"></a>Limpar a implantação 

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, a instância do Cache do Azure para Redis e todos os recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```