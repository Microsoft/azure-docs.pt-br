---
title: incluir arquivo
description: incluir arquivo
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 5f2b77c7d8e1a2da9517183043231b717b6cceab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734005"
---
### <a name="general-query-limits"></a>Limites de consulta geral

| Limite | Descrição |
|:---|:---|
| Idioma de consulta | O Azure Monitor usa a mesma [linguagem de consulta Kusto](/azure/kusto/query/) que o Azure Data Explorer. Confira [Diferenças de linguagem de consulta de log do Azure Monitor](/azure/data-explorer/kusto/query/) para elementos de linguagem KQL sem suporte no Azure Monitor. |
| Regiões do Azure | As consultas de log podem enfrentar sobrecarga excessiva quando os dados abrangem workspaces do Log Analytics em várias regiões do Azure. Confira os [Limites de consulta](../articles/azure-monitor/logs/scope.md#query-scope-limits) para obter detalhes. |
| Consultas entre recursos | Número máximo de recursos do Application Insights e workspaces do Log Analytics em uma consulta limitada a 100.<br>Não há suporte para a consulta entre recursos no Designer de Exibição.<br>A consulta de recursos cruzados nos alertas de log é compatível com a nova API scheduledQueryRules.<br>Confira [Limites de consulta entre recursos](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits) para obter detalhes. |

### <a name="user-query-throttling"></a>Limitação de consulta de usuário
O Azure Monitor tem várias limitações para oferecer proteção contra usuários que enviam um número excessivo de consultas. Esse comportamento pode sobrecarregar os recursos de back-end do sistema e comprometer a capacidade de resposta do serviço. Os limites a seguir foram criados para proteger os clientes contra interrupções e garantir um nível de serviço consistente. A limitação e os limites do usuário foram criados para afetar apenas o cenário de uso extremo e não devem ser relevantes para o uso típico.


| Medida | Limite por usuário | Descrição |
|:---|:---|:---|
| Consultas simultâneas | 5 | Se já houver cinco consultas em execução para o usuário, novas consultas serão colocadas em uma fila de simultaneidade por usuário. Quando uma das consultas em execução terminar, a próxima consulta será retirada da fila e iniciada. Isso não inclui consultas de regras de alerta.
| Tempo na fila de simultaneidade | 3 minutos | Se uma consulta ficar na fila por mais de três minutos sem ser iniciada, ela será encerrada com uma resposta de erro HTTP com o código 429. |
| Total de consultas na fila de simultaneidade | 200 | Depois que o número de consultas na fila atingir 200, todas as consultas adicionais serão rejeitadas com um código de erro HTTP 429. Esse número não inclui as cinco consultas que podem ser executadas simultaneamente. |
| Taxa de consulta | 200 consultas por 30 segundos | Essa é a taxa geral a que as consultas podem ser enviadas por um único usuário para todos os workspaces.  Esse limite se aplica a consultas programáticas ou consultas iniciadas por partes de visualização, como a página de resumo do workspace do Log Analytics e os painéis do Azure. |

- Otimize suas consultas conforme o que está descrito em [Otimizar consultas de log no Azure Monitor](../articles/azure-monitor/logs/query-optimization.md).
- Os painéis e as pastas de trabalho podem conter várias consultas em uma única exibição que geram uma intermitência de consultas sempre que elas são carregadas ou atualizadas. Considere dividi-las em várias exibições que são carregadas sob demanda. 
- No Power BI, considere extrair apenas resultados agregados em vez de logs brutos.