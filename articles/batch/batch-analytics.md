---
title: Análise de lote do Azure | Documentos do Microsoft
description: Referência para análise de lote do Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721548"
---
# <a name="batch-analytics"></a>Análise de lote
Os tópicos na Análise de lote contêm informações de referência para os eventos e alertas disponíveis para recursos de serviço em lotes.

Consulte [Registro em log de diagnóstico de Lote do Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obter mais informações sobre como habilitar e consumir logs de diagnóstico de lote.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

O serviço Lote do Azure emite os eventos de log de diagnóstico a seguir durante o tempo de vida de determinados recursos do lote.

**Eventos do Log de Serviço**
* [Criação de pool](batch-pool-create-event.md)
* [Início de exclusão de pool](batch-pool-delete-start-event.md)
* [Conclusão da exclusão de pool](batch-pool-delete-complete-event.md)
* [Início de redimensionamento de pool](batch-pool-resize-start-event.md)
* [Conclusão de redimensionamento de pool](batch-pool-resize-complete-event.md)
* [Início da tarefa](batch-task-start-event.md)
* [Conclusão da tarefa](batch-task-complete-event.md)
* [Falha da tarefa](batch-task-fail-event.md)