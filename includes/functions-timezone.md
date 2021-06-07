---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026201"
---
O fuso horário padrão usado com as expressões CRON é a Hora Universal Coordenada (UTC). Para que a expressão CRON se baseie em outro fuso horário, crie uma configuração de aplicativo para o aplicativo de funções denominada `WEBSITE_TIME_ZONE`. 

O valor dessa configuração depende do sistema operacional e do plano no qual seu aplicativo de funções é executado.

|Sistema operacional |Plano |Valor |
|-|-|-|
| **Windows** |Tudo | Defina o valor para o nome do fuso horário desejado, conforme fornecido pela segunda linha de cada par fornecido pelo comando do Windows `tzutil.exe /L` |
| **Linux** |Premium<br/>Dedicado |Defina o valor para o nome do fuso horário desejado, conforme mostrado no [banco de dados do TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` Não tem suporte no plano de consumo do Linux no momento.

Por exemplo, o tempo do leste nos EUA (representado pelo `Eastern Standard Time` (Windows) ou `America/New_York` (Linux)) atualmente usa o utc-05:00 durante o horário padrão e o utc-04:00 durante o horário de verão. Para que um gatilho de temporizador seja acionado às 10:00, hora do leste do dia todos os dias, crie uma configuração de aplicativo para seu aplicativo de funções chamado `WEBSITE_TIME_ZONE` , defina o valor para `Eastern Standard Time` (Windows) ou `America/New_York` (Linux) e, em seguida, use a seguinte expressão NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando você usa `WEBSITE_TIME_ZONE` o tempo é ajustado para alterações de hora no fuso horário específico, incluindo o horário de verão e as alterações no horário padrão.
