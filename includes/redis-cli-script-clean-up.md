---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171726"
---
## <a name="clean-up-deployment"></a>Limpar a implantação 

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, a instância do Cache do Azure para Redis e todos os recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```