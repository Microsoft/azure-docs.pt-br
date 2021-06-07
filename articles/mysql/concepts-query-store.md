---
title: Repositório de Consultas – Banco de Dados do Azure para MySQL
description: Saiba mais sobre o recurso Repositório de Consultas no Banco de Dados do Azure para MySQL para ajudar você a acompanhar o desempenho com o tempo.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 82482b260233994672e603c16fe8cf919c92337f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201018"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorar o desempenho do Banco de Dados do Azure para MySQL com o Repositório de Consultas

**Aplica-se a:** Banco de Dados do Azure para MySQL 5.7, 8.0

O recurso Repositório de Consultas no Banco de Dados do Azure para MySQL oferece uma forma de acompanhar o desempenho de consultas ao longo do tempo. O Repositório de Consultas simplifica a solução de problemas ajudando você a rapidamente localizar as consultas de execução mais longa e que consomem mais recursos. O Repositório de Consultas captura automaticamente um histórico das estatísticas de runtime e consultas e o retém para sua análise. Ele separa os dados por janelas de tempo para que você possa ver padrões de uso do banco de dados. Os dados de todos os usuários, bancos de dados e consultas são armazenados no banco de dados de esquema **mysql** na instância do Banco de Dados do Azure para MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para usar o Repositório de Consultas

O repositório de consultas pode ser usado em vários cenários, incluindo o seguinte:

- Detectar consultas regredidas
- Determinação do número de vezes que uma consulta foi executada em uma determinada janela de tempo
- Comparar o tempo médio de execução de uma consulta entre janelas de tempo para ver grandes deltas

## <a name="enabling-query-store"></a>Habilitando o Repositório de Consultas

O Repositório de Consultas é um recurso que requer aceitação, portanto, ele não está ativo em um servidor por padrão. O repositório de consultas é habilitado ou desabilitado globalmente para todos os bancos de dados em um determinado servidor e não pode ser ativado ou desativado por banco de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitar o Repositório de Consultas usando o portal do Azure

1. Entre no portal do Azure e selecione seu servidor do Banco de Dados do Azure para MySQL.
1. Selecione **Parâmetros de Servidor** na seção **Configurações** do menu.
1. Pesquise o parâmetro query_store_capture_mode.
1. Defina o valor como ALL e **Salve**.

Para habilitar as estatísticas de espera no seu Repositório de Consultas:

1. Pesquise o parâmetro query_store_wait_sampling_capture_mode.
1. Defina o valor como ALL e **Salve**.

Permita que o primeiro lote de dados se mantenha no banco de dados mysql por até 20 minutos.

## <a name="information-in-query-store"></a>Informações no Repositório de Consultas

O Repositório de Consultas tem dois repositórios:

- Um repositório de estatísticas de runtime para manter as informações de estatísticas de execução de consulta.
- Um repositório de estatísticas de espera para manter as informações de estatísticas de execução de espera.

Para minimizar o uso de espaço, as estatísticas de execução de runtime no repositório de estatísticas de runtime são agregadas em uma janela de tempo fixa configurável. As informações nesses repositórios são visíveis consultando as exibições do repositório de consultas.

A consulta a seguir retorna informações sobre consultas no Repositório de Consultas:

```sql
SELECT * FROM mysql.query_store;
```

Ou essa consulta para estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Localizando consultas de espera

> [!NOTE]
> As estatísticas de espera não devem ser habilitadas durante o horário de pico da carga de trabalho nem ativadas indefinidamente para cargas de trabalho confidenciais. <br>Para cargas de trabalho em execução com alta utilização de CPU ou em servidores configurados com menos vCores, tome cuidado ao habilitar estatísticas de espera. Ela não deve ser ativada indefinidamente.

Os tipos de evento de espera combinam diferentes eventos de espera em buckets por semelhança. O Repositório de Consultas fornece o tipo de evento de espera, o nome do evento de espera específico e a consulta em questão. Ser capaz de correlacionar essas informações de espera com as estatísticas de runtime de consulta significa que você pode obter uma compreensão mais profunda do que contribui para as características de desempenho de consulta.

Aqui estão alguns exemplos de como você pode obter mais insights sobre sua carga de trabalho usando as estatísticas de espera no Repositório de Consultas:

| **Observação** | **Ação** |
|---|---|
|Esperas de bloqueio alto | Verifique os textos de consulta para as consultas afetadas e identifique as entidades de destino. Procure no Repositório de Consultas outras consultas que modificam a mesma entidade, que é executada com frequência e/ou têm alta duração. Depois de identificar essas consultas, considere alterar a lógica do aplicativo para melhorar a simultaneidade ou use um nível de isolamento menos restritivo. |
|Esperas de E/S de buffer alto | Localize as consultas com um grande número de leituras físicas no Repositório de Consultas. Se elas corresponderem às consultas com esperas de E/S altas, considere a possibilidade de introduzir um índice na entidade subjacente para realizar buscas em vez de verificações. Isso minimizaria a sobrecarga de E/S das consultas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice para esse servidor que otimizariam as consultas. |
|Esperas de memória alta | Localize as consultas que consomem mais memória no Repositório de Consultas. Essas consultas estão provavelmente atrasando o andamento das consultas afetadas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice que otimizariam essas consultas. |

## <a name="configuration-options"></a>Opções de configuração

Quando o Repositório de Consultas está habilitado, ele salva dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela.

As opções a seguir estão disponíveis para configurar os parâmetros do Repositório de Consultas.

| **Parâmetro** | **Descrição** | **Default** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Ativar/desativar o recurso de repositório de consultas com base no valor. Observação: Se performance_schema estiver DESATIVADO, a ativação de query_store_capture ativará performance_schema e um subconjunto de instrumentos de esquema de desempenho necessário para esse recurso. | ALL | NONE, ALL |
| query_store_capture_interval | O intervalo de captura do repositório de consultas em minutos. Permite especificar o intervalo no qual as métricas de consulta são agregadas | 15 | 5 a 60 |
| query_store_capture_utility_queries | ATIVE ou DESATIVE para capturar todas as consultas do utilitário em execução no sistema. | Não | YES, NO |
| query_store_retention_period_in_days | Janela de tempo em dias para manter os dados no repositório de consultas. | 7 | 1 a 30 |

As opções a seguir se aplicam especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Default** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite ATIVAR/DESATIVAR as estatísticas de espera. | Nenhuma | NONE, ALL |
| query_store_wait_sampling_frequency | Altera a frequência de amostragem de espera em segundos. 5 a 300 segundos. | 30 | 5 a 300 |

> [!NOTE]
> No momento, **query_store_capture_mode** substitui essa configuração, o que significa que **query_store_capture_mode** e **query_store_wait_sampling_capture_mode** precisam estar habilitados para TODOS para que as estatísticas de espera funcionem. Se **query_store_capture_mode** estiver desativado, as estatísticas de espera estarão desativadas também, pois elas utilizam performance_schema habilitado e query_text capturado por repositório de consultas.

Use o [portal do Azure](howto-server-parameters.md) ou a [CLI do Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Exibições e funções

Exiba e gerencie o Repositório de Consultas usando as seguintes exibições e funções. Qualquer pessoa na [função pública de privilégio selecionada](howto-create-users.md#to-create-more-admin-users-in-azure-database-for-mysql) pode usar essas exibições para ver os dados no Repositório de Consultas. Essas exibições estão disponíveis somente no banco de dados **mysql**.

Consultas são normalizadas examinando sua estrutura após a remoção de literais e constantes. Se duas consultas forem idênticas, exceto por valores literais, elas terão o mesmo hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Essa exibição retorna todos os dados no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário e ID de consulta distinta.

| **Nome** | **Tipo de Dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | Não | Nome do esquema |
| `query_id`| bigint(20) | Não| ID exclusiva gerada para a consulta específica; se a mesma consulta for executada em um esquema diferente, uma nova ID será gerada |
| `timestamp_id` | timestamp| Não| Carimbo de data/hora no qual a consulta é executada. Isso se baseia na configuração query_store_interval|
| `query_digest_text`| longtext| Não| O texto de consulta normalizado após a remoção de todos os literais|
| `query_sample_text` | longtext| Não| Primeira exibição da consulta real com literais|
| `query_digest_truncated` | bit| YES| Se o texto da consulta foi truncado. O valor será Yes se a consulta tiver mais de 1 KB|
| `execution_count` | bigint(20)| Não| O número de vezes que a consulta foi executada para essa ID de carimbo de data/hora durante o período de intervalo configurado|
| `warning_count` | bigint(20)| Não| Número de avisos que essa consulta gerou durante o intervalo|
| `error_count` | bigint(20)| Não| Número de erros que essa consulta gerou durante o intervalo|
| `sum_timer_wait` | double| YES| Tempo de execução total dessa consulta durante o intervalo|
| `avg_timer_wait` | double| YES| Tempo médio de execução para essa consulta durante o intervalo|
| `min_timer_wait` | double| YES| Tempo mínimo de execução para essa consulta|
| `max_timer_wait` | double| YES| Tempo máximo de execução|
| `sum_lock_time` | bigint(20)| Não| Quantidade total de tempo gasto para todos os bloqueios dessa execução de consulta durante essa janela de tempo|
| `sum_rows_affected` | bigint(20)| Não| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| Não| Número de linhas enviadas ao cliente|
| `sum_rows_examined` | bigint(20)| Não| Número de linhas verificadas|
| `sum_select_full_join` | bigint(20)| Não| Número de junções completas|
| `sum_select_scan` | bigint(20)| Não| Número de verificações de seleção |
| `sum_sort_rows` | bigint(20)| Não| Número de linhas classificadas|
| `sum_no_index_used` | bigint(20)| Não| Número de vezes em que a consulta não usou nenhum índice|
| `sum_no_good_index_used` | bigint(20)| Não| Número de vezes em que o mecanismo de execução de consulta não usou índices bons|
| `sum_created_tmp_tables` | bigint(20)| Não| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| Não| Número total de tabelas temporárias criadas no disco (gera E/S)|
| `first_seen` | timestamp| Não| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` | timestamp| Não| A última ocorrência (UTC) da consulta durante essa janela de agregação|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Essa exibição retorna os dados de eventos de espera no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário, ID de consulta e evento distinto.

| **Nome**| **Tipo de Dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` | timestamp | Não| Início do intervalo (incremento de 15 minutos)|
| `interval_end` | timestamp | Não| Fim do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | Não| ID exclusiva gerada na consulta normalizada (do repositório de consultas)|
| `query_digest_id` | varchar(32) | Não| O texto de consulta normalizado após a remoção de todos os literais (do repositório de consultas) |
| `query_digest_text` | longtext | Não| Primeira exibição da consulta real com literais (do repositório de consultas) |
| `event_type` | varchar(32) | Não| Categoria do evento de espera |
| `event_name` | varchar(128) | Não| Nome do evento de espera |
| `count_star` | bigint(20) | Não| Número de eventos de espera amostrados durante o intervalo para a consulta |
| `sum_timer_wait_ms` | double | Não| Tempo de espera total (em milissegundos) dessa consulta durante o intervalo |

### <a name="functions"></a>Funções

| **Nome**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Limpa todos os dados do repositório de consultas antes do carimbo de data/hora fornecido |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Limpa todos os dados do evento de espera antes do carimbo de data/hora fornecido |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Limpa as recomendações cuja expiração é anterior ao carimbo de data/hora especificado |

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- Se um servidor MySQL tiver o parâmetro `default_transaction_read_only` ativado, o Repositório de Consultas não poderá capturar dados.
- A funcionalidade do Repositório de Consultas poderá ser interrompida se ele encontrar consultas Unicode longas (\>= 6000 bytes).
- O período de retenção para estatísticas de espera é de 24 horas.
- As estatísticas de espera usam um exemplo para capturar uma fração de eventos. A frequência pode ser modificada usando o parâmetro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [Análise de Desempenho de Consultas](concepts-query-performance-insight.md)
