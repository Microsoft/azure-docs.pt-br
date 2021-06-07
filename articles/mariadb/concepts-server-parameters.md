---
title: Parâmetros do servidor-banco de dados do Azure para MariaDB
description: Este tópico fornece diretrizes para configurar parâmetros de servidor no banco de dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 7797ee9d20b33a25c1b51289036651c7ad9f22a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664139"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Parâmetros de servidor no banco de dados do Azure para MariaDB

Este artigo fornece considerações e diretrizes para configurar parâmetros de servidor no banco de dados do Azure para MariaDB.

## <a name="what-are-server-parameters"></a>O que são parâmetros de servidor? 

O mecanismo MariaDB fornece muitas variáveis/parâmetros de servidor diferentes que podem ser usados para configurar e ajustar o comportamento do mecanismo. Alguns parâmetros podem ser definidos dinamicamente durante o tempo de execução, enquanto outros são "estáticos", exigindo uma reinicialização do servidor para serem aplicados.

O banco de dados do Azure para MariaDB expõe a capacidade de alterar o valor de vários parâmetros do servidor MariaDB usando o [portal do Azure](./howto-server-parameters.md), o [CLI do Azure](./howto-configure-server-parameters-cli.md)e o [PowerShell](./howto-configure-server-parameters-using-powershell.md) para corresponder às necessidades da sua carga de trabalho.

## <a name="configurable-server-parameters"></a>Parâmetros de servidor configuráveis

A lista de parâmetros de servidor com suporte está em constante crescimento. Use a guia parâmetros do servidor na portal do Azure para exibir a lista completa e configurar os valores dos parâmetros do servidor.

Consulte as seguintes seções abaixo para saber mais sobre os limites dos vários parâmetros de servidor mais atualizados. Os limites são determinados pelo tipo de preço e vCores do servidor.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

No banco de dados do Azure para MariaDB, os logs binários estão sempre habilitados (ou seja, `log_bin` é definido como ativado). Caso deseje usar gatilhos, você receberá um erro semelhante a *você não tem o privilégio de superprivilégios e o log binário habilitado (talvez você queira usar a variável menos segura `log_bin_trust_function_creators` )*.

O formato de log binário é sempre uma **linha** e todas as conexões com o servidor **sempre** usam o log binário baseado em linha. Com o log binário baseado em linha, os problemas de segurança não existem e o log binário não pode ser interrompido, portanto, você pode definir com segurança [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) como **true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size) para saber mais sobre esse parâmetro.

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

O MariaDB armazena a tabela InnoDB em espaços de tabela diferentes com base na configuração fornecida durante a criação da tabela. O de [espaço de tabela do sistema](https://mariadb.com/kb/en/innodb-system-tablespaces/) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de tabela de arquivo por tabela](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) contém dados e índices de uma única tabela InnoDB e é armazenado no sistema de arquivos em seu próprio arquivo de dados. Esse comportamento é controlado pelo parâmetro do servidor `innodb_file_per_table`. Definir `innodb_file_per_table` como `OFF` faz com que o InnoDB crie tabelas no espaço de tabela do sistema. Caso contrário, o InnoDB cria tabelas em espaços de tabela de arquivo por tabela.

O Banco de Dados do Azure para MariaDB oferece suporte abrangente, de **1 TB**, em um único arquivo de dados. Se o tamanho do banco de dados for maior que 1 TB, você deverá criar a tabela no espaço de tabela [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table). Se você tiver um tamanho de tabela único maior que 1 TB, deverá usar a tabela de partição.

### <a name="join_buffer_size"></a>join_buffer_size

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) para saber mais sobre esse parâmetro.

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

A criação de novas conexões de cliente com o MariaDB leva tempo e, uma vez estabelecidas, essas conexões ocupam recursos do banco de dados, mesmo quando ociosas. A maioria dos aplicativos solicita muitas conexões de curta duração, o que agrega a essa situação. O resultado é um número menor de recursos disponíveis para sua carga de trabalho real, o que leva à redução do desempenho. Um pool de conexões que diminui as conexões ociosas e reutiliza as conexões existentes ajudará a evitar isso. Para saber mais sobre a configuração do ProxySQL, visite nossa [postagem do blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL é uma ferramenta de comunidade de código aberto. Ela tem suporte da Microsoft em uma base de melhor esforço. Para obter suporte de produção com diretrizes autoritativas, você pode avaliar e entrar em contato com o [suporte ao produto ProxySQL](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) para saber mais sobre esse parâmetro.

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

O cache de consulta é habilitado por padrão no MariaDB com o `have_query_cache` parâmetro. 

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) para saber mais sobre esse parâmetro.

|**Tipo de preço**|**vCore(s)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) para saber mais sobre esse parâmetro.

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

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) para saber mais sobre esse parâmetro.

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

### <a name="time_zone"></a>time_zone

Após a implantação inicial, um servidor do Azure para MariaDB inclui tabelas de sistemas para informações de fuso horário, mas essas tabelas não são populadas. As tabelas de fuso horário podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o Workbench do MySQL. Consulte os artigos [Portal do Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou [CLI do Azure](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) para saber como chamar o procedimento armazenado e definir os fusos horários globais ou no nível da sessão.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

Os parâmetros de servidor abaixo não são configuráveis no serviço:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table na camada Básica|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|GRÁFICA|
|innodb_log_files_in_group|2|

Outros parâmetros de servidor que não estão listados aqui são definidos como seus valores padrão prontos para MariaDB para [MariaDB](https://mariadb.com/kb/en/server-system-variables/).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [configurar parâmetros de servidor usando o portal do Azure](./howto-server-parameters.md)
- Saiba como [configurar parâmetros de servidor usando o CLI do Azure](./howto-configure-server-parameters-cli.md)
- Saiba como [configurar parâmetros de servidor usando o PowerShell](./howto-configure-server-parameters-using-powershell.md)
