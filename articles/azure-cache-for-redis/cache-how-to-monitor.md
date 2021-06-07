---
title: Monitorar o cache do Azure para Redis
description: Saiba como monitorar a integridade e o desempenho das instâncias do Cache Redis do Azure
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 0ff11c9601fb55e27d8780185d77c177e9d9201b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584634"
---
# <a name="monitor-azure-cache-for-redis"></a>Monitorar o cache do Azure para Redis

O Cache Redis do Azure usa o [Azure Monitor](../azure-monitor/index.yml) para fornecer várias opções para monitorar suas instâncias de cache. Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Essas ferramentas permitem monitorar a integridade de suas instâncias do Cache do Azure para Redis e ajudá-lo a gerenciar seus aplicativos de cache.

As métricas para instâncias do Cache Redis do Azure são coletadas usando o comando [INFO](https://redis.io/commands/info) do Redis aproximadamente duas vezes por minuto e são armazenadas automaticamente por 30 dias (confira [Exportar métricas de cache](#export-cache-metrics) para configurar uma política de retenção diferente) para que possam ser exibidas nos gráficos de métricas e avaliadas por regras de alerta. Para saber mais sobre os diferentes valores INFO usados para cada métrica de cache, confira [Métricas disponíveis e intervalos de relatórios](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Para exibir as métricas de cache, [procure](cache-configure.md#configure-azure-cache-for-redis-settings) sua instância de cache no [portal do Azure](https://portal.azure.com).  O Cache Redis do Azure fornece alguns gráficos internos na folha **Visão Geral** e na folha **Métricas do Redis**. Cada gráfico pode ser personalizado com a adição ou remoção de métricas e a alteração do intervalo de relatório.

![Seis grafos são mostrados. Um deles é acertos de cache e erros de cache na última hora.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Exibir gráficos de métricas pré-configurados

A folha **Visão geral** tem os seguintes gráficos de monitoramento pré-configurados.

* [Gráficos de monitoramento](#monitoring-charts)
* [Gráficos de uso](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitoramento

A seção **Monitoramento** na folha **Visão geral** tem os gráficos **Acertos e Erros**, **Gets e Sets**, **Conexões** e **Total de Comandos**.

![Gráficos de monitoramento](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gráficos de uso

A seção **Uso** na folha **Visão geral** tem os gráficos **Carga do Servidor Redis**, **Uso de Memória**, **Largura de Banda de Rede** e **Uso de CPU** e também exibe o **Tipo de preço** para a instância de cache.

![Gráficos de uso](./media/cache-how-to-monitor/redis-cache-usage-part.png)

A **Tipo de preço** exibe a tipo de preço do cache e pode ser usada para [dimensionar](cache-how-to-scale.md) o cache para um tipo de preço diferente.

## <a name="view-metrics-with-azure-monitor"></a>Ver métricas com o Azure Monitor

Para exibir as métricas do Redis e criar gráficos personalizados usando o Azure Monitor, clique em **Métricas** no **menu Recursos** e personalize o gráfico usando as métricas desejadas, intervalo de relatórios, o tipo de gráfico e muito mais.

![No painel de navegação à esquerda de contoso55, as métricas são uma opção em monitoramento e são realçadas. Em métricas, há uma lista de métricas. Acertos de cache e erros de cache são selecionados.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Para saber mais sobre como trabalhar com as métricas usando o Azure Monitor, consulte [Visão geral das métricas no Microsoft Azure](../azure-monitor/data-platform.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportar métricas de cache

Por padrão, as métricas de cache no Azure Monitor são [armazenadas durante 30 dias](../azure-monitor/essentials/data-platform-metrics.md) e, em seguida, excluídas. Para persistir suas métricas de cache por mais de 30 dias, [indique uma conta de armazenamento](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) e especifique uma política de **Retenção (dias)** para suas métricas de cache. 

Para configurar uma conta de armazenamento para suas métricas de cache:

1. Na página **cache do Azure para Redis** , no cabeçalho **monitoramento** , selecione **diagnóstico**.
2. Selecione **+ Adicionar configuração de diagnóstico**.
3. Nomeie as configurações.
4. Marque **Arquivar em uma conta de armazenamento**. Você pagará taxas de dados normais por armazenamento e transações ao enviar diagnóstico para uma conta de armazenamento.
4. Selecione **Configurar** para escolher a conta de armazenamento na qual armazenar as métricas de cache.
5. Na **métrica** título da tabela, marque a caixa de seleção ao lado dos itens de linha que você deseja armazenar, como **biométricas**. Especifique uma política de **retenção (dias)** . A retenção máxima de dias que você pode especificar é de **365 dias**. No entanto, se você quiser manter os dados de métricas para sempre, defina a **retenção (dias)** como **0**.
6. Clique em **Save** (Salvar).


![Diagnóstico do Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Além de arquivar suas métricas de cache no armazenamento, você também pode [transmiti-las para um hub de eventos ou enviá-las para Azure monitor logs](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

Para acessar suas métricas, você pode exibi-las no Portal do Azure conforme descrito anteriormente neste artigo, e também pode acessá-las usando a [API REST das métricas do Azure Monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Se você alterar contas de armazenamento, os dados na conta de armazenamento configurada anteriormente permanecerão disponíveis para download, mas não serão exibidos no portal do Azure.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponíveis e intervalos de relatórios

Métricas de cache são relatadas usando vários intervalos de geração de relatórios, incluindo **Última hora**, **Hoje**, **Semana passada**, e **Personalizado**. A lâmina **Métrica** para cada gráfico de métricas exibe os valores médio, mínimos e máximos para cada métrica no gráfico e algumas métricas exibem um total para o intervalo de relatórios. 

Cada métrica inclui duas versões. Uma métrica mede o desempenho de todo o cache, e para os caches que usam [cluster](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` no nome mede o desempenho de um único fragmento em um cache. Por exemplo, se um cache tem quatro fragmentos, `Cache Hits` é o número total de ocorrências para todo o cache e `Cache Hits (Shard 3)` é apenas as ocorrências para esse fragmento do cache.

> [!NOTE]
> Mesmo quando o cache está ocioso sem aplicativos de cliente ativos conectados, você pode ver alguma atividade no cache, como clientes conectados, uso de memória e operações que estão sendo executadas. Essa atividade é normal durante a operação de uma instância do Cache Redis do Azure.
> 
> 

| Métrica | Descrição |
| --- | --- |
| Acertos do Cache |O número de pesquisas de chave com êxito durante o intervalo de relatório especificado. Esse número é mapeado para `keyspace_hits` do comando Redis [info](https://redis.io/commands/info) . |
| Latência do cache (versão prévia) | A latência do cache calculada com base na latência de entre nós do cache. Essa métrica é medida em microssegundos e tem três dimensões: `Avg` , `Min` e `Max` , que representam a latência média, mínima e máxima do cache, respectivamente durante o intervalo de relatório especificado. |
| Erros de Cache |O número de pesquisas de chave com falha durante o intervalo de relatório especificado. Esse número é mapeado para `keyspace_misses` do comando REDIS info. Erros de cache não significam necessariamente que há um problema com o cache. Por exemplo, ao se usar o padrão de programação cache-aside, um aplicativo procura um item no cache primeiro. Se o item não estiver lá (erro de cache), o item será recuperado do banco de dados e adicionado ao cache na próxima vez. Erros de cache são o comportamento normal para o padrão de programação cache-aside. Se o número de erros de cache for maior do que o esperado, examine a lógica do aplicativo que popula e lê do cache. Se os itens estiverem sendo removidos do cache devido à pressão de memória, pode haver alguns erros de cache, mas uma métrica melhor para monitorar a pressão de memória seria `Used Memory` ou `Evicted Keys` . |
| Cache Lido |A quantidade de dados lidos do cache, em MB/s, durante o intervalo de relatório especificado. Esse valor é derivado das placas de adaptador de rede que dão suporte à máquina virtual que hospeda o cache e não é específico do Redis. **Esse valor corresponde à largura de banda de rede usada por esse cache. Se você quiser configurar alertas para limites de largura de banda de rede do lado do servidor, crie-os usando este `Cache Read` contador. Consulte [esta tabela](cache-planning-faq.md#azure-cache-for-redis-performance) para ver os limites de largura de banda observados para vários tamanhos e tipos de preço de cache.** |
| Gravação no Cache |A quantidade de dados gravados no cache, em MB/s, durante o intervalo de relatório especificado. Esse valor é derivado das placas de adaptador de rede que dão suporte à máquina virtual que hospeda o cache e não é específico do Redis. Esse valor corresponde à largura de banda de rede de dados enviados para o cache do cliente. |
| Clientes conectados |O número de conexões de cliente com o cache durante o intervalo de relatório especificado. Esse número é mapeado para `connected_clients` do comando REDIS info. Depois que o [limite de conexão](cache-configure.md#default-redis-server-configuration) for atingido, as tentativas de conexão subsequentes para o cache falharão. Mesmo que não haja nenhum aplicativo cliente ativo, ainda pode haver algumas instâncias de clientes conectados devido a conexões e processos internos. |
| CPU |A utilização da CPU do servidor do Cache Redis do Azure como um percentual durante o intervalo de relatório especificado. Esse valor é mapeado para o contador de desempenho `\Processor(_Total)\% Processor Time` do sistema operacional. |
| Errors | Falhas específicas e problemas de desempenho que o cache pode estar enfrentando durante um intervalo de relatório especificado. Essa métrica tem oito dimensões representando diferentes tipos de erros, mas poderia ter mais adicionadas no futuro. Os tipos de erros representados agora são os seguintes: <br/><ul><li>**Failover** – quando um cache faz failover (subordinado eleva ao primário)</li><li>**Perda** de dados – quando há perda de dado no cache</li><li>**UnresponsiveClients** – quando os clientes não estão lendo dados do servidor com rapidez suficiente</li><li>**AOF** – quando há um problema relacionado à persistência do AOF</li><li>**RDB** – quando há um problema relacionado à persistência do RDB</li><li>**Importação** – quando há um problema relacionado à Importação do RDB</li><li>**Exportação** – quando há um problema relacionado à Exportação do RDB</li></ul> |
| Chaves removidas |O número de itens removidos do cache durante o intervalo de relatório especificado devido ao limite de `maxmemory` . Esse número é mapeado para `evicted_keys` do comando REDIS info. |
| Chaves expiradas |O número de itens expirados do cache durante o intervalo de relatório especificado. Esse valor é mapeado para `expired_keys` do comando INFO do Redis.|
| Gets |O número de operações get do cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores de informações do comando INFO all do Redis: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` e `cmdstat_getrange`. Ele é equivalente à soma de acertos e erros de cache durante o intervalo de relatório. |
| Operações por segundo | O número total de comandos processados por segundo pelo servidor de cache durante o intervalo de relatório especificado.  Este valor mapeia para "instantaneous_ops_per_sec" a partir do comando INFO do Redis. |
| Carga do Servidor Redis |O percentual de ciclos em que o servidor Redis está ocupado processando, em vez de ocioso esperando por mensagens. Se esse contador atingir 100, significa que o servidor Redis atingiu um teto de desempenho e a CPU não pode processar o trabalho mais rapidamente. Se você estiver vendo alta carga do servidor Redis, verá exceções de tempo limite no cliente. Nesse caso, você deve considerar a expansão ou o particionamento de seus dados em vários caches. |
| Conjuntos |O número de operações set para o cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores de informações do comando INFO all do Redis : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` e `cmdstat_setnx`. |
| Total de chaves  | O número máximo de chaves no cache durante o período de relatório anterior. Esse número é mapeado para `keyspace` do comando REDIS info. Devido a uma limitação do sistema subjacente de métricas, para os caches com clustering habilitado, o Total de Chaves retorna o número máximo de chaves do fragmento que tinha o número máximo de chaves durante o intervalo do relatório.  |
| Total de Operações |O número total de comandos processados pelo servidor de cache durante o intervalo de relatório especificado. Esse valor é mapeado para `total_commands_processed` do comando INFO do Redis. Quando o cache do Azure para Redis é usado apenas para pub/sub, não haverá métricas para `Cache Hits` , `Cache Misses` , `Gets` ou `Sets` , mas haverá `Total Operations` métricas que refletem o uso de cache para operações pub/sub. |
| Memória Usada |A quantidade de memória de cache usada para os pares de chave/valor no cache, em MB, durante o intervalo de relatório especificado. Esse valor é mapeado para `used_memory` do comando INFO do Redis. Esse valor não inclui metadados ou fragmentação. |
| Porcentagem de memória utilizada | A % da memória total que está sendo usada durante o intervalo de relatório especificado.  Esse valor faz referência ao `used_memory` valor do comando REDIS info para calcular a porcentagem. |
| Memória RSS Usada |A quantidade de memória de cache usada, em MB, durante o intervalo de relatório especificado, incluindo fragmentação e metadados. Esse valor é mapeado para `used_memory_rss` do comando INFO do Redis. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alertas

É possível configurar para receber alertas com base em métricas e logs de atividades. O Azure Monitor permite configurar um alerta para que ele faça o seguinte quando for acionado:

* Enviar uma notificação por email
* Chamar um webhook
* Invocar um aplicativo lógico do Azure

Para configurar regras de Alerta para seu cache, clique em **Regras de alerta** no **menu Recursos**.

![Monitoramento](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Para saber mais sobre como configurar e usar Alertas, consulte [Visão geral dos alertas](../azure-monitor/alerts/alerts-classic-portal.md).

## <a name="activity-logs"></a>Logs de atividade
Os logs de atividades fornecem insights sobre as operações realizadas nas instâncias do Cache Redis do Azure. Anteriormente eles eram conhecidos como "logs de auditoria" ou "logs operacionais". Usando logs de atividades, é possível determinar "o que, quem e quando" para operações de gravação (PUT, POST, DELETE) realizadas nas instâncias do Cache Redis do Azure. 

> [!NOTE]
> Os logs de atividade não incluem operações de leitura (GET).
>

Para exibir logs de atividade do seu cache, clique em **Logs de atividade** no **menu Recursos**.

Para saber mais sobre como Logs de atividade, consulte [Visão geral dos logs de atividade do Azure](../azure-monitor/essentials/platform-logs-overview.md).