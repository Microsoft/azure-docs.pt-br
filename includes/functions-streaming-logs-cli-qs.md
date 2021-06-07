---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424569"
---
Execute o seguinte comando para ver os [logs de streaming](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) quase em tempo real:

```console
func azure functionapp logstream <APP_NAME> 
```

Em uma janela de terminal separada ou no navegador, chame a função remota novamente. Um log detalhado da execução da função no Azure é mostrado no terminal. 