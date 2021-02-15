---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 906413d0a6702e6146779f79d628b5cebf383af1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165751"
---
| | |
|--|--|
|**`<DESTINATION>`**| O destino para o qual os logs são enviados. Os valores válidos são `AppInsights` e `Blob`.<br/>Ao usar `AppInsights` o, verifique se [Application insights está habilitado em seu aplicativo de funções](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Quando você define o destino como `Blob` , os logs são criados em um contêiner de blob nomeado `azure-functions-scale-controller` na conta de armazenamento padrão definida na `AzureWebJobsStorage` configuração do aplicativo. |
|**`<VERBOSITY>`** | Especifica o nível de registro em log. Os valores com suporte são `None`, `Warning` e `Verbose`.<br/>Quando definido como `Verbose` , o controlador de escala registra um motivo para cada alteração na contagem de trabalhadores, bem como informações sobre os gatilhos que fator nessas decisões. Os logs detalhados incluem avisos de gatilho e os hashes usados pelos gatilhos antes e depois que o controlador de escala é executado. |

> [!TIP]
> Tenha em mente que, enquanto você deixa o registro em log do controlador de escala habilitado, ele afeta os [custos potenciais de monitoramento do seu aplicativo de funções](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Considere habilitar o log até que você tenha coletado dados suficientes para entender como o controlador de escala está se comportando e, em seguida, desabilitá-lo.