---
title: Consultas de log do Azure Monitor | Microsoft Docs
description: Referências a recursos para aprender a escrever consultas de log no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: ecbea91cef69c80975dd105c35f526b357033a6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425955"
---
# <a name="azure-monitor-log-queries"></a>Consultas de log do Azure Monitor
Os logs do Azure Monitor baseiam-se no Azure Data Explorer e as consultas de log do Azure Monitor usam uma versão da mesma linguagem de consulta do Kusto. A [Documentação da linguagem de consulta do Kusto](/azure/kusto/query) tem todos os detalhes da linguagem e deve ser seu principal recurso para escrever consultas de log do Azure Monitor. Esta página fornece links para outros recursos para saber mais sobre como escrever consultas e sobre diferenças na implementação da linguagem no Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Introdução

- [Introdução ao Log Analytics do Azure Monitor](get-started-portal.md) é uma lição para escrever consultas e trabalhar com os resultados no portal do Azure.
- [Introdução às consultas de log do Azure Monitor](get-started-queries.md) é uma lição sobre como escrever consultas usando dados de log do Azure Monitor.

## <a name="concepts"></a>Conceitos
- [Analisar dados de log no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) fornece uma breve visão geral das consultas de log e descreve como os dados de log do Azure Monitor são estruturados.
- [Exibir e analisar dados de log no Azure Monitor](../../azure-monitor/log-query/portals.md) explica os portais nos quais você pode criar e executar consultas de log.

## <a name="reference"></a>Referência

- A [Referência da linguagem de consulta](/azure/kusto/query) é a referência completa da linguagem de consulta do Kusto.
- As [diferenças na linguagem de consulta de log do Azure Monitor](data-explorer-difference.md) descrevem as diferenças entre as versões da linguagem de consulta do Kusto.
- [Propriedades padrão nos registros de log do Azure Monitor](../../azure-monitor/platform/log-standard-properties.md) descreve as propriedades que são padrão para todos os dados de log do Azure Monitor.
- [Executar consultas entre logs de recursos no Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) descreve como escrever consultas de log que usam dados de vários workspaces do Log Analytics e aplicativos do Application Insights.


## <a name="examples"></a>Exemplos

- [Exemplos de consulta de log do Azure Monitor](examples.md) fornece exemplos de consultas usando dados de log do Azure Monitor.



## <a name="lessons"></a>Lições

- [Trabalhar com cadeias de caracteres em consultas de log do Azure Monitor](string-operations.md) descreve como trabalhar com os dados de cadeia de caracteres.
- [Trabalhar com valores de data/hora em consultas de log do Azure Monitor](datetime-operations.md) descreve como trabalhar com os dados de data e hora. 
- [Agregações em consultas de log do Azure Monitor](aggregations.md) e [Agregações avançadas em consultas de log do Azure Monitor](advanced-aggregations.md) descrevem como agregar e resumir dados.
- [Junções em consultas de log do Azure Monitor](joins.md) descreve como unir dados de várias tabelas.
- [Trabalhar com JSON e estruturas de dados em consultas de log do Azure Monitor](json-data-structures.md) descreve como analisar dados JSON.
- [Escrever consultas de log avançadas no Azure Monitor](advanced-query-writing.md) descreve estratégias para criar consultas complexas e reutilizar código.
- [Criar gráficos e diagramas de consultas de log do Azure Monitor](charts.md) descreve como visualizar dados de uma consulta de log.

## <a name="cheatsheets"></a>Folhas de cola

-  [SQL para consultas de log do Azure Monitor](sql-cheatsheet.md) auxilia usuários que já estão familiarizados com o SQL.
-  [Splunk para consultas de log do Azure Monitor](sql-cheatsheet.md) auxilia usuários que já estão familiarizados com o Splunk.
 
## <a name="next-steps"></a>Próximas etapas

- Acesse a [documentação de referência completa da linguagem de consulta do Kusto](/azure/kusto/query/).