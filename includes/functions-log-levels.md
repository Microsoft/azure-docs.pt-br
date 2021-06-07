---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92164891"
---
Um *nível de log* é atribuído a cada log. O valor é um inteiro que indica a importância relativa:

|LogLevel    |Código| Descrição |
|------------|---|--------------|
|Trace       | 0 |Logs que contêm as mensagens mais detalhadas. Essas mensagens podem conter dados confidenciais do aplicativo. Essas mensagens são desabilitadas por padrão e nunca devem ser habilitadas em um ambiente de produção.|
|Depurar       | 1 | Logs que são usados para investigação interativa durante o desenvolvimento. Esses logs devem conter principalmente informações úteis para depuração e não têm valor de longo prazo. |
|Informações | 2 | Logs que rastreiam o fluxo geral do aplicativo. Esses logs devem ter valor de longo prazo. |
|Aviso     | 3 | Logs que realçam um evento anormal ou inesperado no fluxo do aplicativo, mas não fazem com que a execução do aplicativo pare. |
|Erro       | 4 | Logs que destacam quando o fluxo de execução atual é interrompido devido a uma falha. Esses erros devem indicar uma falha na atividade atual, não uma falha em todo o aplicativo. |
|Crítico    | 5 | Logs que descrevem uma falha irrecuperável do aplicativo ou do sistema ou uma falha catastrófica que exige atenção imediata. |
|Nenhum        | 6 | Desabilita o registro em log para a categoria especificada. |

A [ *host.jsna*](../articles/azure-functions/functions-host-json.md) configuração de arquivo determina quanto log um aplicativo de funções envia para Application insights.  
