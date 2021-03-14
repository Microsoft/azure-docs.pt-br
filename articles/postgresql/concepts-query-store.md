---
title: Repositório de Consultas-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso Repositório de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 1779df1c5f9baf2aa46ff809ecae9ec5e3cd7adb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581560"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorar o desempenho com o Repositório de Consultas

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões do servidor único 9,6 e posteriores

O recurso de Repositório de Consultas no Banco de Dados do Azure para PostgreSQL fornece uma maneira de acompanhar o desempenho de consultas ao longo do tempo. O Repositório de Consultas simplifica a solução de problemas ajudando você a rapidamente localizar as consultas de execução mais longa e que consomem mais recursos. O Repositório de Consultas captura automaticamente um histórico das estatísticas de runtime e consultas e o retém para sua análise. Ele separa os dados por janelas de tempo para que você possa ver padrões de uso do banco de dados. Os dados de todos os usuários, bancos de dados e consultas são armazenados em um banco de dados chamado **azure_sys** na instância do Banco de Dados do Azure para PostgreSQL.

> [!IMPORTANT]
> Não modifique o banco de dados **azure_sys** ou seus esquemas. Fazer isso impedirá que o Repositório de Consultas e os recursos de desempenho relacionados funcionem corretamente.

## <a name="enabling-query-store"></a>Habilitando o Repositório de Consultas
O Repositório de Consultas é um recurso que requer aceitação, portanto, ele não está ativo em um servidor por padrão. O armazenamento é habilitado ou desabilitado globalmente para todos os bancos de dados em um determinado servidor e não pode ser ativado ou desativado por banco de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitar o Repositório de Consultas usando o portal do Azure
1. Entre no portal do Azure e selecione seu servidor do Banco de Dados do Azure para PostgreSQL.
2. Selecione **Parâmetros de Servidor** na seção **Configurações** do menu.
3. Pesquise o parâmetro `pg_qs.query_capture_mode`.
4. Defina o valor como `TOP` e **salve**.

Para habilitar as estatísticas de espera no seu Repositório de Consultas: 
1. Pesquise o parâmetro `pgms_wait_sampling.query_capture_mode`.
1. Defina o valor como `ALL` e **salve**.


Como alternativa, você pode definir esses parâmetros usando o CLI do Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Permita que o primeiro lote de dados persista no banco de dados azure_sys por até 20 minutos.

## <a name="information-in-query-store"></a>Informações no Repositório de Consultas
O Repositório de Consultas tem dois repositórios:
- Um repositório de estatísticas de runtime para manter as informações de estatísticas de execução de consulta.
- Um repositório de estatísticas de espera para manter as informações de estatísticas de execução de espera.

Os cenários comuns para usar o Repositório de Consultas incluem:
- Determinação do número de vezes que uma consulta foi executada em uma determinada janela de tempo
- Comparar o tempo médio de execução de uma consulta entre janelas de tempo para ver grandes deltas
- Identificar consultas de execução mais longas nas últimas X horas
- Identificar as principais N consultas que estão aguardando recursos
- Entender a natureza de espera de uma consulta específica

Para minimizar o uso de espaço, as estatísticas de execução de runtime no repositório de estatísticas de runtime são agregadas em uma janela de tempo fixa configurável. As informações nesses repositórios são visíveis consultando as exibições do repositório de consultas.

## <a name="access-query-store-information"></a>Informações de Repositório de Consultas de acesso

Os dados de Repositório de Consultas são armazenados no banco azure_sys no servidor Postgres. 

A consulta a seguir retorna informações sobre consultas no Repositório de Consultas:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Ou essa consulta para estatísticas de espera:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Localizando consultas de espera
Os tipos de evento de espera combinam diferentes eventos de espera em buckets por semelhança. O Repositório de Consultas fornece o tipo de evento de espera, o nome do evento de espera específico e a consulta em questão. Ser capaz de correlacionar essas informações de espera com as estatísticas de runtime de consulta significa que você pode obter uma compreensão mais profunda do que contribui para as características de desempenho de consulta.

Aqui estão alguns exemplos de como você pode obter mais insights sobre sua carga de trabalho usando as estatísticas de espera no Repositório de Consultas:

| **Observação** | **Ação** |
|---|---|
|Esperas de bloqueio alto | Verifique os textos de consulta para as consultas afetadas e identifique as entidades de destino. Procure no Repositório de Consultas outras consultas que modificam a mesma entidade, que é executada com frequência e/ou têm alta duração. Depois de identificar essas consultas, considere alterar a lógica do aplicativo para melhorar a simultaneidade ou use um nível de isolamento menos restritivo.|
| Esperas de E/S de buffer alto | Localize as consultas com um grande número de leituras físicas no Repositório de Consultas. Se elas corresponderem às consultas com esperas de E/S altas, considere a possibilidade de introduzir um índice na entidade subjacente para realizar buscas em vez de verificações. Isso minimizaria a sobrecarga de E/S das consultas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice para esse servidor que otimizariam as consultas.|
| Esperas de memória alta | Localize as consultas que consomem mais memória no Repositório de Consultas. Essas consultas estão provavelmente atrasando o andamento das consultas afetadas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice que otimizariam essas consultas.|

## <a name="configuration-options"></a>Opções de configuração
Quando o Repositório de Consultas está habilitado, ele salva dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela. 

As opções a seguir estão disponíveis para configurar os parâmetros do Repositório de Consultas.

| **Parâmetro** | **Descrição** | **Default** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | Define quais instruções são rastreadas. | nenhum | none, top, all |
| pg_qs.max_query_text_length | Define o comprimento máximo de consulta que pode ser salvo. Consultas mais longas serão truncadas. | 6000 | 100 a 10 mil |
| pg_qs.retention_period_in_days | Define o período de retenção. | 7 | 1 a 30 |
| pg_qs.track_utility | Define se os comandos do utilitário são rastreados | on | on, off |

As opções a seguir se aplicam especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Default** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Define quais instruções são rastreadas para as estatísticas de espera. | nenhum | none, all|
| Pgms_wait_sampling.history_period | Define a frequência, em milissegundos, com a qual são realizadas amostras dos eventos de espera. | 100 | 1 a 600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode for NONE, a configuração pgms_wait_sampling.query_capture_mode não terá efeito.


Use o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou a [CLI do Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Exibições e funções
Exiba e gerencie o Repositório de Consultas usando as seguintes exibições e funções. Qualquer pessoa na função pública do PostgreSQL pode usar essas exibições para ver os dados no Repositório de Consultas. Essas exibições estão disponíveis somente no banco de dados **azure_sys**.

Consultas são normalizadas examinando sua estrutura após a remoção de literais e constantes. Se duas consultas forem idênticas, exceto por valores literais, elas terão o mesmo hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
Essa exibição retorna todos os dados no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário e ID de consulta distinta. 

|**Nome**   |**Tipo** | **Referências**  | **Descrição**|
|---|---|---|---|
|runtime_stats_entry_id |BIGINT | | ID da tabela runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID do usuário que executou a instrução|
|db_id  |oid    |pg_database.oid    |OID do banco de dados no qual a instrução foi executada|
|query_id   |BIGINT  || Código hash interno, computado da árvore de análise da instrução|
|query_sql_text |Varchar(10000)  || Texto de uma instrução representativa. Consultas diferentes com a mesma estrutura são agrupadas. Este texto é o da primeira das consultas no cluster.|
|plan_id    |BIGINT |   |ID do plano correspondente a essa consulta, ainda não disponível|
|start_time | timestamp  ||  Consultas são agregadas por buckets de tempo: o período de um bucket é de 15 minutos por padrão. Essa é a hora de início correspondente ao bucket de tempo para esta entrada.|
|end_time   | timestamp  ||  Hora de término correspondente ao bucket de tempo para esta entrada.|
|chamadas  |BIGINT  || Número de vezes que a consulta foi executada|
|total_time |double precision   ||  Tempo total de execução da consulta em milissegundos|
|min_time   |double precision   ||  Tempo mínimo de execução da consulta em milissegundos|
|max_time   |double precision   ||  Tempo máximo de execução da consulta em milissegundos|
|mean_time  |double precision   ||  Tempo médio de execução da consulta em milissegundos|
|stddev_time|   double precision    ||  Desvio padrão de tempo de execução da consulta em milissegundos |
|rows   |BIGINT ||  Número total de linhas recuperadas ou afetadas pela instrução|
|shared_blks_hit|   BIGINT  ||  Número total de ocorrências no cache do bloco compartilhado pela instrução|
|shared_blks_read|  BIGINT  ||  Número total de blocos compartilhados lidos pela instrução|
|shared_blks_dirtied|   BIGINT   || Número total de blocos compartilhados sujos pela instrução |
|shared_blks_written|   BIGINT  ||  Número total de blocos compartilhados gravados pela instrução|
|local_blks_hit|    BIGINT ||   Número total de ocorrências no cache do bloco local pela instrução|
|local_blks_read|   BIGINT   || Número total de leituras de blocos locais pela instrução|
|local_blks_dirtied|    BIGINT  ||  Número total de blocos locais sujos pela instrução|
|local_blks_written|    BIGINT  ||  Número total de blocos locais gravados pela instrução|
|temp_blks_read |BIGINT  || Número total de leituras de blocos temporários pela instrução|
|temp_blks_written| BIGINT   || Número total de gravações de blocos temporários pela instrução|
|blk_read_time  |double precision    || Tempo total que a instrução passou lendo blocos em milissegundos (se track_io_timing estiver habilitado, caso contrário, zero)|
|blk_write_time |double precision    || Tempo total que a instrução passou gravando blocos em milissegundos (se track_io_timing estiver habilitado, caso contrário, zero)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Essa exibição retorna os dados de texto da consulta no Repositório de Consultas. Há uma linha para cada query_text distinto.

| **Nome** | **Tipo** | **Descrição** |
|--|--|--|
| query_text_id | BIGINT | ID da tabela query_texts |
| query_sql_text | Varchar(10000) | Texto de uma instrução representativa. Consultas diferentes com a mesma estrutura são agrupadas. Este texto é o da primeira das consultas no cluster. |

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Essa exibição retorna os dados de eventos de espera no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário, ID de consulta e evento distinto.

| **Nome** | **Tipo** | **Referências** | **Descrição** |
|--|--|--|--|
| user_id | oid | pg_authid.oid | OID do usuário que executou a instrução |
| db_id | oid | pg_database.oid | OID do banco de dados no qual a instrução foi executada |
| query_id | BIGINT |  | Código hash interno, computado da árvore de análise da instrução |
| event_type | text |  | O tipo de evento pelo qual o back-end está esperando |
| event | text |  | O nome do evento de espera se o back-end estiver esperando no momento |
| chamadas | Integer |  | Número do mesmo evento capturado |

### <a name="functions"></a>Funções

Query_store.qs_reset() retorna void

`qs_reset` descarta todas as estatísticas coletadas até o momento pelo Repositório de Consultas. Essa função só pode ser executada pela função de administrador de servidor.

Query_store.staging_data_reset() retorna void

`staging_data_reset` descarta todas as estatísticas coletadas na memória pelo Repositório de Consultas (isto é, os dados na memória que ainda não foram liberados para o banco de dados). Essa função só pode ser executada pela função de administrador de servidor.


## <a name="azure-monitor"></a>Azure Monitor
O banco de dados do Azure para PostgreSQL é integrado com [Azure monitor configurações de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md). As configurações de diagnóstico permitem que você envie os logs do postgres no formato JSON para [Azure monitor logs](../azure-monitor/logs/log-query-overview.md) para análise e alertas, hubs de eventos para streaming e armazenamento do Azure para arquivamento.

>[!IMPORTANT]
> Este recurso de diagnóstico do está disponível somente nos tipos de preço Uso Geral e com otimização de memória.

### <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico
Você pode habilitar as configurações de diagnóstico para o servidor postgres usando o portal do Azure, a CLI, a API REST e o PowerShell. As categorias de log a serem configuradas são **QueryStoreRuntimeStatistics** e **QueryStoreWaitStatistics**. 

Para habilitar os logs de recursos usando o portal do Azure:

1. No portal, vá para configurações de diagnóstico no menu de navegação do seu servidor do Postgres.
2. Selecione Adicionar configuração de diagnóstico.
3. Nomeie essa configuração.
4. Selecione seu ponto de extremidade preferido (conta de armazenamento, Hub de eventos, log Analytics).
5. Selecione os tipos de log **QueryStoreRuntimeStatistics** e **QueryStoreWaitStatistics**.
6. Salve sua configuração.

Para habilitar essa configuração usando o PowerShell, a CLI ou a API REST, visite o [artigo configurações de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md).

### <a name="json-log-format"></a>Formato de log JSON
As tabelas a seguir descrevem os campos para os dois tipos de log. Dependendo do ponto de extremidade de saída escolhido, os campos incluídos e a ordem em que aparecem podem variar.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Campo** | **Descrição** |
|---|---|
| TimeGenerated [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| ResourceId | URI de recurso do Azure do servidor postgres |
| Categoria | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Nome do servidor postgres | 
| runtime_stats_entry_id_s | ID da tabela runtime_stats_entries |
| user_id_s | OID do usuário que executou a instrução |
| db_id_s | OID do banco de dados no qual a instrução foi executada |
| query_id_s | Código hash interno, computado da árvore de análise da instrução |
| end_time_s | Hora de término correspondente ao Bucket de tempo para esta entrada |
| calls_s | Número de vezes que a consulta foi executada |
| total_time_s | Tempo total de execução da consulta em milissegundos |
| min_time_s | Tempo mínimo de execução da consulta em milissegundos |
| max_time_s | Tempo máximo de execução da consulta em milissegundos |
| mean_time_s | Tempo médio de execução da consulta em milissegundos |
| ResourceGroup | O grupo de recursos | 
| SubscriptionId | Sua ID de assinatura |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Recurso | Nome do servidor postgres |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Campo** | **Descrição** |
|---|---|
| TimeGenerated [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| ResourceId | URI de recurso do Azure do servidor postgres |
| Categoria | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | OID do usuário que executou a instrução |
| db_id_s | OID do banco de dados no qual a instrução foi executada |
| query_id_s | Código hash interno da consulta |
| calls_s | Número do mesmo evento capturado |
| event_type_s | O tipo de evento pelo qual o back-end está esperando |
| event_s | O nome do evento de espera se o back-end estiver aguardando |
| start_time_t | Hora de início do evento |
| end_time_s | Hora de término do evento | 
| LogicalServerName_s | Nome do servidor postgres | 
| ResourceGroup | O grupo de recursos | 
| SubscriptionId | Sua ID de assinatura |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Recurso | Nome do servidor postgres |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos
- Se um servidor PostgreSQL tem o parâmetro default_transaction_read_only ativo, o Repositório de Consultas não é capaz de capturar dados.
- A funcionalidade do Repositório de Consultas poderá ser interrompida se ele encontrar consultas Unicode longas (> = 6.000 bytes).
- As [réplicas de leitura](concepts-read-replicas.md) replicam repositório de consultas dados do servidor primário. Isso significa que a Repositório de Consultas de uma réplica de leitura não fornece estatísticas sobre as consultas executadas na réplica de leitura.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [cenários em que o Repositório de Consultas pode ser especialmente útil](concepts-query-store-scenarios.md).
- Saiba mais sobre as [melhores práticas para usar o Repositório de Consultas](concepts-query-store-best-practices.md).
