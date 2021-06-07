---
title: Monitoramento do portal de gerenciamento de carga de trabalho
description: Diretrizes para monitoramento do portal de gerenciamento de carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/01/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 702cc1fc8b135b2eb4af9106a81946873918c4fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694386"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics – Monitoramento do portal de gerenciamento de carga de trabalho

Este artigo explica como monitorar a atividade de consulta e a utilização de recursos do [grupo de carga de trabalho](sql-data-warehouse-workload-isolation.md#workload-groups).
Para obter detalhes sobre como configurar o Azure Metrics Explorer, consulte o artigo [Introdução ao Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Consulte a seção [Utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) na documentação de Monitoramento do Azure Synapse Analytics para obter detalhes sobre como monitorar o consumo de recursos do sistema.
Há duas categorias diferentes de métricas de grupo de carga de trabalho fornecidas para monitorar o gerenciamento de carga de trabalho: alocação de recursos e atividade de consulta.  Essas métricas podem ser divididas e filtradas por grupo de carga de trabalho.  As métricas podem ser divididas e filtradas com base em se foram definidas pelo sistema (grupos de carga de trabalho da classe de recurso) ou definidas pelo usuário (criadas pelo usuário com a sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)).

## <a name="workload-management-metric-definitions"></a>Definições de métrica de gerenciamento de carga de trabalho

|Nome da métrica                    |Descrição  |Tipo de agregação |
|-------------------------------|-------------|-----------------|
|Percentual de limite efetivo de recursos | O *Percentual de limite efetivo de recursos* é um limite rígido na porcentagem de recursos acessíveis pelo grupo de carga de trabalho, levando em conta o *Percentual mínimo efetivo de recursos* alocado para outros grupos de carga de trabalho. A métrica de *Percentual de limite efetivo de recursos* é configurada usando o parâmetro `CAP_PERCENTAGE_RESOURCE` na sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  O valor efetivo é descrito aqui.<br><br>Por exemplo, se um grupo de carga de trabalho `DataLoads` for criado com `CAP_PERCENTAGE_RESOURCE` = 100 e outro grupo de carga de trabalho for criado com um percentual mínimo efetivo de 25%, o *Percentual de limite efetivo de recursos* para o grupo de carga de trabalho `DataLoads` será 75%.<br><br>O *Percentual de limite efetivo de recursos* determina o limite superior de simultaneidade (e, portanto, a taxa de transferência potencial) que um grupo de carga de trabalho pode atingir.  Se uma taxa de transferência adicional for necessária além do que é relatado atualmente pela métrica *Percentual de limite efetivo de recursos*, aumente o `CAP_PERCENTAGE_RESOURCE`, diminua o `MIN_PERCENTAGE_RESOURCE` de outros grupos de carga de trabalho ou dimensione a instância para adicionar mais recursos.  A diminuição do `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a simultaneidade, mas pode não aumentar a taxa de transferência geral.| MIN, AVG, MAX |
|Percentual mínimo efetivo de recursos |O *Percentual mínimo efetivo de recursos* é a porcentagem mínima de recursos reservados e isolados para o grupo de carga de trabalho levando em conta o nível de serviço mínimo.  A métrica de Percentual de limite efetivo de recursos é configurada usando o parâmetro `MIN_PERCENTAGE_RESOURCE` na sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  O valor efetivo é descrito [aqui](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values).<br><br>Use o tipo de agregação SUM quando essa métrica não estiver filtrada, nem dividida, para monitorar o isolamento de carga de trabalho total configurado no sistema.<br><br>O *Percentual mínimo efetivo de recursos* determina o limite inferior de simultaneidade garantida (e, portanto, a taxa de transferência garantida) que um grupo de carga de trabalho pode atingir.  Se forem necessários recursos adicionais garantidos além do que é relatado atualmente pela métrica de *Percentual mínimo efetivo de recursos*, aumente o parâmetro `MIN_PERCENTAGE_RESOURCE` configurado para o grupo de carga de trabalho.  A diminuição do `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a simultaneidade, mas pode não aumentar a taxa de transferência geral. |MIN, AVG, MAX|
|Consultas ativas do grupo de carga de trabalho  |Essa métrica relata as consultas ativas dentro do grupo de carga de trabalho.  Usar essa métrica não filtrada e sem divisão exibe todas as consultas ativas em execução no sistema.|SUM         |
|Alocação do grupo de carga de trabalho por limite de porcentagem de recursos |Essa métrica exibe a porcentagem de alocação de recursos em relação ao *Percentual de limite efetivo de recursos* por grupo de carga de trabalho.  Essa métrica fornece a utilização efetiva do grupo de carga de trabalho.<br><br>Considere um grupo de carga de trabalho `DataLoads` com um *Percentual de limite efetivo de recursos* de 75% e um `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurado em 25%.  O valor do *Percentual de alocação do grupo de carga de trabalho por limite de porcentagem de recursos* filtrado para `DataLoads` seria 33% (25%/75%) se uma única consulta estivesse em execução neste grupo de carga de trabalho.<br><br>Use essa métrica para identificar a utilização de um grupo de carga de trabalho.  Um valor próximo a 100% indica que todos os recursos disponíveis para o grupo de carga de trabalho estão sendo usados.  Além disso, a métrica de *Consultas na fila para o grupo de carga de trabalho* para o mesmo grupo de carga de trabalho que mostra um valor maior que zero indicaria que o grupo de carga de trabalho utilizaria recursos adicionais, se alocados.  Por outro lado, se essa métrica estiver consistentemente baixa e a métrica *Consultas ativas do grupo de carga de trabalho* for baixa, o grupo de carga de trabalho não está sendo utilizado.  Essa situação é especialmente problemática se o *Percentual de limite efetivo de recursos* for maior que zero, pois isso indicaria um [isolamento de carga de trabalho subutilizado](#underutilized-workload-isolation).|MIN, AVG, MAX |
|Percentual de alocação do grupo de carga de trabalho por sistema | Essa métrica exibe a alocação de porcentagem de recursos em relação a todo o sistema.<br><br>Considere um grupo de carga de trabalho `DataLoads` com um `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurado em 25%.  O valor do *Percentual de alocação do grupo de carga de trabalho por sistema* filtrado para `DataLoads` seria 25% (25%/100%) se uma única consulta estava em execução neste grupo de carga de trabalho.|MIN, AVG, MAX |
|Tempos limite de consulta do grupo de carga de trabalho |Consultas para o grupo de carga de trabalho que atingiram o tempo limite.  Os tempos limite de consulta relatados por essa métrica ocorrem apenas uma vez que a consulta começou a ser executada (ela não inclui o tempo de espera devido a esperas de bloqueio ou de recursos).<br><br>O tempo limite de consulta é configurado usando o parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` na sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Aumentar o valor pode reduzir o número de tempos limite de consulta.<br><br>Considere aumentar o parâmetro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para o grupo de carga de trabalho para reduzir a quantidade de tempos limite e alocar mais recursos por consulta.  Observe que aumentar `REQUEST_MIN_RESOURCE_GRANT_PERCENT` reduz a quantidade de simultaneidade para o grupo de carga de trabalho. |SUM |
|Consultas na fila do grupo de carga de trabalho | Consultas para o grupo de carga de trabalho que estão atualmente na fila aguardando para iniciar a execução.  As consultas podem ser enfileiradas porque estão aguardando recursos ou bloqueios.<br><br>As consultas podem estar aguardando por vários motivos.  Se o sistema estiver sobrecarregado e a demanda de simultaneidade for maior que o que está disponível, as consultas serão enfileiradas.<br><br>Considere adicionar mais recursos ao grupo de carga de trabalho aumentando o parâmetro `CAP_PERCENTAGE_RESOURCE` na instrução [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Se `CAP_PERCENTAGE_RESOURCE` for maior do que a métrica *Percentual de limite efetivo de recursos*, o isolamento de carga de trabalho configurado para outro grupo de carga de trabalho está afetando os recursos alocados para esse grupo de carga de trabalho.  Considere reduzir o `MIN_PERCENTAGE_RESOURCE` de outros grupos de carga de trabalho ou dimensionar a instância para adicionar mais recursos. |SUM |

## <a name="monitoring-scenarios-and-actions"></a>Monitorar cenários e ações

Veja abaixo uma série de configurações de gráfico para destacar o uso da métrica de gerenciamento de carga de trabalho para solução de problemas, juntamente com as ações associadas para resolver o problema.

### <a name="underutilized-workload-isolation"></a>Isolamento de carga de trabalho subutilizada

Considere o seguinte grupo de carga de trabalho e configuração de classificador em que um grupo de carga de trabalho chamado `wgPriority` é criado e *TheCEO* `membername` é mapeado para ele usando o classificador de carga de trabalho `wcCEOPriority`.  O grupo de carga de trabalho `wgPriority` tem 25% de isolamento de carga de trabalho configurado para ele (`MIN_PERCENTAGE_RESOURCE` = 25).  Cada consulta enviada por *TheCEO* recebe 5% dos recursos do sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

O gráfico abaixo é configurado da seguinte maneira:<br>
Métrica 1: *Percentual mínimo efetivo de recursos* (Agregação AVG, `blue line`)<br>
Métrica 2: *Percentual de alocação do grupo de carga de trabalho por sistema* (Agregação AVG, `purple line`)<br>
Filtro: [Grupo de carga de trabalho] = `wgPriority`<br>
![Captura de tela mostra um gráfico com as duas métricas e o filtro.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
O gráfico mostra que, com o isolamento de carga de trabalho de 25%, somente 10% estão sendo usados em média.  Neste caso, o valor do parâmetro `MIN_PERCENTAGE_RESOURCE` pode ser reduzido para 10 ou 15 e permitir que outras cargas de trabalho no sistema consumam os recursos.

### <a name="workload-group-bottleneck"></a>Gargalo do grupo de carga de trabalho

Considere o seguinte grupo de carga de trabalho e configuração de classificador em que um grupo de carga de trabalho chamado `wgDataAnalyst` é criado e *DataAnalyst* `membername` é mapeado para ele usando o classificador de carga de trabalho `wcDataAnalyst`.  O grupo de carga de trabalho `wgDataAnalyst` tem 6% de isolamento de carga de trabalho configurado para ele (`MIN_PERCENTAGE_RESOURCE` = 6) e um limite de recursos de 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  Cada consulta enviada por *DataAnalyst* recebe 3% dos recursos do sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

O gráfico abaixo é configurado da seguinte maneira:<br>
Métrica 1: *Percentual de limite efetivo de recursos* (Agregação AVG, `blue line`)<br>
Métrica 2: *Alocação do grupo de carga de trabalho por limite de porcentagem de recursos* (Agregação AVG, `purple line`)<br>
Métrica 3: *Consultas na fila do grupo de carga de trabalho* (Agregação SUM, `turquoise line`)<br>
Filtro: [Grupo de carga de trabalho] = `wgDataAnalyst`<br>
![Captura de tela mostra um gráfico com as três métricas e o filtro.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
O gráfico mostra que, com um limite de 9% para os recursos, o grupo de carga de trabalho está com uma utilização de 90%+ (da métrica *Alocação de grupo de carga de trabalho por limite de porcentagem de recursos*).  Há um enfileiramento constante de consultas, conforme mostrado na métrica *Consultas na fila do grupo de carga de trabalho*.  Neste caso, aumentar o `CAP_PERCENTAGE_RESOURCE` para um valor maior que 9% permitirá que mais consultas sejam executadas simultaneamente.  Aumentar o `CAP_PERCENTAGE_RESOURCE` pressupõe que há recursos suficientes disponíveis e não isolados por outros grupos de carga de trabalho.  Verifique o limite aumentado verificando a métrica *Percentual de limite efetivo de recursos*.  Caso deseje mais taxa de transferência, considere também aumentar o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para um valor maior que 3.  Aumentar o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode fazer que as consultas sejam executadas mais rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Configurar isolamento da carga de trabalho com o T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Monitoramento da utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md)
