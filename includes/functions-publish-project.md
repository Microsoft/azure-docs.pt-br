---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171707"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Implantar o projeto de aplicativo de funções no Azure

Depois que o aplicativo de funções for criado no Azure, você poderá usar o comando do Core Tools [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) para implantar o código do projeto no Azure. No comando a seguir, substitua `<APP_NAME>` pelo nome do seu aplicativo da etapa anterior.

```bash
func azure functionapp publish <APP_NAME>
```

Você verá a saída semelhante à seguinte, que foi truncada para facilitar a leitura.

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Copie o valor da URL de invocação para seu HttpTrigger, que agora você pode usar para testar sua função no Azure. A URL contém um valor da cadeia de caracteres de consulta `code` que é sua chave de função. Essa chave dificulta para outras pessoas chamarem seu ponto de extremidade de gatilho HTTP no Azure.