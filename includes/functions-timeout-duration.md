---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172484"
---
## <a name="timeout"></a>Duração de tempo limite do aplicativo de função 

A duração de tempo limite de um aplicativo de funções é definida pela propriedade functionTimeout na [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) arquivo de projeto. A tabela a seguir mostra os valores padrão e máxima em minutos para que ambos os planos em ambas as versões de tempo de execução:

| Plano | Versão de tempo de execução | Padrão | Máximo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2. x | 5 | 10 |
| Serviço de Aplicativo | 1.x | Ilimitado | Ilimitado |
| Serviço de Aplicativo | 2. x | 30 | Ilimitado |

> [!NOTE] 
> Independentemente da configuração de tempo limite do aplicativo de função, 230 segundos é a quantidade máxima de tempo que uma função HTTP disparada pode levar para responder a uma solicitação. Isso é devido a [padrão de tempo limite de ociosidade do balanceador de carga do Azure](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para tempos de processamento, considere o uso de [padrão assíncrono de funções duráveis](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) ou [adiar o trabalho real e retornar uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
