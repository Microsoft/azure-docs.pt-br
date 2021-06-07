---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424575"
---
## <a name="deploy-the-function-project-to-azure"></a>Implantar o projeto de funções no Azure

Depois de criar com sucesso o aplicativo de funções no Azure, você estará pronto para implantar um projeto local de funções usando o comando [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment).  

No exemplo a seguir, substitua `<APP_NAME>` pelo nome do aplicativo.

```console
func azure functionapp publish <APP_NAME>
```

O comando de publicação mostra resultados semelhantes à seguinte saída (truncado para fins de simplicidade):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
