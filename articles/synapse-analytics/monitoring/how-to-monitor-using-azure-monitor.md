---
title: Como monitorar a análise de Synapse usando Azure Monitor
description: Saiba como monitorar seu espaço de trabalho do Synapse Analytics usando Azure Monitor métricas, alertas e logs
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 6861fd7a92c26fad883f14fb430a03b237c90122
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609251"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Use Azure Monitor com seu espaço de trabalho do Azure Synapse Analytics

Os aplicativos de nuvem são complexos e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que seus aplicativos permaneçam em funcionamento em um estado íntegro. Os monitores também ajudam a evitar possíveis problemas e a solucionar problemas anteriores. Você pode usar dados de monitoramento para obter informações detalhadas sobre seus aplicativos. Esse conhecimento ajuda a melhorar o desempenho e a manutenção do aplicativo. Ele também ajuda a automatizar ações que, caso contrário, exigem intervenção manual.

O Azure Monitor fornece métricas, alertas e logs de infraestrutura de nível básico para a maioria dos serviços do Azure. Os logs de diagnóstico do Azure são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. O Azure Synapse Analytics pode gravar logs de diagnóstico em Azure Monitor.

Para saber mais, confira [Visão geral do Azure Monitor](../../azure-monitor/overview.md).

## <a name="metrics"></a>Métricas

Com o monitor, você pode obter visibilidade do desempenho e da integridade de suas cargas de trabalho do Azure. O tipo de dados de monitor mais importante é a métrica, que também é chamada de contador de desempenho. As métricas são emitidas pela maioria dos recursos do Azure. O monitor fornece várias maneiras de configurar e consumir essas métricas para monitoramento e solução de problemas.

Para acessar essas métricas, conclua as instruções em [Azure monitor plataforma de dados](../../azure-monitor/data-platform.md).

### <a name="workspace-level-metrics"></a>Métricas no nível do espaço de trabalho

Aqui estão algumas das métricas emitidas por espaços de trabalho:

| **Métrica**                           | **Categoria de métrica, nome de exibição**                  | **Unidade** | **Tipos de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | Integração, métrica de execuções de atividade                     | Contagem    | Sum (padrão), contagem                | O número total de execuções de atividade ocorridas/encerradas em uma janela de 1 minuto. </br></br> Use a dimensão de resultado dessa métrica para filtrar por estado final com êxito, falha ou cancelado.|
| IntegrationPipelineRunsEnded         | Integração, métrica de execuções de pipeline                     | Contagem    | Sum (padrão), contagem                | O número total de execuções de pipeline ocorridas/encerradas em uma janela de 1 minuto. </br></br> Use a dimensão de resultado dessa métrica para filtrar por estado final com êxito, falha ou cancelado. |
| IntegrationTriggerRunsEnded          | Integração, execução de métricas de gatilho                      | Contagem    | Sum (padrão), contagem                | O número total de execuções de gatilho ocorridas/encerradas em uma janela de 1 minuto. </br></br> Use a dimensão de resultado dessa métrica para filtrar por estado final com êxito, falha ou cancelado. |
| BuiltinSqlPoolDataProcessedBytes     | Pool de SQL interno, dados processados (bytes) | Byte | Sum (padrão) | Quantidade de dados processados pelo pool de SQL sem servidor interno. |
| BuiltinSqlPoolLoginAttempts          | Pool de SQL interno, tentativas de logon | Contagem | Sum (padrão) | Número de tentativas de logon para o pool de SQL sem servidor interno. |
| BuiltinSqlPoolDataRequestsEnded      | Pool do SQL interno, solicitações encerradas (bytes) | Contagem | Sum (padrão) | Número de solicitações SQL encerradas para o pool de SQL sem servidor interno. </br></br> Use a dimensão de resultado dessa métrica para filtrar pelo estado final. |

### <a name="dedicated-sql-pool-metrics"></a>Métricas de pool de SQL dedicada

Aqui estão algumas das métricas emitidas por pools SQL dedicados:

| **Métrica**                           | **Nome de exibição**                  | **Unidade** | **Tipos de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | Limite de DWU                       | Contagem   | Max (padrão), mín., Méd. | Tamanho configurado do pool SQL |
| DWUUsed                             | DWU usado                        | Contagem   | Max (padrão), mín., Méd. | Representa uma representação de uso de alto nível no pool do SQL. Medido por limite de DWU * DWU percentual |
| DWUUsedPercent                      | Percentual de DWU usado             | Porcentagem | Max (padrão), mín., Méd. | Representa uma representação de uso de alto nível no pool do SQL. Medido com o máximo entre o percentual de CPU e a porcentagem de e/s de dados |
| ConnectionsBlockedByFirewall        | Conexões bloqueadas pelo firewall | Contagem   | Sum (padrão)   | Contagem de conexões bloqueadas por regras de firewall. Reveja as políticas de controle de acesso para seu pool SQL e monitore essas conexões se a contagem for alta |
| AdaptiveCacheHitPercent             | Percentual de impacto de cache adaptável   | Porcentagem | Max (padrão), mín., Méd. | Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual de impacto de cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache |
| AdaptiveCacheUsedPercent            | Percentual de cache adaptável usado  | Porcentagem | Max (padrão), mín., Méd. | Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual usada do cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache |
| LocalTempDBUsedPercent              | Percentual de uso do tempdb local    | Porcentagem | Max (padrão), mín., Méd. | Utilização de tempdb local em todos os nós de computação-os valores são emitidos a cada cinco minutos |
| MemoryUsedPercent                   | Porcentagem de utilização da memória          | Porcentagem | Max (padrão), mín., Méd. | Utilização de memória em todos os nós no pool de SQL |
| CPUPercent                          | Percentual de utilização da CPU             | Porcentagem | Max (padrão), mín., Méd. | Utilização da CPU em todos os nós no pool do SQL |
| conexões                         | conexões                     | Contagem   | Sum (padrão)  | Contagem de logons totais para o pool do SQL |
| ActiveQueries                      | Consultas ativas                 | Contagem   | Sum (padrão)   | As consultas ativas. Usar essa métrica não filtrada e a divisão exibe todas as consultas ativas em execução no sistema |
| QueuedQueries                      | Consultas em fila                  | Contagem   | Sum (padrão)   | Contagem cumulativa de solicitações enfileiradas após o limite de simultaneidade máxima ter sido atingido |
| WLGActiveQueries                   | Consultas ativas do grupo de carga de trabalho   | Contagem   | Sum (padrão)   | As consultas ativas no grupo de cargas de trabalho. Usar essa métrica não filtrada e a divisão exibe todas as consultas ativas em execução no sistema |
| WLGActiveQueriesTimeouts           | Tempos limite de consulta do grupo de carga de trabalho   | Contagem   | Sum (padrão)   | Consultas para o grupo de cargas de trabalho que atingiram o tempo limite. Os tempos limite de consulta relatados por essa métrica são apenas uma vez que a consulta começou a ser executada (ela não inclui o tempo de espera devido a esperas de bloqueio ou de recursos) |
| WLGQueuedQueries                   | Consultas na fila do grupo de carga de trabalho   | Contagem   | Sum (padrão)   | Contagem cumulativa de solicitações enfileiradas após o limite de simultaneidade máxima ter sido atingido |
| WLGAllocationBySystemPercent        | Percentual de alocação do grupo de carga de trabalho por sistema | Porcentagem | Max (padrão), min, AVG, Sum | A alocação percentual de recursos em relação a todo o sistema |
| WLGAllocationByEffectiveCapResourcePercent   | Alocação do grupo de carga de trabalho por porcentagem máxima de recursos | Porcentagem | Max (padrão), mín., Méd. | Exibe a porcentagem de alocação de recursos em relação à porcentagem de recursos de limite efetivo por grupo de carga de trabalho. Essa métrica fornece a utilização efetiva do grupo de cargas de trabalho |
| WLGEffectiveCapResourcePercent      | Percentual de limite efetivo de recursos  | Porcentagem | Max (padrão), mín., Méd. | A porcentagem do recurso Cap efetivo para o grupo de carga de trabalho. Se houver outros grupos de cargas de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource será reduzido proporcionalmente |
| WLGEffectiveMinResourcePercent      | Percentual mínimo efetivo de recursos  | Porcentagem | Max (padrão), min, AVG, Sum | A configuração de porcentagem mínima de recursos efetiva permitida Considerando o nível de serviço e as configurações do grupo de carga de trabalho. Os min_percentage_resource efetivos podem ser ajustados em níveis mais baixos de serviço |

### <a name="apache-spark-pool-metrics"></a>Métricas do pool de Apache Spark

Aqui estão algumas das métricas emitidas por pools de Apache Spark:

| **Métrica**                           | **Categoria de métrica, nome de exibição**                  | **Unidade** | **Tipos de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Apache Spark aplicativos encerrados  | Contagem | Sum (padrão) | Número de aplicativos do pool de Apache Spark encerrados |
| BigDataPoolAllocatedCores     | Número de vCores alocadas para o pool de Apache Spark                 | Contagem | Max (padrão), mín., Méd. | VCores alocada para um pool de Apache Spark |
| BigDataPoolAllocatedMemory    | Quantidade de memória (GB) alocada para o pool de Apache Spark            | Contagem | Max (padrão), mín., Méd. | Memória alocada para o pool de Apache Spark (GB) |
| BigDataPoolApplicationsActive | Aplicativos do Active Apache Spark | Contagem | Max (padrão), mín., Méd. | Número de aplicativos do pool de Apache Spark ativos |

## <a name="alerts"></a>Alertas

Entre no portal do Azure e selecione **monitorar**  >  **alertas** para criar alertas.

### <a name="create-alerts"></a>Criar alertas

1. Selecione **+ Nova regra de alerta** para criar um novo alerta.

1. Defina a **condição de alerta** para especificar quando o alerta deve ser acionado.

    > [!NOTE]
    > Certifique-se de selecionar **tudo** na lista suspensa **Filtrar por tipo de recurso** .

1. Defina os **detalhes do alerta** para especificar ainda mais como o alerta deve ser configurado.

1. Defina o **grupo de ações** para determinar quem deve receber os alertas (e como).

## <a name="logs"></a>Logs

### <a name="workspace-level-logs"></a>Logs de nível de espaço de trabalho

Estes são os logs emitidos pelos espaços de trabalho do Azure Synapse Analytics:

| Nome da tabela de Log Analytics | Nome da categoria de log                 | Descrição |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Solicitações da API do gateway Synapse do Azure. |
| SynapseRbacOperations         | SynapseRbacOperations          | Operações de SRBAC (controle de acesso baseado em função) do Azure Synapse. |

### <a name="dedicated-sql-pool-logs"></a>Logs de pool do SQL dedicados

Estes são os logs emitidos por pools SQL dedicados:

| Nome da tabela de Log Analytics        | Nome da categoria de log             | Descrição |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Informações sobre solicitações/consultas SQL em um pool SQL dedicado do Azure Synapse.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Informações sobre trabalhadores concluindo as etapas de DMS em um pool SQL dedicado do Azure Synapse.
| SynapseSqlPoolRequestSteps  | RequestSteps | Informações sobre as etapas de solicitação que compõem uma determinada solicitação/consulta SQL em um pool SQL dedicado do Azure Synapse.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Informações sobre distribuições de consulta das etapas de solicitações/consultas SQL em um pool SQL dedicado do Azure Synapse.
| SynapseSqlPoolWaits         | Esperas        | Informações sobre os Estados de espera encontrados durante a execução de uma solicitação/consulta SQL em um pool SQL dedicado do Azure Synapse, incluindo bloqueios e esperas em filas de transmissão.

Para obter mais informações sobre esses logs, consulte as seguintes informações:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Log do pool de Apache Spark

Este é o log emitido por pools de Apache Spark:

| Nome da tabela de Log Analytics               | Nome da categoria de log              | Descrição                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Informações sobre os aplicativos Apache Spark finalizados |

### <a name="diagnostic-settings"></a>Configurações de Diagnóstico

Use as configurações de diagnóstico para configurar os logs de diagnóstico para recursos que não são de computação. As configurações de um controle de recurso têm os seguintes recursos:

* Eles especificam onde os logs de diagnóstico são enviados. Os exemplos incluem uma conta de armazenamento do Azure, um hub de eventos do Azure ou logs de monitor.
* Eles especificam quais categorias de log são enviadas.
* Eles especificam quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento.
* Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias de 1 a 2.147.483.647.
* Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento estiver desabilitado, as políticas de retenção não terão nenhum efeito. Por exemplo, essa condição pode ocorrer quando apenas as opções hubs de eventos ou logs de monitor são selecionadas.
* As políticas de retenção são aplicadas por dia. O limite entre os dias ocorre no UTC (tempo universal coordenado da meia-noite). No final de um dia, os logs de dias que estão além da política de retenção são excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início de hoje, os logs de antes de ontem serão excluídos.

Com Azure Monitor configurações de diagnóstico, você pode rotear os logs de diagnóstico para análise em vários destinos diferentes.

* **Conta de armazenamento**: Salve os logs de diagnóstico em uma conta de armazenamento para auditoria ou inspeção manual. Você pode usar as configurações de diagnóstico para especificar o tempo de retenção em dias.
* **Hub de eventos**: transmita os logs para os hubs de eventos do Azure. Os logs tornam-se entrada para uma solução de serviço de parceiro/análise personalizada, como Power BI.
* **Log Analytics espaço de trabalho**: analise os logs com log Analytics. A integração do Synapse do Azure com o Log Analytics é útil nos seguintes cenários:
  * Você deseja escrever consultas complexas em um conjunto avançado de métricas que são publicadas pelo Azure Synapse para Log Analytics. Você pode criar alertas personalizados nessas consultas por meio de Azure Monitor.
  * Você deseja monitorar entre espaços de trabalho. Você pode rotear dados de vários espaços de trabalho para um único espaço de trabalho Log Analytics.

Você também pode usar uma conta de armazenamento ou um namespace de Hub de eventos que não esteja na assinatura do recurso que emite logs. O usuário que define a configuração deve ter acesso apropriado ao RBAC (controle de acesso baseado em função) do Azure para ambas as assinaturas.

#### <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico

Crie ou adicione configurações de diagnóstico para seu espaço de trabalho, pool de SQL dedicado ou pool de Apache Spark.

1. No portal, vá para monitor. Selecione **configurações** configurações de  >  **diagnóstico**.

1. Selecione o espaço de trabalho Synapse, o pool de SQL dedicado ou o pool de Apache Spark para o qual você deseja criar uma configuração de diagnóstico.

1. Se não existir nenhuma configuração de diagnóstico no espaço de trabalho selecionado, você será solicitado a criar uma configuração. Selecione **Ativar diagnóstico**.

   Se houver configurações de diagnóstico existentes no espaço de trabalho, você verá uma lista de configurações já configuradas no recurso. Selecione **Adicionar configuração de diagnóstico**.

1. Dê um nome à sua configuração, selecione **Enviar para log Analytics** e, em seguida, selecione um espaço de trabalho no **espaço de trabalho log Analytics**.

    > [!NOTE]
    > Como uma tabela de log do Azure não pode ter mais de 500 colunas, é **altamente recomendável** selecionar o _modo específico do recurso_. Para obter mais informações, consulte [referência de logs do AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Selecione **Salvar**.

Após alguns instantes, a nova configuração aparecerá na lista de configurações para seu espaço de trabalho, pool de SQL dedicado ou pool de Apache Spark. Os logs de diagnóstico são transmitidos para esse espaço de trabalho assim que novos dados de evento são gerados. Até 15 minutos podem decorrer entre o momento em que um evento é emitido e quando ele aparece no Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [monitorar execuções de pipeline no Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Para obter mais informações sobre como monitorar Apache Spark aplicativos, consulte o artigo [monitorar Apache Spark aplicativos no Synapse Studio](apache-spark-applications.md) .

Para obter mais informações sobre como monitorar solicitações SQL, consulte o artigo [monitorar solicitações do SQL no Synapse Studio](how-to-monitor-sql-requests.md) .
