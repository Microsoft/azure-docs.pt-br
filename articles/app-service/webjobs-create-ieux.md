---
title: Executar tarefas em segundo plano com trabalhos Web
description: Saiba como usar trabalhos Web para executar tarefas em segundo plano no serviço Azure App. Escolha entre uma variedade de formatos de script e execute-os com expressões CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452791"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas em segundo plano com trabalhos Web no serviço Azure App

O conceito de execução de [tarefas em segundo plano](./webjobs-create-ieux-conceptual.md) no Azure é fornecido com Azure app trabalhos Web do serviço. Saiba como implantar <abbr title="Um programa ou script na mesma instância que um aplicativo Web, aplicativo de API ou aplicativo móvel.">WebJobs</abbr> usando o [portal do Azure](https://portal.azure.com) para carregar um executável ou script. 

Três trabalhos Web com suporte incluem:

* **Contínuo**: inicia imediatamente, normalmente em execução em um loop infinito.
* **Agendado**: inicia do gatilho agendado
* **Manual**: inicia do gatilho manual

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> Próximas etapas

* [Saiba mais sobre as tarefas em segundo plano como trabalhos Web](./webjobs-create-ieux-conceptual.md)
* [Exibir histórico de log de trabalhos Web](./webjobs-create-ieux-view-log.md)

* Use o [SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki) Web para simplificar muitas tarefas de programação

* Aprenda a [desenvolver e implantar trabalhos Web com o Visual Studio](webjobs-dotnet-deploy-vs.md)
