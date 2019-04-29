---
title: Réplicas de leitura no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve o recurso de réplica de leitura no Banco de Dados do Azure para PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 04/01/2019
ms.date: 04/22/2019
ms.openlocfilehash: f340f1e42b6993a1f834ab05570c669d4241222b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564350"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Réplicas de leitura no Banco de Dados do Azure para PostgreSQL

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para PostgreSQL para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas dentro da mesma região do Azure. Réplicas são atualizadas de forma assíncrona com a tecnologia de replicação nativa do mecanismo PostgreSQL.

As réplicas são novos servidores que você gerencia de modo similar ao Banco de Dados do Azure para PostgreSQL normal. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Saiba como [criar e gerenciar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do PostgreSQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="create-a-replica"></a>Criar uma réplica
O servidor mestre deve ter o parâmetro `azure.replication_support` definido como **REPLICA**. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. (O parâmetro `azure.replication_support` se aplica somente às camadas de Uso Geral e Otimizada para Memória.)

Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para PostgreSQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

O recurso de réplica de leitura usa a replicação física do PostgreSQL, e não a replicação lógica. A replicação de streaming usando slots de replicação é o modo de operação padrão. Quando necessário, o envio de logs é usado para recuperar o atraso.

> [!NOTE]
> Se você não tem alerta de armazenamento configurado em seus servidores, recomendamos que você faça isso. O alerta informa quando um servidor está se aproximando de seu limite de armazenamento, o que afetará a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica
Quando você cria uma réplica, ela não herda as regras de firewall nem o ponto de extremidade de serviço de rede virtual do servidor mestre. Essas regras precisam ser configuradas independentemente da réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para PostgreSQL. Para um servidor chamado **myreplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando o psql:

```
psql -h myreplica.postgres.database.chinacloudapi.cn -U myadmin@myreplica -d postgres
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação
O Banco de Dados do Azure para PostgreSQL fornece a métrica **Máximo de Latência Entre Réplicas** no Azure Monitor. Essa métrica está disponível apenas no servidor mestre. A métrica mostra o tempo de retardo entre o mestre e a réplica com o maior retardo. 

O Banco de Dados do Azure para PostgreSQL também fornece a métrica **Latência de Réplica** no Azure Monitor. Essa métrica está disponível apenas para réplicas. 

A métrica é calculada pela exibição `pg_stat_wal_receiver`:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```

A métrica Retardo da Réplica mostra o tempo decorrido desde a última transação reproduzida. Se não houver nenhuma transação ocorrendo no servidor mestre, a métrica refletirá esse retardo.

Defina um alerta para informá-lo quando o retardo de réplica atinge um valor que não é aceitável para sua carga de trabalho. 

Para obter mais informações, consulte diretamente o servidor mestre para conhecer o retardo de replicação em bytes em todas as réplicas.

No PostgreSQL versão 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

No PostgreSQL versão 9.6 e versões anteriores:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Se um servidor mestre ou a réplica de leitura for reiniciado, o tempo necessário para reiniciar e pegar o ritmo será refletido na métrica de Retardo de Réplica.

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um mestre e uma réplica. A ação de interrupção faz com que a réplica seja reinicia e remova suas configurações de replicação. Após a replicação ser interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não alcança o servidor mestre.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).


## <a name="considerations"></a>Considerações

Esta seção resume as considerações sobre o recurso de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma réplica de leitura, o parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. O parâmetro `azure.replication_support` se aplica somente às camadas de Uso Geral e Otimizada para Memória.

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para PostgreSQL. Um servidor existente não pode se tornar uma réplica. Uma réplica de leitura pode ser criadas apenas na mesma região do Azure que o mestre. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica
Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

PostgreSQL requer que o valor do parâmetro `max_connections` na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. No Banco de Dados do Azure para PostgreSQL, o valor do parâmetro `max_connections` se baseia na SKU. Para obter mais informações, confira [Limites no Banco de Dados do Azure para PostgreSQL](concepts-limits.md). 

Se você tentar atualizar os valores do servidor, mas não cumprir os limites, receberá um erro.

### <a name="stopped-replicas"></a>Réplicas paradas
Se você interromper a replicação entre um servidor mestre e uma réplica de leitura, a réplica será reiniciada para aplicar a alteração. A réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos
Quando um servidor mestre é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas são reiniciadas para refletir essa alteração.

## <a name="next-steps"></a>Próximas etapas
Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
