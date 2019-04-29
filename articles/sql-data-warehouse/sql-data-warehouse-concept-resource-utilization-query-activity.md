---
title: Capacidade de gerenciamento e monitoramento do SQL Data Warehouse do Azure – atividade de consulta e utilização de recursos | Microsoft Docs
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar o SQL Data Warehouse do Azure. Use o portal do Azure e DMVs (Exibições de Gerenciamento Dinâmico) para entender a atividade de consulta e a utilização de recursos do data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/12/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f80c1817d5c0ce79f2dc53f40a2cc4e00dd5c72b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420959"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorando a atividade de consulta e a utilização de recursos no SQL Data Warehouse do Azure
O SQL Data Warehouse do Azure oferece uma rica experiência de monitoramento no portal do Azure para gerar insights para sua carga de trabalho do data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois ele fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e painéis personalizáveis para métricas e logs. O portal também permite que a integração com outros serviços de monitoramento do Azure, como o Operations Management Suite (OMS) e o Azure Monitor (logs) para fornecer uma experiência holística de monitoramento para não apenas seu data warehouse, mas também sua análise do Azure inteiro plataforma para uma experiência integrada de monitoramento. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise com o SQL Data Warehouse. 

## <a name="resource-utilization"></a>Utilização de recursos 
As seguintes métricas estão disponíveis no portal do Azure para SQL Data Warehouse. Essas métricas são exibidas no [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).

> [!NOTE]
> Atualmente, o nível de nó da CPU e métricas de e/s não refletem corretamente uso do depósito de dados. Essas métricas serão removidas em breve como a equipe melhora o monitoramento e solução de problemas de experiência para o SQL Data Warehouse. 

| Nome da métrica                           | DESCRIÇÃO     | Tipo de agregação |
| --------------------------------------- | ---------------- | --------------------------------------- |
| Percentual de CPU                          | Utilização da CPU em todos os nós para o data warehouse | Máximo      |
| Porcentagem de E/S de dados                      | Utilização de E/S em todos os nós para o data warehouse | Máximo   |
| Conexões bem sucedidas                  | Número de conexões bem-sucedidas com os dados | Total            |
| Conexões com falha                      | Número de conexões com falha com o data warehouse | Total            |
| Bloqueado pelo firewall                     | Número de logons para o data warehouse que foram bloqueados | Total            |
| Limite de DWU                              | Objetivo de nível de serviço do data warehouse | Máximo   |
| Porcentagem de DWU                          | Máximo entre o percentual de CPU e o percentual de E/S de dados | Máximo   |
| DWU usado                                | Limite de DWU * percentual de DWU | Máximo   |
| Percentual de ocorrência no cache | (ocorrências no cache/perda no cache) * 100, em que ocorrências no cache é a soma de todas as ocorrências de segmentos columnstore no cache SSD local e a perda no cache são as perdas de segmentos columnstore no cache SSD local somadas entre todos os nós | Máximo |
| Percentual de cache usado | (cache usado / capacidade de cache) * 100, em que o cache usado é a soma de todos os bytes no cache SSD local entre todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local entre todos os nós | Máximo |
| Porcentagem de local de tempdb | Utilização de tempdb local em todos os nós de computação - os valores são emitidos a cada cinco minutos | Máximo |

## <a name="query-activity"></a>Consultar atividade
Para uma experiência de programação ao monitorar o SQL Data Warehouse por meio do T-SQL, o serviço fornece um conjunto de DMVs (Exibições de Gerenciamento Dinâmico). Essas exibições são úteis ao ativamente resolver problemas e identificar gargalos de desempenho com sua carga de trabalho.

Para exibir a lista de DMVs que o SQL Data Warehouse fornece, consulte esta [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de diagnósticos e métricas
Métricas e logs podem ser exportados para o Azure Monitor, especificamente a [registra em log do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) componente e pode ser acessado programaticamente por meio [consultas de log](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). A latência de log para o SQL Data Warehouse é cerca de 10 a 15 minutos. Para obter mais detalhes sobre os fatores que afetam a latência, visite a documentação a seguir.


## <a name="next-steps"></a>Próximas etapas
Os guias de instruções a seguir descrevem cenários e casos de uso comuns ao monitorar e gerenciar seu data warehouse:

- [Monitorar sua carga de trabalho do data warehouse com DMVs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

