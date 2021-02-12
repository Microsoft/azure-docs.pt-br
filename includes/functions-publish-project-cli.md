---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
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
