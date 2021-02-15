---
title: Variáveis do sistema no Azure Data Factory
description: Este artigo descreve as variáveis do sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir entidades do Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: cb19b7e8c5271e1106e69c98d5bd17a1bcb822bf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385278"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema com suporte pelo Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve as variáveis do sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir entidades do Data Factory.

## <a name="pipeline-scope"></a>Escopo do pipeline
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no pipeline dp JSON.

| Nome da variável | Descrição |
| --- | --- |
| @pipeline().DataFactory |Nome do data factory em que a execução do pipeline está sendo executada |
| @pipeline().Pipeline |Nome do pipeline |
| @pipeline().RunId |ID da execução do pipeline específica |
| @pipeline().TriggerType |O tipo de gatilho que invocou o pipeline (por exemplo `ScheduleTrigger` , `BlobEventsTrigger` ). Para obter uma lista de tipos de gatilho com suporte, consulte [execução de pipeline e gatilhos em Azure data Factory](concepts-pipeline-execution-triggers.md). Um tipo de gatilho `Manual` indica que o pipeline foi disparado manualmente. |
| @pipeline().TriggerId|ID do gatilho que invocou o pipeline |
| @pipeline().TriggerName|Nome do gatilho que invocou o pipeline |
| @pipeline().TriggerTime|Hora da execução do gatilho que invocou o pipeline. Essa é a hora em que o gatilho **realmente** foi disparado para invocar a execução do pipeline e pode diferir ligeiramente do horário agendado do gatilho.  |

>[!NOTE]
>As variáveis do sistema de data/hora relacionadas ao gatilho (em escopos de pipeline e de gatilho) retornam datas UTC no formato ISO 8601, por exemplo, `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Planejar o escopo do gatilho
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no gatilho JSON para gatilhos do tipo [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Nome da variável | Descrição |
| --- | --- |
| @trigger().scheduledTime |Hora em que o gatilho foi agendado para invocar a execução do pipeline. |
| @trigger().startTime |Hora em que o gatilho **realmente** foi acionado para invocar a execução do pipeline. Isso pode ser ligeiramente diferente do horário agendado do gatilho. |

## <a name="tumbling-window-trigger-scope"></a>Escopo de gatilho de janela em cascata
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no gatilho JSON para gatilhos do tipo [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Nome da variável | Descrição |
| --- | --- |
| @trigger().outputs.windowStartTime |Início da janela associada à execução do gatilho. |
| @trigger().outputs.windowEndTime |Fim da janela associada à execução do gatilho. |
| @trigger().scheduledTime |Hora em que o gatilho foi agendado para invocar a execução do pipeline. |
| @trigger().startTime |Hora em que o gatilho **realmente** foi acionado para invocar a execução do pipeline. Isso pode ser ligeiramente diferente do horário agendado do gatilho. |

## <a name="event-based-trigger-scope"></a>Escopo do gatilho baseado em evento
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no gatilho JSON para gatilhos do tipo [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Nome da variável | Descrição |
| --- | --- |
| @triggerBody(). nome de arquivo  |Nome do arquivo cuja criação ou exclusão fez com que o gatilho fosse acionado.   |
| @triggerBody(). nome_da_pasta  |Caminho para a pasta que contém o arquivo especificado por `@triggerBody().fileName` . O primeiro segmento do caminho da pasta é o nome do contêiner de armazenamento de BLOBs do Azure.  |
| @trigger().startTime |Hora em que o gatilho foi acionado para invocar a execução do pipeline. |

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como essas variáveis são usadas em expressões, consulte [Funções e linguagem de expressão](control-flow-expression-language-functions.md).
