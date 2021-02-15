---
title: Parâmetros do servidor – banco de dados do Azure para MySQL-servidor flexível
description: Este tópico fornece diretrizes para configurar parâmetros de servidor no banco de dados do Azure para MySQL-servidor flexível.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 58978f120578afeca129b0d8928713835def8418
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496471"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Parâmetros de servidor no banco de dados do Azure para MySQL – servidor flexível

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este artigo fornece considerações e diretrizes para configurar parâmetros de servidor no banco de dados do Azure para MySQL servidor flexível.

## <a name="what-are-server-variables"></a>O que são variáveis de servidor? 

O mecanismo MySQL fornece várias [variáveis/parâmetros de servidor](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) diferentes que podem ser usados para configurar e ajustar o comportamento do mecanismo. Alguns parâmetros podem ser definidos dinamicamente durante o tempo de execução, enquanto outros são "estáticos", exigindo uma reinicialização do servidor para serem aplicados.

O banco de dados do Azure para MySQL servidor flexível expõe a capacidade de alterar o valor de vários parâmetros do servidor MySQL usando o [portal do Azure](./how-to-configure-server-parameters-portal.md) e [CLI do Azure](./how-to-configure-server-parameters-cli.md) para corresponder às necessidades da sua carga de trabalho.

## <a name="configurable-server-parameters"></a>Parâmetros de servidor configuráveis

Você pode gerenciar a configuração de servidor flexível do banco de dados do Azure para MySQL usando parâmetros de servidor. Os parâmetros de servidor são configurados com o valor padrão e recomendado quando você cria o servidor. A folha parâmetro de servidor no portal do Azure mostra os parâmetros de servidor modificáveis e não modificáveis. Os parâmetros de servidor não modificáveis estão esmaecidos.

A lista de parâmetros de servidor com suporte está em constante crescimento. Use a guia parâmetros do servidor na portal do Azure para exibir a lista completa e configurar os valores dos parâmetros do servidor.

Consulte as seguintes seções abaixo para saber mais sobre os limites dos vários parâmetros de servidor mais atualizados. Os limites são determinados pela camada de computação e pelo tamanho (vCores) do servidor.

> [!NOTE]
> Se você estiver procurando modificar um parâmetro de servidor que não pode ser modificado, mas quiser ver como modificável para seu ambiente, abra um item do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) ou vote se os comentários já existirem, o que pode nos ajudar a priorizar.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

No banco de dados do Azure para MySQL servidor flexível, os logs binários são sempre habilitados (ou seja, `log_bin` é definido como ativado). Caso deseje usar gatilhos, você receberá um erro semelhante a *você não tem o privilégio de superprivilégios e o log binário habilitado (talvez você queira usar a variável menos segura `log_bin_trust_function_creators` )*. 

O formato de log binário é sempre uma **linha** e todas as conexões com o servidor **sempre** usam o log binário baseado em linha. Com o log binário baseado em linha, os problemas de segurança não existem e o log binário não pode ser interrompido, portanto, você pode definir com segurança [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) como **true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) para saber mais sobre esse parâmetro.

|**Tipo de preço**|**vCore(s)**|**Tamanho da memória (GiB)**|**Valor padrão (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|---|
|Expansível (B1s)|1|1|134217728|33554432|134217728|
|Expansível (B1ms)|1|2|536870912|134217728|536870912|
|Expansíveis|2|4|2147483648|134217728|2147483648|
|Uso Geral|2|8|6442450944|134217728|6442450944|
|Uso Geral|4|16|12884901888|134217728|12884901888|
|Uso Geral|8|32|25769803776|134217728|25769803776|
|Uso Geral|16|64|51539607552|134217728|51539607552|
|Uso Geral|32|128|103079215104|134217728|103079215104|
|Uso Geral|48|192|154618822656|134217728|154618822656|
|Uso Geral|64|256|206158430208|134217728|206158430208|
|Otimizado para memória|2|16|12884901888|134217728|12884901888|
|Otimizado para memória|4|32|25769803776|134217728|25769803776|
|Otimizado para memória|8|64|51539607552|134217728|51539607552|
|Otimizado para memória|16|128|103079215104|134217728|103079215104|
|Otimizado para memória|32|256|206158430208|134217728|206158430208|
|Otimizado para memória|48|384|309237645312|134217728|309237645312|
|Otimizado para memória|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

O MySQL armazena a tabela InnoDB em espaços de tabela diferentes com base na configuração fornecida durante a criação da tabela. O [espaço de tabela do sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de tabela de arquivo por tabela](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contém dados e índices de uma única tabela InnoDB e é armazenado no sistema de arquivos em seu próprio arquivo de dados. Esse comportamento é controlado pelo parâmetro do servidor `innodb_file_per_table`. Definir `innodb_file_per_table` como `OFF` faz com que o InnoDB crie tabelas no espaço de tabela do sistema. Caso contrário, o InnoDB cria tabelas em espaços de tabela de arquivo por tabela.

O servidor flexível do banco de dados do Azure para MySQL oferece suporte a, no máximo, **4 TB** , em um único arquivo de dados. Se o tamanho do seu banco de dados for maior do que 4 TB, você deverá criar a tabela no [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tablespace. Se você tiver um tamanho de tabela único maior que 4 TB, deverá usar a tabela de partição.

### <a name="max_connections"></a>max_connections

O valor de max_connection é determinado pelo tamanho da memória do servidor. 

|**Tipo de preço**|**vCore(s)**|**Tamanho da memória (GiB)**|**Valor padrão**|**Valor mínimo**|**Valor máximo**|
|---|---|---|---|---|---|
|Expansível (B1s)|1|1|85|10|171|
|Expansível (B1ms)|1|2|171|10|341|
|Expansíveis|2|4|341|10|683|
|Uso Geral|2|8|683|10|1365|
|Uso Geral|4|16|1365|10|2731|
|Uso Geral|8|32|2731|10|5461|
|Uso Geral|16|64|5461|10|10923|
|Uso Geral|32|128|10923|10|21845|
|Uso Geral|48|192|16384|10|32768|
|Uso Geral|64|256|21845|10|43691|
|Otimizado para memória|2|16|1365|10|2731|
|Otimizado para memória|4|32|2731|10|5461|
|Otimizado para memória|8|64|5461|10|10923|
|Otimizado para memória|16|128|10923|10|21845|
|Otimizado para memória|32|256|21845|10|43691|
|Otimizado para memória|48|384|32768|10|65536|
|Otimizado para memória|64|504|43008|10|86016|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> ERRO 1040 (08004): Muitas conexões

> [!IMPORTANT]
> Para obter a melhor experiência, recomendamos usar um pool de conexões como o ProxySQL para gerenciar conexões com eficiência.

A criação de novas conexões de cliente com o MySQL leva tempo e, uma vez estabelecidas, essas conexões ocupam recursos do banco de dados, mesmo quando ociosas. A maioria dos aplicativos solicita muitas conexões de curta duração, o que agrega a essa situação. O resultado é um número menor de recursos disponíveis para sua carga de trabalho real, o que leva à redução do desempenho. Um pool de conexões que diminui as conexões ociosas e reutiliza as conexões existentes ajudará a evitar isso. Para saber mais sobre a configuração do ProxySQL, visite nossa [postagem do blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL é uma ferramenta de comunidade de código aberto. Ela tem suporte da Microsoft em uma base de melhor esforço. Para obter suporte de produção com diretrizes autoritativas, você pode avaliar e entrar em contato com o [suporte ao produto ProxySQL](https://proxysql.com/services/support/).

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Se você receber um erro semelhante a "tamanho de linha muito grande (> 8126)", talvez queira desativar o parâmetro **innodb_strict_mode**. O parâmetro do servidor **innodb_strict_mode** não pode ser modificado globalmente no nível do servidor, pois se o tamanho dos dados da linha for maior que 8K, os dados serão truncados sem um erro, o que pode levar a uma possível perda de dados. É recomendável modificar o esquema para se ajustar ao limite de tamanho de página. 

Esse parâmetro pode ser definido em um nível de sessão usando `init_connect` . Para definir **innodb_strict_mode** no nível de sessão, consulte [parâmetro de configuração não listado](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters).

> [!NOTE]
> Se você tiver um servidor de réplica de leitura, definir **innodb_strict_mode** como desativado no nível de sessão em um servidor de origem interromperá a replicação. Sugerimos manter o parâmetro definido como OFF se você tiver réplicas de leitura.

### <a name="time_zone"></a>time_zone

Após a implantação inicial, um servidor flexível do Azure para MySQL inclui tabelas do sistema para informações de fuso horário, mas essas tabelas não são populadas. As tabelas de fuso horário podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o Workbench do MySQL. Consulte os artigos [Portal do Azure](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) ou [CLI do Azure](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) para saber como chamar o procedimento armazenado e definir os fusos horários globais ou no nível da sessão.

## <a name="non-modifiable-server-parameters"></a>Parâmetros de servidor não modificáveis

A folha parâmetro de servidor na portal do Azure mostra os parâmetros de servidor modificáveis e não modificáveis. Os parâmetros de servidor não modificáveis estão esmaecidos. Se você quiser configurar um parâmetro de servidor não modificável no nível de sessão, consulte o artigo [portal do Azure](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) ou [CLI do Azure](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) para definir o parâmetro no nível de conexão usando `init_connect` .

## <a name="next-steps"></a>Próximas etapas

- Como configurar [parâmetros de servidor no Portal do Azure](./how-to-configure-server-parameters-portal.md)
- Como configurar [parâmetros de servidor no CLI do Azure](./how-to-configure-server-parameters-cli.md)
