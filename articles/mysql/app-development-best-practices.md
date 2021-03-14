---
title: Práticas recomendadas de desenvolvimento de aplicativo-banco de dados do Azure para MySQL
description: Saiba mais sobre as práticas recomendadas para criar um aplicativo usando o banco de dados do Azure para MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 7f98e14c2eae133941f3fc87247a427198ebcdc2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611956"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Melhores práticas para criar um aplicativo com o Banco de Dados do Azure para MySQL 

Aqui estão algumas práticas recomendadas para ajudá-lo a criar um aplicativo pronto para a nuvem usando o banco de dados do Azure para MySQL. Essas práticas recomendadas podem reduzir o tempo de desenvolvimento para seu aplicativo. 

## <a name="configuration-of-application-and-database-resources"></a>Configuração de recursos de aplicativo e banco de dados

### <a name="keep-the-application-and-database-in-the-same-region"></a>Manter o aplicativo e o banco de dados na mesma região
Verifique se todas as suas dependências estão na mesma região ao implantar seu aplicativo no Azure. A difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 

### <a name="keep-your-mysql-server-secure"></a>Mantenha seu servidor MySQL seguro
Configure seu servidor MySQL para ser [seguro](./concepts-security.md) e não acessível publicamente. Use uma destas opções para proteger seu servidor: 
- [Regras de firewall](./concepts-firewall-rules.md)
- [Redes virtuais](./concepts-data-access-and-security-vnet.md) 
- [Link Privado do Azure](./concepts-data-access-security-private-link.md)

Para segurança, você deve sempre se conectar ao servidor MySQL por SSL e configurar seu servidor MySQL e seu aplicativo para usar o TLS 1,2. Consulte [como configurar SSL/TLS](./concepts-ssl-connection-security.md). 

### <a name="tune-your-server-parameters"></a>Ajustar os parâmetros do servidor
Para cargas de trabalho de leitura pesadas que ajustam parâmetros de servidor `tmp_table_size` e `max_heap_table_size` podem ajudar a otimizar o melhor desempenho. Para calcular os valores necessários para essas variáveis, examine o total de valores de memória por conexão e a memória base. A soma dos parâmetros de memória por conexão, exceto `tmp_table_size` , combinada com as contas de memória base para a memória total do servidor.

Para calcular o maior tamanho possível de `tmp_table_size` e `max_heap_table_size` , use a seguinte fórmula:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Memória total indica a quantidade total de memória que o servidor tem em todos os vCores provisionados.  Por exemplo, em um Uso Geral banco de dados do Azure de dois vCore para o servidor MySQL, a memória total será 5 GB * 2. Você pode encontrar mais detalhes sobre a memória para cada camada na documentação do [tipo de preço](./concepts-pricing-tiers.md) .
>
> A memória base indica as variáveis de memória, como `query_cache_size` e `innodb_buffer_pool_size` , que o MySQL irá inicializar e alocar na inicialização do servidor. A memória por conexão, como `sort_buffer_size` e `join_buffer_size` , é a memória que é alocada somente quando uma consulta precisa dela.

### <a name="create-non-admin-users"></a>Criar usuários não administradores 
[Crie usuários não administradores](./howto-create-users.md) para cada banco de dados. Normalmente, os nomes de usuário são identificados como os nomes de banco de dados.

### <a name="reset-your-password"></a>Redefinir sua senha
Você pode [redefinir sua senha](./howto-create-manage-server-portal.md#update-admin-password) para o servidor MySQL usando o portal do Azure. 

Redefinir a senha do servidor para um banco de dados de produção pode desativar seu aplicativo. É uma boa prática redefinir a senha para qualquer carga de trabalho de produção fora do horário de pico para minimizar o impacto nos usuários do seu aplicativo.

## <a name="performance-and-resiliency"></a>Desempenho e resiliência 
Aqui estão algumas ferramentas e práticas que você pode usar para ajudar a depurar problemas de desempenho com seu aplicativo.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Habilitar logs de consulta lentos para identificar problemas de desempenho
Você pode habilitar [logs de consulta lentos](./concepts-server-logs.md) e [logs de auditoria](./concepts-audit-logs.md) em seu servidor. Analisar logs de consulta lentos pode ajudar a identificar gargalos de desempenho para solução de problemas. 

Os logs de auditoria também estão disponíveis por meio de logs de Diagnóstico do Azure em logs de Azure Monitor, hubs de eventos do Azure e contas de armazenamento. Consulte [como solucionar problemas de desempenho de consulta](./howto-troubleshoot-query-performance.md).

### <a name="use-connection-pooling"></a>Usar o pool de conexões
O gerenciamento de conexões de banco de dados pode ter um impacto significativo no desempenho do aplicativo como um todo. Para otimizar o desempenho, você deve reduzir o número de vezes que as conexões são estabelecidas e o tempo para estabelecer conexões em caminhos de código-chave. Use o [pool de conexões](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended) para se conectar ao banco de dados do Azure para MySQL para melhorar a resiliência e o desempenho. 

Você pode usar o pool de conexões do [ProxySQL](https://proxysql.com/) para gerenciar conexões com eficiência. O uso de um pool de conexões pode diminuir as conexões ociosas e reutilizar as conexões existentes, o que ajudará a evitar problemas. Consulte [como configurar o ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) para saber mais. 

### <a name="retry-logic-to-handle-transient-errors"></a>Lógica de repetição para manipular erros transitórios
Seu aplicativo pode apresentar [erros transitórios](./concepts-connectivity.md#handling-transient-errors) em que as conexões com o banco de dados são descartadas ou perdidas intermitentemente. Nessas situações, o servidor está em execução após uma ou duas repetições em 5 a 10 segundos. 

Uma prática recomendada é aguardar 5 segundos antes de sua primeira tentativa. Em seguida, siga cada nova tentativa aumentando a espera gradualmente, até 60 segundos. Limite o número máximo de repetições em que o seu aplicativo considera que a operação falhou, para que você possa investigar ainda mais. Consulte [como solucionar problemas de erros de conexão](./howto-troubleshoot-common-connection-issues.md) para saber mais. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Habilitar a replicação de leitura para mitigar failovers
Você pode usar [replicação de dados](./howto-data-in-replication.md) para cenários de failover. Quando você estiver usando réplicas de leitura, ocorrerá nenhum failover automatizado entre os servidores de origem e de réplica. 

Você perceberá um atraso entre a origem e a réplica porque a replicação é assíncrona. O retardo de rede pode ser influenciado por muitos fatores, como o tamanho da carga de trabalho em execução no servidor de origem e a latência entre datacenters. Na maioria dos casos, a latência de réplica varia de alguns segundos a alguns minutos.

## <a name="database-deployment"></a>Implantação de banco de dados 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Configurar uma tarefa de banco de dados do Azure para MySQL em seu pipeline de implantação de CI/CD
Ocasionalmente, você precisa implantar alterações em seu banco de dados. Nesses casos, você pode usar a CI (integração contínua) e o CD (entrega contínua) por meio de [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) e usar uma tarefa para [o servidor MySQL](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment) para atualizar o banco de dados executando um script personalizado em relação a ele.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Usar um processo efetivo para implantação manual de banco de dados 
Durante a implantação manual do banco de dados, siga estas etapas para minimizar o tempo de inatividade ou reduzir o risco de falha na implantação: 

1. Crie uma cópia de um banco de dados de produção em um novo banco de dados usando o [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) ou o [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html). 
2. Atualize o novo banco de dados com suas novas alterações de esquema ou atualizações necessárias para seu banco de dados. 
3. Coloque o banco de dados de produção em um estado somente leitura. Você não deve ter operações de gravação no banco de dados de produção até que a implantação seja concluída. 
4. Teste seu aplicativo com o banco de dados recentemente atualizado da etapa 1.
5. Implante as alterações do aplicativo e verifique se o aplicativo agora está usando o novo banco de dados que tem as atualizações mais recentes. 
6. Mantenha o banco de dados de produção antigo para que você possa reverter as alterações. Em seguida, você pode avaliar para excluir o banco de dados de produção antigo ou exportá-lo no armazenamento do Azure, se necessário. 

>[!NOTE]
>Se o aplicativo for como um aplicativo de comércio eletrônico e você não puder colocá-lo no estado somente leitura, implante as alterações diretamente no banco de dados de produção depois de fazer um backup. A alteração de alterações deve ocorrer fora do horário de pico, com baixo tráfego para o aplicativo para minimizar o impacto, pois alguns usuários podem ter solicitações com falha. 
>
>Verifique se o código do aplicativo também lida com as solicitações com falha.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Use métricas nativas do MySQL para ver se sua carga de trabalho está excedendo tamanhos de tabela temporária na memória
Com uma carga de trabalho com uso intenso de leitura, as consultas em execução no servidor MySQL podem exceder os tamanhos de tabela temporária na memória. Uma carga de trabalho de leitura intensa pode fazer com que o servidor alterne para a gravação de tabelas temporárias em disco, o que afeta o desempenho do seu aplicativo. Para determinar se o servidor está gravando no disco como resultado da excedente do tamanho de tabela temporário, examine as seguintes métricas:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
A `created_tmp_disk_tables` métrica indica quantas tabelas foram criadas no disco. A `created_tmp_table` métrica informa quantas tabelas temporárias devem ser formadas na memória, considerando sua carga de trabalho. Para determinar se a execução de uma consulta específica usará tabelas temporárias, execute a instrução [explicar](https://dev.mysql.com/doc/refman/8.0/en/explain.html) na consulta. Os detalhes na `extra` coluna indicam `Using temporary` se a consulta será executada usando tabelas temporárias.

Para calcular a porcentagem de sua carga de trabalho com consultas que são despejadas em discos, use os valores de métrica na fórmula a seguir:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Idealmente, esse percentual deve ser inferior a 25%. Se você perceber que a porcentagem é 25% ou maior, sugerimos modificar dois parâmetros de servidor, tmp_table_size e max_heap_table_size.

## <a name="database-schema-and-queries"></a>Esquema e consultas de banco de dados

Aqui estão algumas dicas para ter em mente quando você cria seu esquema de banco de dados e suas consultas.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Usar o tipo de dados correto para as colunas da tabela
O uso do tipo de dados correto com base no tipo de dado que você deseja armazenar pode otimizar o armazenamento e reduzir os erros que podem ocorrer devido a tipos incorretos.

### <a name="use-indexes"></a>Usar índices
Para evitar consultas lentas, você pode usar índices. Os índices podem ajudar a localizar linhas com colunas específicas rapidamente. Consulte [como usar índices no MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>Use explicar para suas consultas SELECT
Use a `EXPLAIN` instrução para obter informações sobre o que o MySQL está fazendo para executar sua consulta. Ele pode ajudá-lo a detectar gargalos ou problemas com sua consulta. Consulte [como usar explicar para analisar o desempenho da consulta](./howto-troubleshoot-query-performance.md).