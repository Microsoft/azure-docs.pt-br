---
title: Réplicas de leitura no banco de dados do Azure para PostgreSQL – servidor único
description: Este artigo descreve o recurso de réplica de leitura no banco de dados do Azure para PostgreSQL – servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/14/2019
ms.openlocfilehash: c98247b0ba8b670a59dec9aa3ec87e949f1dda78
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147937"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Réplicas de leitura no banco de dados do Azure para PostgreSQL – servidor único

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para PostgreSQL para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas. Réplicas são atualizadas de forma assíncrona com a tecnologia de replicação nativa do mecanismo PostgreSQL.

> [!IMPORTANT]
> Você pode criar uma réplica de leitura na mesma região do seu servidor mestre, ou em qualquer outra região do Azure de sua escolha. A replicação entre regiões está atualmente em visualização pública.

As réplicas são novos servidores que você gerencia de modo similar ao Banco de Dados do Azure para PostgreSQL normal. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Saiba como [criar e gerenciar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do PostgreSQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

Leitura de réplicas podem aprimorar seu plano de recuperação de desastres. Primeiro você precisa ter uma réplica em uma região do Azure diferente do servidor mestre. Se houver um desastre de região, você pode parar a replicação para que a réplica e redirecionar a carga de trabalho para ele. Parar a replicação permite que a réplica começar a aceitar gravações, bem como lê. Saiba mais na [parar a replicação](#stop-replication) seção. 

## <a name="create-a-replica"></a>Criar uma réplica
O servidor mestre deve ter o parâmetro `azure.replication_support` definido como **REPLICA**. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. (O parâmetro `azure.replication_support` se aplica somente às camadas de Uso Geral e Otimizada para Memória.)

Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para PostgreSQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

Cada réplica está habilitada para armazenamento [aumentá-lo](concepts-pricing-tiers.md#storage-auto-grow). O recurso de auto-grow permite que a réplica para manter atualizado com os dados replicados a ele e evitar uma quebra na replicação causada por erros de armazenamento insuficiente.

O recurso de réplica de leitura usa a replicação física do PostgreSQL, e não a replicação lógica. A replicação de streaming usando slots de replicação é o modo de operação padrão. Quando necessário, o envio de logs é usado para recuperar o atraso.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica
Quando você cria uma réplica, ela não herda as regras de firewall nem o ponto de extremidade de serviço de rede virtual do servidor mestre. Essas regras precisam ser configuradas independentemente da réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para PostgreSQL. Para um servidor nomeado **minha réplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando o psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação
Banco de dados do Azure para PostgreSQL fornece duas métricas para monitorar a replicação. Duas métricas são **máximo de latência entre réplicas** e **latência de réplica**. Para saber como exibir essas métricas, consulte o **monitorar uma réplica** seção o [leia o artigo de instruções de réplica](howto-read-replicas-portal.md).

O **máximo de latência entre réplicas** métrica mostra o retardo em bytes entre o mestre e a réplica de retardo máximo. Essa métrica está disponível apenas no servidor mestre.

O **latência de réplica** métrica mostra o tempo desde a última transação de reproduzidos. Se não houver nenhuma transação ocorrendo no servidor mestre, a métrica refletirá esse retardo. Essa métrica está disponível para somente os servidores de réplica. Latência de réplica é calculada a partir de `pg_stat_wal_receiver` exibição:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

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

Quando você interrompe a replicação, a réplica perde todos os links para seu mestre anterior e outras réplicas. Não há nenhum failover automático entre um mestre e de réplica. 

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).


## <a name="considerations"></a>Considerações

Esta seção resume as considerações sobre o recurso de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma réplica de leitura, o parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. O parâmetro `azure.replication_support` se aplica somente às camadas de Uso Geral e Otimizada para Memória.

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para PostgreSQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica
Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

PostgreSQL requer que o valor do parâmetro `max_connections` na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. No Banco de Dados do Azure para PostgreSQL, o valor do parâmetro `max_connections` se baseia na SKU. Para obter mais informações, confira [Limites no Banco de Dados do Azure para PostgreSQL](concepts-limits.md). 

Se você tentar atualizar os valores do servidor, mas não cumprir os limites, receberá um erro.

### <a name="maxpreparedtransactions"></a>max_prepared_transactions
[PostgreSQL requer](https://www.postgresql.org/docs/10/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) o valor da `max_prepared_transactions` parâmetro na réplica de leitura para ser maior que ou igual ao valor mestre; caso contrário, a réplica não será iniciado. Se você quiser alterar `max_prepared_transactions` no mestre, primeiro alterá-la nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se você interromper a replicação entre um servidor mestre e uma réplica de leitura, a réplica será reiniciada para aplicar a alteração. A réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos
Quando um servidor mestre é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas são reiniciadas para refletir essa alteração.

## <a name="next-steps"></a>Próximas etapas
Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
