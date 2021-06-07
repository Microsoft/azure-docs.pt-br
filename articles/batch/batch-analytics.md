---
title: Análise de Lote do Azure
description: Os tópicos na Análise de lote contêm informações de referência para os eventos e alertas disponíveis para recursos de serviço em lotes.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91849504"
---
# <a name="batch-analytics"></a>Análise de lote

Os tópicos nesta seção contêm informações de referência para os eventos e alertas disponíveis para recursos do serviço de lote.

Consulte [Registro em log de diagnóstico de Lote do Azure](./batch-diagnostics.md) para obter mais informações sobre como habilitar e consumir logs de diagnóstico de lote.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

O serviço Lote do Azure emite os eventos de log de diagnóstico a seguir durante o tempo de vida de determinados recursos do lote.

### <a name="service-log-events"></a>Eventos do Log de Serviço

- [Criação de pool](batch-pool-create-event.md)
- [Início de exclusão de pool](batch-pool-delete-start-event.md)
- [Conclusão da exclusão de pool](batch-pool-delete-complete-event.md)
- [Início de redimensionamento de pool](batch-pool-resize-start-event.md)
- [Conclusão de redimensionamento de pool](batch-pool-resize-complete-event.md)
- [Dimensionamento automático do pool](batch-pool-autoscale-event.md)
- [Início da tarefa](batch-task-start-event.md)
- [Conclusão da tarefa](batch-task-complete-event.md)
- [Falha da tarefa](batch-task-fail-event.md)
- [Falha no agendamento da tarefa](batch-task-schedule-fail-event.md)
