---
title: Tipos de problemas de desempenho de consulta no banco de dados SQL do Azure
description: Neste artigo, saiba mais sobre os tipos de problemas de desempenho de consulta no banco de dados SQL do Azure e também saiba como identificar e resolver consultas com esses problemas
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 4d0f5404a64eae99ced0dd797954ba042b50060f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217219"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Tipos detectáveis de gargalos de desempenho de consulta no Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Ao tentar resolver um afunilamento de desempenho, comece determinando se o afunilamento está ocorrendo enquanto a consulta está em estado de execução ou em estado de espera. Resoluções diferentes se aplicam dependendo dessa determinação. Use o diagrama a seguir para ajudar a entender os fatores que podem causar um problema relacionado à execução ou a um problema de espera. Problemas e resoluções relacionadas a cada tipo de problema são discutidos neste artigo.

Você pode usar o banco de dados SQL do Azure [Intelligent insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) ou [DMVs](database/monitoring-with-dmvs.md) de SQL Server para detectar esses tipos de afunilamentos de desempenho.

![Estados da carga de trabalho](./media/identify-query-performance-issues/workload-states.png)

**Problemas relacionados à execução**: problemas relacionados à execução geralmente estão relacionados a problemas de compilação, resultando em um plano de consulta de qualidade inferior ou problemas de execução relacionados a recursos insuficientes ou superutilizados.
**Problemas relacionados à espera**: problemas relacionados à espera geralmente estão relacionados a:

- Bloqueios (bloqueio)
- E/S
- Contenção relacionada ao uso de TempDB
- Esperas de concessão de memória

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemas de compilação resultando em um plano de consulta de qualidade inferior

Um plano de qualidade inferior gerado pelo otimizador de consulta SQL pode ser a causa do desempenho de consulta lento. O otimizador de consulta do SQL pode produzir um plano abaixo do ideal devido a um índice ausente, estatísticas obsoletas, uma estimativa incorreta do número de linhas a serem processadas ou uma estimativa imprecisa da memória necessária. Se você souber que a consulta foi executada mais rapidamente no passado ou em outra instância, compare os planos de execução reais para ver se eles são diferentes.

- Identifique todos os índices ausentes usando um destes métodos:

  - Use [Intelligent insights](database/intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Assistente do banco de dados](database/database-advisor-implement-performance-recommendations.md) para bancos de dados individuais e em pool.
  - DMVs. Este exemplo mostra o impacto de um índice ausente, como detectar os [índices ausentes](database/performance-guidance.md#identifying-and-adding-missing-indexes) usando DMVs e o impacto da implementação da recomendação de índice ausente.
- Tente aplicar [dicas de consulta](/sql/t-sql/queries/hints-transact-sql-query), [atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql)ou [Recompilar índices](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) para obter um plano melhor. Habilite a [correção automática de plano](../azure-sql/database/automatic-tuning-overview.md) no banco de dados SQL do Azure para atenuar esses problemas automaticamente.

  Este [exemplo](database/performance-guidance.md#query-tuning-and-hinting) mostra o impacto de um plano de consulta de qualidade inferior devido a uma consulta parametrizada, como detectar essa condição e como usar uma dica de consulta para resolver.

- Tente alterar o nível de compatibilidade do banco de dados e implementar o processamento inteligente de consultas. O otimizador de consulta SQL pode gerar um plano de consulta diferente dependendo do nível de compatibilidade do banco de dados. Níveis de compatibilidade mais altos fornecem [recursos de processamento de consulta mais inteligentes](/sql/relational-databases/performance/intelligent-query-processing).

  - Para obter mais informações sobre o processamento de consultas, consulte [Guia de arquitetura de processamento de consultas](/sql/relational-databases/query-processing-architecture-guide).
  - Para alterar os níveis de compatibilidade do banco de dados e ler mais sobre as diferenças entre os níveis de compatibilidade, consulte [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Para ler mais sobre a estimativa de cardinalidade, consulte [estimativa de cardinalidade](/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Resolvendo consultas com planos de execução de consulta de qualidade inferior

As seções a seguir discutem como resolver consultas com plano de execução de consulta de qualidade inferior.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Consultas que têm problemas de PSP (plano sensível ao parâmetro)

Um problema de PSP (plano sensível ao parâmetro) ocorre quando o otimizador de consulta gera um plano de execução de consulta que é ideal apenas para um valor de parâmetro específico (ou conjunto de valores) e o plano em cache não é ideal para valores de parâmetro que são usados em execuções consecutivas. Os planos que não são ideais podem causar problemas de desempenho de consulta e degradar a taxa de transferência geral da carga de trabalho.

Para obter mais informações sobre detecção de parâmetros e processamento de consultas, consulte o [Guia de arquitetura de processamento de consultas](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Várias soluções alternativas podem reduzir os problemas de PSP. Cada solução alternativa tem compensações e desvantagens associadas:

- Use a dica de consulta [RECOMPILE](/sql/t-sql/queries/hints-transact-sql-query) em cada execução da consulta. Essa solução negocia o tempo de compilação e aumenta a CPU para melhorar a qualidade do plano. A `RECOMPILE` opção geralmente não é possível para cargas de trabalho que exigem uma alta taxa de transferência.
- Use a dica de consulta [Option (Optimize for...)](/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor do parâmetro real por um valor de parâmetro típico que produz um plano que é bom o suficiente para a maioria das possibilidades de valor de parâmetro. Essa opção requer uma boa compreensão dos valores de parâmetro ideais e características de plano associadas.
- Use a dica de consulta [opção (otimizar para desconhecido)](/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor real do parâmetro e, em vez disso, use a média do vetor de densidade. Você também pode fazer isso capturando os valores de parâmetro de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro dos predicados em vez de usar os próprios parâmetros. Para essa correção, a densidade média deve ser *boa o suficiente*.
- Desabilite a detecção de parâmetros inteiramente usando a dica de consulta [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) .
- Use a dica de consulta [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações no cache. Essa solução alternativa pressupõe que o plano comum bom o suficiente é o já existente no cache. Você também pode desabilitar as atualizações automáticas de estatísticas para reduzir as chances de que o bom plano seja removido e um novo plano incorreto será compilado.
- Force o plano usando explicitamente a dica de consulta [use Plan](/sql/t-sql/queries/hints-transact-sql-query) , reescrevendo a consulta e adicionando a dica no texto da consulta. Ou defina um plano específico usando Repositório de Consultas ou habilitando o [ajuste automático](../azure-sql/database/automatic-tuning-overview.md).
- Substitua o procedimento único por um conjunto aninhado de procedimentos que podem, cada um, ser usado com base em lógica condicional e nos valores de parâmetro associados.
- Crie alternativas de execução de cadeia de caracteres dinâmica para uma definição de procedimento estático.

Para obter mais informações sobre como resolver problemas de PSP, consulte estas Postagens de blog:

- [Eu Smell um parâmetro](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. SQL vs. procedimentos vs. qualidade do plano para consultas parametrizadas](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [Técnicas de otimização de consulta SQL no SQL Server: detecção de parâmetros](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Atividade de compilação causada por parametrização incorreta

Quando uma consulta tem literais, o mecanismo de banco de dados automaticamente parametriza a instrução ou um usuário parametriza explicitamente a instrução para reduzir o número de compilações. Um alto número de compilações para uma consulta usando o mesmo padrão, mas valores literais diferentes podem resultar em alto uso da CPU. Da mesma forma, se você apenas parcialmente parametrizar uma consulta que continua a ter literais, o mecanismo de banco de dados não parametrizará mais a consulta.

Aqui está um exemplo de uma consulta com parâmetros parcialmente:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

Neste exemplo, o `t1.c1` leva `@p1` , mas `t2.c2` continua a pegar o GUID como literal. Nesse caso, se você alterar o valor de `c2` , a consulta será tratada como uma consulta diferente e uma nova compilação ocorrerá. Para reduzir as compilações neste exemplo, você também parametrizaria o GUID.

A consulta a seguir mostra a contagem de consultas por hash de consulta para determinar se uma consulta está parametrizada corretamente:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>Fatores que afetam as alterações do plano de consulta

Uma recompilação do plano de execução de consulta pode resultar em um plano de consulta gerado diferente do plano original em cache. Um plano original existente pode ser recompilado automaticamente por vários motivos:

- As alterações no esquema são referenciadas pela consulta
- As alterações de dados nas tabelas são referenciadas pela consulta
- As opções de contexto de consulta foram alteradas

Um plano compilado pode ser ejetado do cache por vários motivos, como:

- Reinicializações de instância
- Alterações de configuração no escopo do banco de dados
- Demanda de memória
- Solicitações explícitas para limpar o cache

Se você usar uma dica de recompilação, um plano não será armazenado em cache.

Uma recompilação (ou uma nova compilação após a remoção do cache) ainda pode resultar na geração de um plano de execução de consulta que seja idêntico ao original. Quando o plano muda do plano anterior ou original, é provável que essas explicações sejam:

- **Design físico alterado**: por exemplo, índices recém-criados abordam com mais eficiência os requisitos de uma consulta. Os novos índices podem ser usados em uma nova compilação se o otimizador de consulta decidir que usar esse novo índice é mais adequado do que usar a estrutura de dados que foi originalmente selecionada para a primeira versão da execução da consulta. Qualquer alteração física nos objetos referenciados pode resultar em uma nova opção de plano no momento da compilação.

- **Diferenças de recursos de servidor**: quando um plano em um sistema difere do plano em outro sistema, a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar qual plano é gerado. Por exemplo, se um sistema tiver mais processadores, um plano paralelo poderá ser escolhido.

- **Estatísticas diferentes**: as estatísticas associadas aos objetos referenciados podem ter sido alteradas ou podem ser materiais diferentes das estatísticas do sistema original. Se as estatísticas forem alteradas e uma recompilação ocorrer, o otimizador de consulta usará as estatísticas a partir de quando elas forem alteradas. As frequências e as distribuições de dados das estatísticas revisadas podem ser diferentes das da compilação original. Essas alterações são usadas para criar estimativas de cardinalidade. (*Estimativas de cardinalidade* são o número de linhas que devem fluir por meio da árvore de consulta lógica.) As alterações nas estimativas de cardinalidade podem levá-lo a escolher diferentes operadores físicos e ordens associadas de operações. Até mesmo alterações secundárias em estatísticas podem resultar em um plano de execução de consulta alterado.

- **Nível de compatibilidade do banco de dados alterado ou versão do avaliador de cardinalidade**: alterações no nível de compatibilidade do banco de dados podem habilitar novas estratégias e recursos que podem resultar em um plano de execução de consulta diferente. Além do nível de compatibilidade do banco de dados, um sinalizador de rastreamento desabilitado ou habilitado 4199 ou um estado alterado da configuração no escopo do banco de dados QUERY_OPTIMIZER_HOTFIXES também pode influenciar as opções do plano de execução de consulta no momento da compilação. Os sinalizadores de rastreamento 9481 (forçar herança CE) e 2312 (forçar padrão CE) também afetam o plano.

## <a name="resource-limits-issues"></a>Problemas de limites de recursos

O desempenho de consulta lento não relacionado a planos de consulta de qualidade inferior e índices ausentes geralmente está relacionado a recursos insuficientes ou superutilizados. Se o plano de consulta for ideal, a consulta (e o banco de dados) poderá estar atingindo os limites de recurso para o banco de dados, o pool elástico ou a instância gerenciada. Um exemplo pode ser a taxa de transferência de gravação de log em excesso para o nível de serviço.

- Detectando problemas de recursos usando o portal do Azure: para ver se os limites de recursos são o problema, consulte [monitoramento de recursos do banco de dados SQL](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Para bancos de dados individuais e pools elásticos, consulte [Assistente do banco de dados recomendações de desempenho](database/database-advisor-implement-performance-recommendations.md) e [informações de desempenho de consulta](database/query-performance-insight-use.md).
- Detectando limites de recursos usando [Intelligent insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Detectando problemas de recursos usando [DMVs](database/monitoring-with-dmvs.md):

  - A DMV [Sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) retorna CPU, e/s e consumo de memória para o banco de dados. Existe uma linha para cada intervalo de 15 segundos, mesmo que não haja atividade no banco de dados. Dados do histórico são mantidos por uma hora.
  - A DMV [Sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) retorna o uso da CPU e os dados de armazenamento para o Azure SQL Database. Os dados são coletados e agregados em intervalos de cinco minutos.
  - [Muitas consultas individuais que consomem de alta utilização cumulativa](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Se você identificar o problema como recurso insuficiente, poderá atualizar os recursos para aumentar a capacidade de seu banco de dados para absorver os requisitos de CPU. Para obter mais informações, consulte [dimensionar recursos de banco de dados individual no banco de dados SQL do Azure](database/single-database-scale.md) e [dimensionar recursos de pool elástico no banco de dados SQL do Azure](database/elastic-pool-scale.md). Para obter informações sobre como dimensionar uma instância gerenciada, consulte [limites de recursos da camada de serviço](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemas de desempenho causados pelo aumento do volume de carga de trabalho

Um aumento no tráfego do aplicativo e no volume da carga de trabalho pode causar um aumento no uso da CPU. Mas você deve ter cuidado para diagnosticar corretamente esse problema. Quando você vir um problema de alta CPU, responda a essas perguntas para determinar se o aumento é causado por alterações no volume de carga de trabalho:

- As consultas do aplicativo são a causa do problema de alta CPU?
- Para obter as [principais consultas que consomem a CPU que você pode identificar](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Foram associados vários planos de execução à mesma consulta? Em caso afirmativo, por quê?
  - Para consultas com o mesmo plano de execução, os tempos de execução são consistentes? A contagem de execuções aumentou? Nesse caso, o aumento da carga de trabalho provavelmente está causando problemas de desempenho.

Em resumo, se o plano de execução de consulta não foi executado de forma diferente, mas o uso da CPU aumentou junto com a contagem de execução, o problema de desempenho provavelmente está relacionado a um aumento de carga de trabalho.

Nem sempre é fácil identificar uma alteração de volume de carga de trabalho que está gerando um problema de CPU. Considere estes fatores:

- **Uso de recursos alterado**: por exemplo, considere um cenário em que o uso da CPU aumentou para 80% por um longo período de tempo. O uso da CPU sozinho não significa que o volume da carga de trabalho foi alterado. As regressões no plano de execução de consulta e as alterações na distribuição de dados também podem contribuir para mais utilização de recursos, embora o aplicativo execute a mesma carga de trabalho.

- **A aparência de uma nova consulta**: um aplicativo pode direcionar um novo conjunto de consultas em momentos diferentes.

- **Um aumento ou uma diminuição no número de solicitações**: esse cenário é a medida mais óbvia de uma carga de trabalho. O número de consultas nem sempre corresponde a uma maior utilização de recursos. No entanto, essa métrica ainda é um sinal significativo, supondo que outros fatores não sejam alterados.

Use Intelligent Insights para detectar [aumentos de carga de trabalho](database/intelligent-insights-troubleshoot-performance.md#workload-increase) e [planejar regressões](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Problemas relacionados à espera

Depois de ter eliminado um plano de qualidade inferior e problemas *relacionados à espera* relacionados a problemas de execução, o problema de desempenho geralmente é que as consultas estão provavelmente aguardando algum recurso. Problemas relacionados à espera podem ser causados por:

- **Bloqueio**:

  Uma consulta pode manter o bloqueio em objetos no banco de dados enquanto outros tentam acessar os mesmos objetos. Você pode identificar consultas de bloqueio usando [DMVs](database/monitoring-with-dmvs.md#monitoring-blocked-queries) ou [Intelligent insights](database/intelligent-insights-troubleshoot-performance.md#locking). Para obter mais informações, consulte [entender e resolver problemas de bloqueio do SQL do Azure](database/understand-resolve-blocking.md).
- **Problemas de e/s**

  As consultas podem estar aguardando que as páginas sejam gravadas nos arquivos de dados ou de log. Nesse caso, verifique as `INSTANCE_LOG_RATE_GOVERNOR` estatísticas, `WRITE_LOG` ou de `PAGEIOLATCH_*` espera no DMV. Consulte usando DMVs para [identificar problemas de desempenho de e/s](database/monitoring-with-dmvs.md#identify-io-performance-issues).
- **Problemas de TempDB**

  Se a carga de trabalho usar tabelas temporárias ou se houver derramamentos de TempDB nos planos, as consultas poderão ter um problema com a taxa de transferência de TempDB. Consulte usando DMVs para [identificar problemas de tempdb](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemas relacionados à memória**

  Se a carga de trabalho não tiver memória suficiente, a expectativa de vida da página poderá ser descartada ou as consultas poderão ter menos memória do que precisam. Em alguns casos, a inteligência interna no otimizador de consulta corrigirá os problemas relacionados à memória. Consulte usando DMVs para [identificar problemas de concessão de memória](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Métodos para mostrar as principais categorias de espera

Esses métodos são comumente usados para mostrar as principais categorias de tipos de espera:

- Use Intelligent Insights para identificar consultas com degradação de desempenho devido a [maiores esperas](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Use [repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para localizar estatísticas de espera para cada consulta ao longo do tempo. No Repositório de Consultas, aguarde os tipos de espera combinados em categorias de espera. Você pode encontrar o mapeamento de categorias de espera para esperar tipos em [Sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Use [Sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) para retornar informações sobre todas as esperas encontradas por threads executados durante uma operação de consulta. Você pode usar essa exibição agregada para diagnosticar problemas de desempenho com o banco de dados SQL do Azure e também com consultas e lotes específicos. As consultas podem estar aguardando recursos, esperas de fila ou esperas externas.
- Use [Sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para retornar informações sobre a fila de tarefas que estão aguardando algum recurso.

Em cenários de alta CPU, Repositório de Consultas e estatísticas de espera podem não refletir o uso da CPU se:

- As consultas de alto consumo de CPU ainda estão em execução.
- As consultas de alto consumo de CPU estavam sendo executadas quando um failover ocorreu.

DMVs que rastreiam Repositório de Consultas e estatísticas de espera mostram resultados somente para consultas concluídas com êxito e tempo limite. Eles não mostram dados para instruções atualmente em execução até que as instruções sejam concluídas. Use o [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) de exibição de gerenciamento dinâmico para controlar as consultas em execução no momento e a hora de trabalho associada.

> [!TIP]
> Ferramentas adicionais:
>
> - [Esperas e travas do TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [Usp_whatsup TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Próximas etapas

[Visão geral de monitoramento e ajuste do banco de dados SQL](database/monitor-tune-overview.md)