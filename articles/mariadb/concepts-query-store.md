---
title: Store consulta no banco de dados do Azure para MariaDB
description: Este artigo descreve o recurso de Store de consulta no banco de dados do Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 883f780059e38c53dedda309dd059cc714539f80
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462083"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Monitorar o banco de dados do Azure para desempenho MariaDB com consulta Store

**Aplica-se a:**  banco de dados do Azure para MariaDB 10.2

> [!NOTE]
> Consulta Store está em visualização.

O recurso de Store de consulta no banco de dados do Azure para Mariadb fornece uma maneira de acompanhar o desempenho de consulta ao longo do tempo. O Repositório de Consultas simplifica a solução de problemas ajudando você a rapidamente localizar as consultas de execução mais longa e que consomem mais recursos. O Repositório de Consultas captura automaticamente um histórico das estatísticas de tempo de execução e consultas e o retém para sua análise. Ele separa os dados por janelas de tempo para que você possa ver padrões de uso do banco de dados. Dados para todos os usuários, bancos de dados e consultas são armazenados na **mysql** banco de dados de esquema no banco de dados do Azure para MariaDB de instância.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para usar a consulta Store

Repositório de consultas pode ser usado em vários cenários, incluindo o seguinte:

- Detectando as consultas retornadas
- Determinação do número de vezes que uma consulta foi executada em uma determinada janela de tempo
- Comparar o tempo médio de execução de uma consulta entre janelas de tempo para ver grandes deltas

## <a name="enabling-query-store"></a>Habilitando o Repositório de Consultas

O Repositório de Consultas é um recurso que requer aceitação, portanto, ele não está ativo em um servidor por padrão. O repositório de consultas está habilitado ou desabilitado globalmente para todos os bancos de dados em um determinado servidor e não pode ser ativado ou desativado por banco de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitar o Repositório de Consultas usando o portal do Azure

1. Entre portal do Azure e selecione seu banco de dados do Azure para MariaDB.
1. Selecione **parâmetros de servidor** no **configurações** seção do menu.
1. Procure o parâmetro query_store_capture_mode.
1. Defina o valor para todos e **salvar**.

Para habilitar as estatísticas de espera em sua consulta Store:

1. Procure o parâmetro query_store_wait_sampling_capture_mode.
1. Defina o valor para todos e **salvar**.

Permitir até 20 minutos para o primeiro lote de dados para persistir no banco de dados mysql.

## <a name="information-in-query-store"></a>Informações no Repositório de Consultas

O Repositório de Consultas tem dois repositórios:

- Armazenar uma estatística de tempo de execução para manter as informações de estatísticas de execução de consulta.
- Um repositório de estatísticas de espera para manter informações de estatísticas de espera.

Para minimizar o uso de espaço, as estatísticas de execução de tempo de execução no repositório de estatísticas de tempo de execução são agregadas em uma janela de tempo fixo, configurável. As informações nesses repositórios são visíveis consultando as exibições do repositório de consultas.

A consulta a seguir retorna informações sobre consultas no Repositório de Consultas:

```sql
SELECT * FROM mysql.query_store;
```

Essa consulta ou para as estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Localizando consultas de espera

Os tipos de evento de espera combinam diferentes eventos de espera em buckets por semelhança. O Repositório de Consultas fornece o tipo de evento de espera, o nome do evento de espera específico e a consulta em questão. Ser capaz de correlacionar essas informações de espera com as estatísticas de tempo de execução de consulta significa que você pode obter uma compreensão mais profunda do que contribui para as características de desempenho de consulta.

Aqui estão alguns exemplos de como você pode obter mais insights sobre sua carga de trabalho usando as estatísticas de espera no Repositório de Consultas:

| **Observação** | **Ação** |
|---|---|
|Esperas de bloqueio alto | Verifique os textos de consulta para as consultas afetadas e identifique as entidades de destino. Procure no Repositório de Consultas outras consultas que modificam a mesma entidade, que é executada com frequência e/ou têm alta duração. Depois de identificar essas consultas, considere alterar a lógica do aplicativo para melhorar a simultaneidade ou use um nível de isolamento menos restritivo. |
|Esperas de E/S de buffer alto | Localize as consultas com um grande número de leituras físicas no Repositório de Consultas. Se eles corresponderem às consultas com esperas de e/s, considere a possibilidade de introduzir um índice na entidade subjacente, fazer buscas em vez de verificações. Isso minimizaria a sobrecarga de E/S das consultas. Verifique as **recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice para o servidor que seria otimizar as consultas. |
|Esperas de memória alta | Localize as consultas que consomem mais memória no Repositório de Consultas. Essas consultas estão provavelmente atrasando o andamento das consultas afetadas. Verifique as **recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice que seriam otimizar essas consultas.|

## <a name="configuration-options"></a>Opções de configuração

Quando o Repositório de Consultas está habilitado, ele salva dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela.

As opções a seguir estão disponíveis para configurar os parâmetros do Repositório de Consultas.

| **Parâmetro** | **Descrição** | **Padrão** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Ative o recurso de repositório de consultas ON/OFF com base no valor. Observação: Se performance_schema for OFF, ativando query_store_capture_mode ativará performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para esse recurso. | ALL | NENHUM, TODOS |
| query_store_capture_interval | O repositório de consultas captura de intervalo em minutos. Permite especificar o intervalo no qual as métricas de consulta são agregadas | 15 | 5 - 60 |
| query_store_capture_utility_queries | A ativação ON ou OFF para capturar todas as consultas de utilitário que está em execução no sistema. | NÃO | SIM, NÃO |
| query_store_retention_period_in_days | Janela de tempo em dias para manter os dados no repositório de consultas. | 7 | 1 a 30 |

As opções a seguir se aplicam especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Padrão** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite a ativação ON / OFF as estatísticas de espera. | NENHUM | NENHUM, TODOS |
| query_store_wait_sampling_frequency | Frequência de altera de amostragem de espera em segundos. 5 a 300 segundos. | 30 | 5-300 |

> [!NOTE]
> No momento **query_store_capture_mode** prevalece sobre essa configuração, o que significa que ambos **query_store_capture_mode** e **query_store_wait_sampling_capture_mode** precisa ser habilitado para todas as estatísticas de espera trabalhar. Se **query_store_capture_mode** estiver desativada, as estatísticas de espera será desligada, bem como as estatísticas de espera utiliza o performance_schema habilitado e o query_text capturados pelo repositório de consultas.

Use o [portal do Azure](howto-server-parameters.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Exibições e funções

Exiba e gerencie o Repositório de Consultas usando as seguintes exibições e funções. Qualquer pessoa a [função pública de privilégio selecione](howto-create-users.md#create-additional-admin-users) pode usar esses modos de exibição para ver os dados na consulta Store. Essas exibições estão disponíveis somente na **mysql** banco de dados.

Consultas são normalizadas examinando sua estrutura após a remoção de literais e constantes. Se duas consultas forem idênticas, exceto por valores literais, elas terão o mesmo hash.

### <a name="mysqlquerystore"></a>mysql.query_store

Essa exibição retorna todos os dados no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário e ID de consulta distinta.

| **Name** | **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | NÃO | Nome do esquema |
| `query_id`| bigint(20) | NÃO| ID exclusiva gerada para a consulta específica, se a mesma consulta for executada em um esquema diferente, uma nova ID será gerado |
| `timestamp_id` | timestamp| NÃO| O carimbo de hora em que a consulta é executada. Isso se baseia na configuração query_store_interval|
| `query_digest_text`| longtext| NÃO| O texto da consulta normalizado após a remoção de todos os literais|
| `query_sample_text` | longtext| NÃO| Primeira aparição de consulta real com literais|
| `query_digest_truncated` | bit| SIM| Se o texto da consulta foi truncado. Valor será Sim, se a consulta for maior que 1 KB|
| `execution_count` | bigint(20)| NÃO| O número de vezes que a consulta foi executada para esta ID de carimbo de hora / durante o período de intervalo configurado|
| `warning_count` | bigint(20)| NÃO| Número de avisos que esta consulta gerada durante o interno|
| `error_count` | bigint(20)| NÃO| Número de erros que esta consulta gerada durante o intervalo|
| `sum_timer_wait` | double| SIM| Tempo total de execução dessa consulta durante o intervalo|
| `avg_timer_wait` | double| SIM| Tempo médio de execução para esta consulta durante o intervalo|
| `min_timer_wait` | double| SIM| Mínimo de tempo de execução para esta consulta|
| `max_timer_wait` | double| SIM| Tempo de execução máximo|
| `sum_lock_time` | bigint(20)| NÃO| Quantidade total de tempo gasto para todos os bloqueios para essa execução de consulta durante a janela de tempo|
| `sum_rows_affected` | bigint(20)| NÃO| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| NÃO| Número de linhas enviadas ao cliente|
| `sum_rows_examined` | bigint(20)| NÃO| Número de linhas verificadas|
| `sum_select_full_join` | bigint(20)| NÃO| Número de junções completos|
| `sum_select_scan` | bigint(20)| NÃO| Número de exames de select |
| `sum_sort_rows` | bigint(20)| NÃO| Número de linhas classificadas|
| `sum_no_index_used` | bigint(20)| NÃO| Número de vezes quando a consulta não tiver usado todos os índices|
| `sum_no_good_index_used` | bigint(20)| NÃO| Número de vezes quando o mecanismo de execução de consulta não tiver usado qualquer bons índices|
| `sum_created_tmp_tables` | bigint(20)| NÃO| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| NÃO| Número total de tabelas temporárias criadas no disco (gera e/s)|
| `first_seen` | timestamp| NÃO| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` | timestamp| NÃO| A última ocorrência (UTC) da consulta durante a janela de agregação|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

Essa exibição retorna os dados de eventos de espera no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário, ID de consulta e evento distinto.

| **Name**| **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` | timestamp | NÃO| Início do intervalo (incremento de 15 minutos)|
| `interval_end` | timestamp | NÃO| Final do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | NÃO| ID exclusiva gerada na consulta normalizada (a partir do repositório de consultas)|
| `query_digest_id` | varchar(32) | NÃO| O texto da consulta normalizado após a remoção de todos os literais (a partir do repositório de consultas) |
| `query_digest_text` | longtext | NÃO| Primeira aparição de consulta real com literais (a partir do repositório de consultas) |
| `event_type` | varchar(32) | NÃO| Categoria do evento de espera |
| `event_name` | varchar(128) | NÃO| Nome do evento de espera |
| `count_star` | bigint(20) | NÃO| Número de eventos de espera durante o intervalo para a consulta de amostra |
| `sum_timer_wait_ms` | double | NÃO| Tempo de espera total (em milissegundos) dessa consulta durante o intervalo |

### <a name="functions"></a>Funções

| **Name**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Limpa todos os dados de repositório de consulta antes do carimbo de hora determinada |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Espera de removerá todos os dados de evento antes do carimbo de hora determinada |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Recomendações de limpezas cuja validade está antes do carimbo de hora determinada |

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- Se um servidor MariaDB tem o parâmetro `default_transaction_read_only` , consulta Store não é possível capturar dados.
- Funcionalidade de Store de consulta pode ser interrompida se ele encontrar consultas longas de Unicode (\>= 6000 bytes).
- O período de retenção de estatísticas de espera é de 24 horas.
- As estatísticas de espera usa captura de ti de exemplo uma fração dos eventos. A frequência pode ser modificada usando o parâmetro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [análises de desempenho de consulta](concepts-query-performance-insight.md)
