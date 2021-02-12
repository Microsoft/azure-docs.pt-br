---
title: Parâmetros do servidor-banco de dados do Azure para MySQL
description: Este tópico fornece diretrizes para configurar parâmetros de servidor no banco de dados do Azure para MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/26/2021
ms.openlocfilehash: 1b0bcf528a16e2f75bf21235980424b5375f8824
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539477"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Parâmetros de servidor no banco de dados do Azure para MySQL

Este artigo fornece considerações e diretrizes para configurar parâmetros de servidor no banco de dados do Azure para MySQL.

## <a name="what-are-server-parameters"></a>O que são parâmetros de servidor? 

O mecanismo MySQL fornece várias variáveis/parâmetros de servidor diferentes que podem ser usados para configurar e ajustar o comportamento do mecanismo. Alguns parâmetros podem ser definidos dinamicamente durante o tempo de execução, enquanto outros são "estáticos", exigindo uma reinicialização do servidor para serem aplicados.

O banco de dados do Azure para MySQL expõe a capacidade de alterar o valor de vários parâmetros do servidor MySQL usando o [portal do Azure](./howto-server-parameters.md), o [CLI do Azure](./howto-configure-server-parameters-using-cli.md)e o [PowerShell](./howto-configure-server-parameters-using-powershell.md) para corresponder às necessidades da sua carga de trabalho.

## <a name="configurable-server-parameters"></a>Parâmetros de servidor configuráveis

A lista de parâmetros de servidor com suporte está em constante crescimento. Use a guia parâmetros do servidor na portal do Azure para exibir a lista completa e configurar os valores dos parâmetros do servidor.

Consulte as seguintes seções abaixo para saber mais sobre os limites dos vários parâmetros de servidor mais atualizados. Os limites são determinados pelo tipo de preço e vCores do servidor.

### <a name="thread-pools"></a>Pools de threads

O MySQL tradicionalmente atribui um thread para cada conexão de cliente. À medida que o número de usuários simultâneos aumenta, há uma queda de desempenho correspondente. Muitos threads ativos podem impactar significativamente o desempenho devido à maior alternância de contexto, contenção de thread e localidade inadequada para caches de CPU.

Pools de threads que são um recurso do lado do servidor e são distintos do pool de conexões, maximizam o desempenho introduzindo um pool dinâmico de threads de trabalho que pode ser usado para limitar o número de threads ativos em execução no servidor e minimizar a rotatividade de threads. Isso ajuda a garantir que uma intermitência de conexões não fará com que o servidor fique sem recursos ou falhe com um erro de memória insuficiente. Os pools de threads são mais eficientes para consultas curtas e cargas de trabalho com uso intensivo de CPU, por exemplo, cargas de trabalho OLTP.

Para saber mais sobre pools de threads, consulte [apresentando pools de threads no banco de dados do Azure para MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> O recurso pool de threads não tem suporte para a versão 5,6 do MySQL. 

### <a name="configuring-the-thread-pool"></a>Configurando o pool de threads
Para habilitar o pool de threads, atualize o `thread_handling` parâmetro do servidor para "pool-de-threads". Por padrão, esse parâmetro é definido como `one-thread-per-connection` , o que significa que o MySQL cria um novo thread para cada nova conexão. Observe que esse é um parâmetro estático e requer uma reinicialização do servidor para ser aplicada.

Você também pode configurar o número máximo e mínimo de threads no pool definindo os seguintes parâmetros de servidor: 
- `thread_pool_max_threads`: Esse valor garante que não haverá mais do que esse número de threads no pool.
- `thread_pool_min_threads`: Esse valor define o número de threads que serão reservados mesmo depois que as conexões forem fechadas.

Para melhorar os problemas de desempenho de consultas curtas no pool de threads, o banco de dados do Azure para MySQL permite que você habilite a execução em lote em que, em vez de retornar ao pool de threads imediatamente após a execução de uma consulta, os threads permanecerão ativos por um curto período de espera para a próxima consulta por meio dessa conexão. Em seguida, o thread executa a consulta rapidamente e, uma vez concluída, aguarda o próximo, até que o consumo de tempo geral desse processo exceda um limite. O comportamento de execução em lotes é determinado usando os seguintes parâmetros de servidor:  

-  `thread_pool_batch_wait_timeout`: Esse valor especifica o tempo que um thread aguarda para que outra consulta seja processada.
- `thread_pool_batch_max_time`: Esse valor determina o tempo máximo que um thread irá repetir o ciclo de execução da consulta e aguardando a próxima consulta.

> [!IMPORTANT]
> Teste o pool de threads antes de ligá-lo na produção. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

No banco de dados do Azure para MySQL, os logs binários estão sempre habilitados (ou seja, `log_bin` é definido como ativado). Caso deseje usar gatilhos, você receberá um erro semelhante a *você não tem o privilégio de superprivilégios e o log binário habilitado (talvez você queira usar a variável menos segura `log_bin_trust_function_creators` )*. 

O formato de log binário é sempre uma **linha** e todas as conexões com o servidor **sempre** usam o log binário baseado em linha. Com o log binário baseado em linha, os problemas de segurança não existem e o log binário não pode ser interrompido, portanto, você pode definir com segurança [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) como **true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) para saber mais sobre esse parâmetro.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Servidores com suporte para armazenamento de até 4 TB

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Uso Geral|2|3758096384|134217728|3758096384|
|Uso Geral|4|8053063680|134217728|8053063680|
|Uso Geral|8|16106127360|134217728|16106127360|
|Uso Geral|16|32749125632|134217728|32749125632|
|Uso Geral|32|66035122176|134217728|66035122176|
|Uso Geral|64|132070244352|134217728|132070244352|
|Otimizado para memória|2|7516192768|134217728|7516192768|
|Otimizado para memória|4|16106127360|134217728|16106127360|
|Otimizado para memória|8|32212254720|134217728|32212254720|
|Otimizado para memória|16|65498251264|134217728|65498251264|
|Otimizado para memória|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Os servidores dão suporte a até 16 TB de armazenamento

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Uso Geral|2|7516192768|134217728|7516192768|
|Uso Geral|4|16106127360|134217728|16106127360|
|Uso Geral|8|32212254720|134217728|32212254720|
|Uso Geral|16|65498251264|134217728|65498251264|
|Uso Geral|32|132070244352|134217728|132070244352|
|Uso Geral|64|264140488704|134217728|264140488704|
|Otimizado para memória|2|15032385536|134217728|15032385536|
|Otimizado para memória|4|32212254720|134217728|32212254720|
|Otimizado para memória|8|64424509440|134217728|64424509440|
|Otimizado para memória|16|130996502528|134217728|130996502528|
|Otimizado para memória|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` Só pode ser atualizado nos tipos de preço Uso Geral e com otimização de memória.

O MySQL armazena a tabela InnoDB em espaços de tabela diferentes com base na configuração fornecida durante a criação da tabela. O [espaço de tabela do sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de tabela de arquivo por tabela](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contém dados e índices de uma única tabela InnoDB e é armazenado no sistema de arquivos em seu próprio arquivo de dados. Esse comportamento é controlado pelo parâmetro do servidor `innodb_file_per_table`. Definir `innodb_file_per_table` como `OFF` faz com que o InnoDB crie tabelas no espaço de tabela do sistema. Caso contrário, o InnoDB cria tabelas em espaços de tabela de arquivo por tabela.

O banco de dados do Azure para MySQL dá suporte a, no máximo, **4 TB**, em um único arquivo. Se o tamanho do seu banco de dados for maior do que 4 TB, você deverá criar a tabela no [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tablespace. Se você tiver um tamanho de tabela único maior que 4 TB, deverá usar a tabela de partição.

### <a name="join_buffer_size"></a>join_buffer_size

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) para saber mais sobre esse parâmetro.

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Basic|1|Não configurável na camada básica|N/D|N/D|
|Basic|2|Não configurável na camada básica|N/D|N/D|
|Uso Geral|2|262144|128|268435455|
|Uso Geral|4|262144|128|536870912|
|Uso Geral|8|262144|128|1073741824|
|Uso Geral|16|262144|128|2147483648|
|Uso Geral|32|262144|128|4294967295|
|Uso Geral|64|262144|128|4294967295|
|Otimizado para memória|2|262144|128|536870912|
|Otimizado para memória|4|262144|128|1073741824|
|Otimizado para memória|8|262144|128|2147483648|
|Otimizado para memória|16|262144|128|4294967295|
|Otimizado para memória|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Tipo de preço**|**vCore(s)**|**Valor padrão**|**Valor mínimo**|**Valor máximo**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Uso Geral|2|300|10|600|
|Uso Geral|4|625|10|1250|
|Uso Geral|8|1250|10|2500|
|Uso Geral|16|2500|10|5.000|
|Uso Geral|32|5.000|10|10000|
|Uso Geral|64|10000|10|20000|
|Otimizado para memória|2|625|10|1250|
|Otimizado para memória|4|1250|10|2500|
|Otimizado para memória|8|2500|10|5.000|
|Otimizado para memória|16|5.000|10|10000|
|Otimizado para memória|32|10000|10|20000|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> ERRO 1040 (08004): Muitas conexões

> [!IMPORTANT]
> Para obter a melhor experiência, recomendamos usar um pool de conexões como o ProxySQL para gerenciar conexões com eficiência.

A criação de novas conexões de cliente com o MySQL leva tempo e, uma vez estabelecidas, essas conexões ocupam recursos do banco de dados, mesmo quando ociosas. A maioria dos aplicativos solicita muitas conexões de curta duração, o que agrega a essa situação. O resultado é um número menor de recursos disponíveis para sua carga de trabalho real, o que leva à redução do desempenho. Um pool de conexões que diminui as conexões ociosas e reutiliza as conexões existentes ajudará a evitar isso. Para saber mais sobre a configuração do ProxySQL, visite nossa [postagem do blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL é uma ferramenta de comunidade de código aberto. Ela tem suporte da Microsoft em uma base de melhor esforço. Para obter suporte de produção com diretrizes autoritativas, você pode avaliar e entrar em contato com o [suporte ao produto ProxySQL](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) para saber mais sobre esse parâmetro.

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Basic|1|Não configurável na camada básica|N/D|N/D|
|Basic|2|Não configurável na camada básica|N/D|N/D|
|Uso Geral|2|16777216|16384|268435455|
|Uso Geral|4|16777216|16384|536870912|
|Uso Geral|8|16777216|16384|1073741824|
|Uso Geral|16|16777216|16384|2147483648|
|Uso Geral|32|16777216|16384|4294967295|
|Uso Geral|64|16777216|16384|4294967295|
|Otimizado para memória|2|16777216|16384|536870912|
|Otimizado para memória|4|16777216|16384|1073741824|
|Otimizado para memória|8|16777216|16384|2147483648|
|Otimizado para memória|16|16777216|16384|4294967295|
|Otimizado para memória|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

O cache de consulta é desativado por padrão. Para habilitar o cache de consulta, configure o parâmetro `query_cache_type`. 

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) para saber mais sobre esse parâmetro.

> [!NOTE]
> O cache de consulta foi preterido a partir do MySQL 5.7.20 e foi removido no MySQL 8.0

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|* * Valor máximo * *|
|---|---|---|---|---|
|Basic|1|Não configurável na camada básica|N/D|N/D|
|Basic|2|Não configurável na camada básica|N/D|N/D|
|Uso Geral|2|0|0|16777216|
|Uso Geral|4|0|0|33554432|
|Uso Geral|8|0|0|67108864|
|Uso Geral|16|0|0|134217728|
|Uso Geral|32|0|0|134217728|
|Uso Geral|64|0|0|134217728|
|Otimizado para memória|2|0|0|33554432|
|Otimizado para memória|4|0|0|67108864|
|Otimizado para memória|8|0|0|134217728|
|Otimizado para memória|16|0|0|134217728|
|Otimizado para memória|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

O lower_case_table_name é definido como 1 por padrão e você pode atualizar esse parâmetro no MySQL 5,6 e no MySQL 5,7

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) para saber mais sobre esse parâmetro.

> [!NOTE]
> No MySQL 8,0, o lower_case_table_name é definido como 1 por padrão e você não pode alterá-lo.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Se você receber um erro semelhante a "tamanho de linha muito grande (> 8126)", talvez queira desativar o parâmetro **innodb_strict_mode**. O parâmetro do servidor **innodb_strict_mode** não pode ser modificado globalmente no nível do servidor porque se o tamanho dos dados da linha for maior que 8K, os dados serão truncados sem um erro que leva à perda potencial de dados. É recomendável modificar o esquema para se ajustar ao limite de tamanho de página. 

Esse parâmetro pode ser definido em um nível de sessão usando `init_connect` . Para definir **innodb_strict_mode** no nível de sessão, consulte [parâmetro de configuração não listado](./howto-server-parameters.md#setting-parameters-not-listed).

> [!NOTE]
> Se você tiver um servidor de réplica de leitura, definir **innodb_strict_mode** como desativado no nível de sessão em um servidor de origem interromperá a replicação. Sugerimos manter o parâmetro definido como OFF se você tiver réplicas de leitura.

### <a name="sort_buffer_size"></a>sort_buffer_size

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) para saber mais sobre esse parâmetro.

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Basic|1|Não configurável na camada básica|N/D|N/D|
|Basic|2|Não configurável na camada básica|N/D|N/D|
|Uso Geral|2|524288|32768|4194304|
|Uso Geral|4|524288|32768|8388608|
|Uso Geral|8|524288|32768|16777216|
|Uso Geral|16|524288|32768|33554432|
|Uso Geral|32|524288|32768|33554432|
|Uso Geral|64|524288|32768|33554432|
|Otimizado para memória|2|524288|32768|8388608|
|Otimizado para memória|4|524288|32768|16777216|
|Otimizado para memória|8|524288|32768|33554432|
|Otimizado para memória|16|524288|32768|33554432|
|Otimizado para memória|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) para saber mais sobre esse parâmetro.

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Basic|1|Não configurável na camada básica|N/D|N/D|
|Basic|2|Não configurável na camada básica|N/D|N/D|
|Uso Geral|2|16777216|1024|67108864|
|Uso Geral|4|16777216|1024|134217728|
|Uso Geral|8|16777216|1024|268435456|
|Uso Geral|16|16777216|1024|536870912|
|Uso Geral|32|16777216|1024|1073741824|
|Uso Geral|64|16777216|1024|1073741824|
|Otimizado para memória|2|16777216|1024|134217728|
|Otimizado para memória|4|16777216|1024|268435456|
|Otimizado para memória|8|16777216|1024|536870912|
|Otimizado para memória|16|16777216|1024|1073741824|
|Otimizado para memória|32|16777216|1024|1073741824|

### <a name="innodb-buffer-pool-warmup"></a>Pool de buffers InnoDB aquecimento
Depois de reiniciar o banco de dados do Azure para o servidor MySQL, as páginas que residem no disco são carregadas conforme as tabelas são consultadas. Isso leva a uma maior latência e desempenho mais lento para a primeira execução das consultas. Isso pode não ser aceitável para cargas de trabalho sensíveis à latência. Usar o pool de buffers InnoDB aquecimento reduz o período de aquecimento recarregando páginas de disco que estavam no pool de buffers antes da reinicialização em vez de esperar que as operações DML ou SELECT acessem as linhas correspondentes.

Você pode reduzir o período de aquecimento após reiniciar o banco de dados do Azure para o servidor MySQL, que representa uma vantagem de desempenho Configurando os [parâmetros do servidor do pool de buffers do InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). O InnoDB salva uma porcentagem das páginas usadas mais recentemente para cada pool de buffers no desligamento do servidor e restaura essas páginas na inicialização do servidor.

Também é importante observar que o desempenho aprimorado é a despesa de tempo de inicialização mais longo para o servidor. Quando esse parâmetro é habilitado, espera-se que a inicialização do servidor e o tempo de reinicialização aumentem dependendo do IOPS provisionado no servidor. É recomendável testar e monitorar o tempo de reinicialização para garantir que o desempenho de inicialização/reinicialização seja aceitável, pois o servidor não está disponível durante esse tempo. Não é recomendável usar esse parâmetro quando o IOPS provisionado for menor que 1000 IOPS (ou em outras palavras, quando o armazenamento provisionado for menor que 335GB.

Para salvar o estado do pool de buffers em desligar servidor, defina o parâmetro `innodb_buffer_pool_dump_at_shutdown` de servidor como `ON` . Da mesma forma, defina o parâmetro de servidor `innodb_buffer_pool_load_at_startup` como `ON` para restaurar o estado do pool de buffers na inicialização do servidor. Você pode controlar o impacto na inicialização/reinicialização ao reduzir e ajustar o valor do parâmetro de servidor `innodb_buffer_pool_dump_pct` , por padrão, esse parâmetro é definido como `25` .

> [!Note]
> Os parâmetros aquecimento do pool de buffers InnoDB têm suporte apenas em servidores de armazenamento de uso geral com até 16 TB de armazenamento. Saiba mais sobre [as opções de armazenamento do banco de dados do Azure para MySQL aqui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage).

### <a name="time_zone"></a>time_zone

Após a implantação inicial, um servidor do Azure para MySQL inclui tabelas de sistemas para informações de fuso horário, mas essas tabelas não são populadas. As tabelas de fuso horário podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o Workbench do MySQL. Consulte os artigos [Portal do Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) para saber como chamar o procedimento armazenado e definir os fusos horários globais ou no nível da sessão.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

Os parâmetros de servidor abaixo não são configuráveis no serviço:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table na camada Básica|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|GRÁFICA|
|innodb_log_files_in_group|2|

Outras variáveis não listadas aqui são definidas como os valores padrão do MySQL prontos para uso. Consulte os documentos do MySQL para as versões [8,0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5,7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)e [5,6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) dos valores padrão. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como [configurar parâmetros de servidor usando o portal do Azure](./howto-server-parameters.md)
- Saiba como [configurar parâmetros de servidor usando o CLI do Azure](./howto-configure-server-parameters-using-cli.md)
- Saiba como [configurar parâmetros de servidor usando o PowerShell](./howto-configure-server-parameters-using-powershell.md)
