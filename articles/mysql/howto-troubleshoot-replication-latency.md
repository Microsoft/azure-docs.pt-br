---
title: Solucionar problemas de latência de replicação-banco de dados do Azure para MySQL
description: Saiba como solucionar problemas de latência de replicação usando réplicas de leitura do banco de dados do Azure para MySQL.
keywords: MySQL, solucionar problemas, latência de replicação em segundos
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: 92513a8c24b5106e3a59c8cfa4d743e900b957bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249764"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Solucionar problemas de latência de replicação no Banco de Dados do Azure para MySQL

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

O recurso de [réplica de leitura](concepts-read-replicas.md) permite replicar dados de um servidor do Azure para MySQL para um servidor de réplica somente leitura. Você pode escalar horizontalmente as cargas de trabalho roteando consultas de leitura e relatório do aplicativo para servidores de réplica. Essa configuração reduz a pressão no servidor de origem. Ele também melhora o desempenho geral e a latência do aplicativo conforme ele é dimensionado.

As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição de arquivo binlog (log binário nativo) do mecanismo MySQL. Para obter mais informações, consulte [visão geral da configuração de replicação baseada em posição do arquivo MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

O atraso de replicação nas réplicas de leitura secundárias depende de vários fatores. Esses fatores incluem, mas não estão limitados a:

- Latência de rede.
- Volume da transação no servidor de origem.
- Camada de computação do servidor de origem e do servidor de réplica de leitura secundário.
- Consultas em execução no servidor de origem e no servidor secundário.

Neste artigo, você aprenderá a solucionar problemas de latência de replicação no banco de dados do Azure para MySQL. Você também entenderá algumas causas comuns da maior latência de replicação em servidores de réplica.

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.
>

## <a name="replication-concepts"></a>Conceitos de replicação

Quando um log binário é habilitado, o servidor de origem grava transações confirmadas no log binário. O log binário é usado para replicação. Ele é ativado por padrão para todos os servidores provisionados recentemente que dão suporte a até 16 TB de armazenamento. Em servidores de réplica, dois threads são executados em cada servidor de réplica. Um thread é o *thread de e/s* e o outro é o *thread do SQL*:

- O thread de e/s conecta-se ao servidor de origem e solicita logs binários atualizados. Esse thread recebe as atualizações de log binários. Essas atualizações são salvas em um servidor de réplica, em um log local chamado *log de retransmissão*.
- O thread SQL lê o log de retransmissão e, em seguida, aplica as alterações de dados nos servidores de réplica.

## <a name="monitoring-replication-latency"></a>Monitorando latência de replicação

O banco de dados do Azure para MySQL fornece a métrica para o atraso de replicação em segundos no [Azure monitor](concepts-monitoring.md). Essa métrica está disponível somente em servidores de réplica de leitura. Ele é calculado pela métrica de seconds_behind_master que está disponível no MySQL. 

Para entender a causa da latência de replicação maior, conecte-se ao servidor de réplica usando o [MySQL Workbench](connect-workbench.md) ou o [Azure cloud Shell](https://shell.azure.com). Em seguida, execute o comando a seguir.

>[!NOTE]
> Em seu código, substitua os valores de exemplo com o nome do servidor de réplica e o nome de usuário do administrador. O nome de usuário do administrador requer o `@\<servername>` banco de dados do Azure para MySQL.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Veja como é a experiência no terminal do Cloud Shell:

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

No mesmo Cloud Shell terminal, execute o seguinte comando:

```
mysql> SHOW SLAVE STATUS;
```

Veja uma saída típica:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorando latência de replicação":::

A saída contém muitas informações. Normalmente, você precisa se concentrar apenas nas linhas descritas na tabela a seguir.

|Métrica|Descrição|
|---|---|
|Slave_IO_State| Representa o status atual do thread de e/s. Normalmente, o status é "aguardando o mestre enviar evento" se o servidor de origem (Mestre) estiver sincronizando. Um status como "conectando-se ao mestre" indica que a réplica perdeu a conexão com o servidor de origem. Verifique se o servidor de origem está em execução ou verifique se um firewall está bloqueando a conexão.|
|Master_Log_File| Representa o arquivo de log binário no qual o servidor de origem está gravando.|
|Read_Master_Log_Pos| Indica onde o servidor de origem está gravando no arquivo de log binário.|
|Relay_Master_Log_File| Representa o arquivo de log binário que o servidor de réplica está lendo do servidor de origem.|
|Slave_IO_Running| Indica se o thread de e/s está em execução. O valor deve ser `Yes` . Se o valor for `NO` , a replicação provavelmente será interrompida.|
|Slave_SQL_Running| Indica se o thread SQL está em execução. O valor deve ser `Yes` . Se o valor for `NO` , a replicação provavelmente será interrompida.|
|Exec_Master_Log_Pos| Indica a posição do Relay_Master_Log_File que a réplica está aplicando. Se houver latência, essa sequência de posição deverá ser menor que Read_Master_Log_Pos.|
|Relay_Log_Space|Indica o limite superior do tamanho do log de retransmissão. Você pode verificar o tamanho consultando `SHOW GLOBAL VARIABLES` como `relay_log_space_limit` .|
|Seconds_Behind_Master| Exibe a latência de replicação em segundos.|
|Last_IO_Errno|Exibe o código de erro de thread de e/s, se houver. Para obter mais informações sobre esses códigos, consulte a [referência da mensagem de erro do MySQL Server](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Exibe a mensagem de erro de thread de e/s, se houver.|
|Last_SQL_Errno|Exibe o código de erro de thread do SQL, se houver. Para obter mais informações sobre esses códigos, consulte a [referência da mensagem de erro do MySQL Server](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Exibe a mensagem de erro de thread do SQL, se houver.|
|Slave_SQL_Running_State| Indica o status atual do thread SQL. Nesse estado, `System lock` é normal. Também é normal ver um status de `Waiting for dependent transaction to commit` . Esse status indica que a réplica está aguardando que o servidor de origem atualize as transações confirmadas.|

Se Slave_IO_Running for `Yes` e Slave_SQL_Running for `Yes` , a replicação estará funcionando corretamente. 

Em seguida, verifique Last_IO_Errno, Last_IO_Error, Last_SQL_Errno e Last_SQL_Error.  Esses campos exibem o número do erro e a mensagem de erro do erro mais recente que causou a interrupção do thread do SQL. Um número de erro `0` e uma mensagem vazia significam que não há erro. Investigue qualquer valor de erro diferente de zero verificando o código de erro na [referência de mensagem de erro do servidor MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Cenários comuns para alta latência de replicação

As seções a seguir abordam cenários em que alta latência de replicação é comum.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Latência de rede ou alto consumo de CPU no servidor de origem

Se você vir os valores a seguir, a latência de replicação provavelmente será causada por alta latência de rede ou alto consumo de CPU no servidor de origem.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

Nesse caso, o thread de e/s está em execução e está aguardando o servidor de origem. O servidor de origem já foi gravado no número 20 do arquivo de log binário. A réplica recebeu apenas até o número de arquivo 10. Os principais fatores para a alta latência de replicação nesse cenário são a velocidade da rede ou a alta utilização da CPU no servidor de origem.  

No Azure, a latência de rede em uma região normalmente pode ser medida em milissegundos. Entre regiões, a latência varia de milissegundos a segundos.

Na maioria dos casos, o atraso de conexão entre os threads de e/s e o servidor de origem é causado pela alta utilização da CPU no servidor de origem. Os threads de e/s são processados lentamente. Você pode detectar esse problema usando Azure Monitor para verificar a utilização da CPU e o número de conexões simultâneas no servidor de origem.

Se você não vir a alta utilização da CPU no servidor de origem, o problema poderá ser latência de rede. Se a latência de rede for repentinamente alta, verifique a [página de status do Azure](https://status.azure.com/status) para problemas ou interrupções conhecidas. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Intermitências pesadas de transações no servidor de origem

Se você vir os valores a seguir, uma intermitência pesada de transações no servidor de origem provavelmente está causando a latência de replicação. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

A saída mostra que a réplica pode recuperar o log binário por trás do servidor de origem. Mas o thread de e/s de réplica indica que o espaço de log de retransmissão já está cheio.

A velocidade da rede não está causando o atraso. A réplica está tentando acompanhar. Mas o tamanho do log binário atualizado excede o limite superior do espaço de log de retransmissão.

Para solucionar esse problema, habilite o [log de consultas lentas](concepts-server-logs.md) no servidor de origem. Use logs de consulta lentos para identificar transações de longa execução no servidor de origem. Em seguida, ajuste as consultas identificadas para reduzir a latência no servidor. 

A latência de replicação dessa classificação é normalmente causada pela carga de dados no servidor de origem. Quando os servidores de origem têm carregamentos de dados semanais ou mensais, a latência de replicação é infelizmente inevitável. Os servidores de réplica eventualmente são atualizados após a conclusão do carregamento de dados no servidor de origem.

### <a name="slowness-on-the-replica-server"></a>Lentidão no servidor de réplica

Se você observar os valores a seguir, o problema poderá estar no servidor de réplica.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Nesse cenário, a saída mostra que o thread de e/s e o thread do SQL estão funcionando bem. A réplica lê o mesmo arquivo de log binário que o servidor de origem grava. No entanto, alguma latência no servidor de réplica reflete a mesma transação do servidor de origem.

As seções a seguir descrevem as causas comuns desse tipo de latência.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Nenhuma chave primária ou chave exclusiva em uma tabela

O banco de dados do Azure para MySQL usa replicação baseada em linha. O servidor de origem grava eventos no log binário, registrando alterações em linhas de tabela individuais. Em seguida, o thread SQL Replica essas alterações nas linhas da tabela correspondentes no servidor de réplica. Quando uma tabela não tem uma chave primária ou chave exclusiva, o thread do SQL examina todas as linhas na tabela de destino para aplicar as alterações. Essa verificação pode causar latência de replicação.

No MySQL, a chave primária é um índice associado que garante um desempenho de consulta rápido, pois não pode incluir valores nulos. Se você usar o mecanismo de armazenamento InnoDB, os dados da tabela serão organizados fisicamente para fazer pesquisas e classificações extremamente rápidas com base na chave primária.

É recomendável que você adicione uma chave primária em tabelas no servidor de origem antes de criar o servidor de réplica. Adicione chaves primárias no servidor de origem e recrie réplicas de leitura para ajudar a melhorar a latência de replicação.

Use a consulta a seguir para descobrir quais tabelas estão faltando uma chave primária no servidor de origem:

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>Consultas de execução longa no servidor de réplica

A carga de trabalho no servidor de réplica pode fazer o atraso de thread do SQL por trás do thread de e/s. As consultas de execução longa no servidor de réplica são uma das causas comuns da alta latência de replicação. Para solucionar esse problema, habilite o [log de consultas lentas](concepts-server-logs.md) no servidor de réplica.

Consultas lentas podem aumentar o consumo de recursos ou reduzir o servidor para que a réplica não possa ser atualizada com o servidor de origem. Nesse cenário, ajuste as consultas lentas. Consultas mais rápidas impedem o bloqueio do thread do SQL e melhoram significativamente a latência de replicação.

#### <a name="ddl-queries-on-the-source-server"></a>Consultas DDL no servidor de origem

No servidor de origem, um comando DDL (linguagem de definição de dados) como [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) pode levar muito tempo. Enquanto o comando DDL está em execução, milhares de outras consultas podem estar em execução em paralelo no servidor de origem. 

Quando o DDL é replicado, para garantir a consistência do banco de dados, o mecanismo do MySQL executa o DDL em um único thread de replicação. Durante essa tarefa, todas as outras consultas replicadas são bloqueadas e devem aguardar até que a operação DDL seja concluída no servidor de réplica. Até mesmo operações DDL online causam esse atraso. As operações DDL aumentam a latência de replicação.

Se você habilitou o [log de consultas lentas](concepts-server-logs.md) no servidor de origem, poderá detectar esse problema de latência verificando um comando DDL executado no servidor de origem. Por meio do descarte de índice, renomeação e criação, você pode usar o algoritmo local para ALTER TABLE. Talvez seja necessário copiar os dados da tabela e recriar a tabela.

Normalmente, o DML simultâneo tem suporte para o algoritmo local. Mas você pode rapidamente fazer um bloqueio de metadados exclusivo na tabela ao preparar e executar a operação. Portanto, para a instrução CREATE INDEX, você pode usar o algoritmo de cláusulas e o bloqueio para influenciar o método de cópia de tabela e o nível de simultaneidade para leitura e gravação. Você ainda pode impedir operações DML adicionando um índice de texto completo ou um índice espacial.

O exemplo a seguir cria um índice usando cláusulas de algoritmo e LOCK.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Infelizmente, para uma instrução DDL que requer um bloqueio, não é possível evitar a latência de replicação. Para reduzir os possíveis efeitos, faça esses tipos de operações DDL fora do horário de pico, por exemplo, durante a noite.

#### <a name="downgraded-replica-server"></a>Servidor de réplica de downgrade

No banco de dados do Azure para MySQL, as réplicas de leitura usam a mesma configuração de servidor que o servidor de origem. Você pode alterar a configuração do servidor de réplica depois que ele tiver sido criado.

Se o servidor de réplica for desatualizado, a carga de trabalho poderá consumir mais recursos, o que, por sua vez, poderá levar à latência de replicação. Para detectar esse problema, use Azure Monitor para verificar o consumo de CPU e de memória do servidor de réplica.

Nesse cenário, recomendamos que você mantenha a configuração do servidor de réplica em valores iguais ou maiores que os valores do servidor de origem. Essa configuração permite que a réplica acompanhe o servidor de origem.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Melhorando a latência de replicação ajustando os parâmetros do servidor de origem

No banco de dados do Azure para MySQL, por padrão, a replicação é otimizada para ser executada com threads paralelos em réplicas. Quando cargas de trabalho de alta simultaneidade no servidor de origem fazem com que o servidor de réplica fique atrás, você pode melhorar a latência de replicação Configurando o parâmetro binlog_group_commit_sync_delay no servidor de origem.

O parâmetro binlog_group_commit_sync_delay controla o número de microssegundos que a confirmação de log binário aguarda antes de sincronizar o arquivo de log binário. O benefício desse parâmetro é que, em vez de aplicar imediatamente cada transação confirmada, o servidor de origem envia as atualizações de log binários em massa. Esse atraso reduz a e/s na réplica e ajuda a melhorar o desempenho.

Pode ser útil definir o parâmetro binlog_group_commit_sync_delay como 1000 ou assim por diante. Em seguida, monitore a latência de replicação. Defina esse parâmetro com cuidado e use-o apenas para cargas de trabalho de alta simultaneidade.

> [!IMPORTANT]
> No servidor de réplica, é recomendável que binlog_group_commit_sync_delay parâmetro seja 0. Isso é recomendado porque, diferentemente do servidor de origem, o servidor de réplica não terá alta simultaneidade e o aumento do valor para binlog_group_commit_sync_delay no servidor de réplica poderá inadvertidamente aumentar o aumento de replicação.

Para cargas de trabalho de baixa simultaneidade que incluem muitas transações singleton, a configuração binlog_group_commit_sync_delay pode aumentar a latência. A latência pode aumentar porque o thread de e/s aguarda atualizações de log binário em massa, mesmo que apenas algumas transações sejam confirmadas.

## <a name="next-steps"></a>Próximas etapas

Confira a [visão geral da replicação do MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
