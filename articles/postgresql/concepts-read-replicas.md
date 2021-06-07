---
title: Ler réplicas-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso de réplica de leitura no banco de dados do Azure para PostgreSQL-servidor único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 62ef47e7d8f98241009c1c1f3d8c111113be432c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99220761"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Ler réplicas no banco de dados do Azure para PostgreSQL-servidor único

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para PostgreSQL para um servidor somente leitura. As réplicas são atualizadas de **forma assíncrona** com a tecnologia de replicação física nativa do mecanismo PostgreSQL. Você pode replicar do servidor primário para até cinco réplicas.

As réplicas são novos servidores que você gerencia de modo similar ao Banco de Dados do Azure para PostgreSQL normal. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Saiba como [criar e gerenciar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o primário. As réplicas de leitura também podem ser implantadas em uma região diferente e podem ser promovidas para serem um servidor de leitura/gravação no caso de uma recuperação de desastre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como as réplicas são somente leitura, elas não reduzem diretamente as sobrecargas de capacidade de gravação no primário.

### <a name="considerations"></a>Considerações
O recurso destina-se a cenários em que o retardo é aceitável e destinado a descarregamento de consultas. Não é destinado a cenários de replicação síncrona em que os dados de réplica devem estar atualizados. Haverá um atraso mensurável entre o primário e a réplica. Isso pode ser em minutos ou até mesmo horas, dependendo da carga de trabalho e da latência entre o primário e a réplica. Os dados na réplica eventualmente se tornam consistentes com os dados no primário. Use este recurso para cargas de trabalho que podem acomodar esse atraso. 

> [!NOTE]
> Para a maioria das cargas de trabalho de leitura, as réplicas oferecem atualizações quase em tempo real do primário. No entanto, com cargas de trabalho primárias intensivamente pesadas de gravação intensa, o retardo de replicação pode continuar crescendo e talvez nunca seja capaz de se deslocar com o primário. Isso também pode aumentar o uso de armazenamento no primário, pois os arquivos WAL não são excluídos até que sejam recebidos na réplica. Se essa situação persistir, excluir e recriar a réplica de leitura após a conclusão das cargas de trabalho com uso intensivo de gravação é a opção de retornar a réplica para um bom estado em relação ao retardo.
> Réplicas de leitura assíncronas não são adequadas para cargas de trabalho de gravação pesadas. Ao avaliar réplicas de leitura para seu aplicativo, monitore o retardo na réplica para um ciclo de carga de trabalho completo do aplicativo por meio de seu pico e horários de pico para acessar o possível atraso e o RTO/RPO esperado em vários pontos do ciclo de carga de trabalho.
> 
## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente do seu servidor primário. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou para trazer dados mais próximos dos seus usuários.

>[!NOTE]
> Os servidores de camada básica oferecem suporte apenas à replicação de mesma região.

Você pode ter um servidor primário em qualquer [região do banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Um servidor primário pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo da sua região primária.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Ler regiões de réplica":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Você sempre pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor primário está localizado. Estas são as regiões de réplica universal:

Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, oeste do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2, Oeste EUA Central.

### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure de seu servidor primário. Se não souber o par da sua região, você pode encontrar essa informação no [artigo Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para o planejamento de recuperação de desastres, recomendamos criar a réplica na região emparelhada, e não em uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam isolamento físico e residência de dados.  

Há limitações a serem consideradas: 
    
* Pares unidirecionais: Algumas regiões do Azure são emparelhadas em uma direção apenas. Essas regiões incluem Índia ocidental, sul do Brasil. 
   Isso significa que um servidor primário na Índia ocidental pode criar uma réplica na Índia Sul. No entanto, um servidor primário na Índia Sul não pode criar uma réplica na Índia ocidental. Isso ocorre porque a região secundária do Oeste da Índia é o Sul da Índia, mas a região secundária do Sul da Índia não é o Oeste da Índia.


## <a name="create-a-replica"></a>Criar uma réplica
Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para PostgreSQL é criado. O novo servidor é preenchido com os dados que estavam no servidor primário. O tempo de criação depende da quantidade de dados no primário e do tempo desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

Cada réplica é habilitada para armazenamento de [aumento automático](concepts-pricing-tiers.md#storage-auto-grow). O recurso de aumento automático permite que a réplica acompanhe os dados replicados para ele e evite uma interrupção na replicação causada por erros de armazenamento insuficiente.

O recurso de réplica de leitura usa a replicação física do PostgreSQL, e não a replicação lógica. A replicação de streaming usando slots de replicação é o modo de operação padrão. Quando necessário, o envio de logs é usado para recuperar o atraso.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

Se o servidor PostgreSQL de origem estiver criptografado com chaves gerenciadas pelo cliente, consulte a [documentação](concepts-data-encryption-postgresql.md) para obter considerações adicionais.

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica
Quando você cria uma réplica, ela não herda as regras de firewall ou o ponto de extremidade de serviço VNet do servidor primário. Essas regras precisam ser configuradas independentemente da réplica.

A réplica herda a conta de administrador do servidor primário. Todas as contas de usuário no servidor primário são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário que estão disponíveis no servidor primário.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para PostgreSQL. Para um servidor chamado **minha réplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando psql:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação
O banco de dados do Azure para PostgreSQL fornece duas métricas para monitorar a replicação. As duas métricas são o **retardo máximo entre réplicas** e **atraso de réplica**. Para saber como exibir essas métricas, consulte a seção **monitorar uma réplica** do artigo de [instruções de leitura de réplica](howto-read-replicas-portal.md).

A métrica de **atraso máximo entre réplicas** mostra o retardo em bytes entre a réplica primária e a mais alta. Essa métrica é aplicável e está disponível somente no servidor primário e estará disponível somente se pelo menos uma das réplicas de leitura estiver conectada ao primário e o primário estiver no modo de replicação de streaming. As informações de latência não mostram detalhes quando a réplica está no processo de se capturar com o primário usando os logs arquivados do primário em um modo de replicação de envio de arquivos.

A métrica de **atraso de réplica** mostra o tempo desde a última transação reproduzida. Se não houver nenhuma transação ocorrendo no servidor primário, a métrica refletirá esse intervalo de tempo. Essa métrica é aplicável e está disponível somente para servidores de réplica. A latência de réplica é calculada a partir da `pg_stat_wal_receiver` exibição:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Defina um alerta para informá-lo quando o retardo de réplica atinge um valor que não é aceitável para sua carga de trabalho. 

Para obter informações adicionais, consulte o servidor primário diretamente para obter o retardo de replicação em bytes em todas as réplicas.

> [!NOTE]
> Se um servidor primário ou uma réplica de leitura for reiniciado, o tempo necessário para reiniciar e se acumular será refletido na métrica de retardo de réplica.

## <a name="stop-replication--promote-replica"></a>Parar replicação/promover réplica
Você pode interromper a replicação entre um primário e uma réplica a qualquer momento. A ação de parada faz com que a réplica reinicie e promova a réplica para ser um servidor independente e gravável de leitura. Os dados no servidor autônomo são os dados que estavam disponíveis no servidor de réplica no momento em que a replicação é interrompida. Todas as atualizações subsequentes no primário não são propagadas para a réplica. No entanto, o servidor de réplica pode ter logs acumulados que ainda não foram aplicados. Como parte do processo de reinicialização, a réplica aplica todos os logs pendentes antes de aceitar conexões de cliente.  

### <a name="considerations"></a>Considerações
- Antes de interromper a replicação em uma réplica de leitura, verifique o atraso de replicação para garantir que a réplica tenha todos os dados necessários. 
- Como a réplica de leitura precisa aplicar todos os logs pendentes antes que possa se tornar um servidor autônomo, o RTO pode ser maior para cargas de trabalho pesadas de gravação quando a replicação de parada acontece, pois pode haver um atraso significativo na réplica. Preste atenção a isso ao planejar promover uma réplica.
- O servidor de réplica promovido não pode ser tornado novamente em uma réplica.
- Se você promover uma réplica para ser o servidor primário, não poderá estabelecer a replicação de volta para o servidor primário antigo. Se você quiser voltar para a região primária antiga, poderá estabelecer um novo servidor de réplica com um novo nome (ou) excluir o primário antigo e criar uma réplica usando o antigo nome primário.
- Se você tiver várias réplicas de leitura e se promover uma delas para ser seu servidor primário, outros servidores de réplica ainda estarão conectados ao primário antigo. Talvez seja necessário recriar réplicas do servidor novo e promovido.

Quando você interrompe a replicação, a réplica perde todos os links para suas réplicas primárias e outras anteriores.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Failover para réplica

No caso de uma falha do servidor primário, **não** há failover automático para a réplica de leitura. 

Como a replicação é assíncrona, pode haver um atraso considerável entre o primário e a réplica. A quantidade de latência é influenciada por vários fatores, como o tipo de carga de trabalho em execução no servidor primário e a latência entre o servidor primário e de réplica. Em casos típicos com carga de trabalho de gravação nominal, o retardo de réplica é esperado entre alguns segundos e alguns minutos. No entanto, nos casos em que o primário executa uma carga de trabalho muito pesada de gravação e a réplica não está se acumulando rápido o suficiente, a latência pode ser muito maior. Você pode acompanhar o atraso de replicação para cada réplica usando o *retardo da réplica* de métrica. Essa métrica mostra o tempo desde a última transação reproduzida na réplica. É recomendável que você identifique o atraso médio observando o atraso da réplica em um período de tempo. Você pode definir um alerta no atraso da réplica, de modo que, se ela ficar fora do intervalo esperado, você será notificado para executar uma ação.

> [!Tip]
> Se você realizar o failover para a réplica, o retardo no momento em que você desvincular a réplica da primária indicará a quantidade de dados perdida.

Depois que você decidir que deseja fazer failover para uma réplica, 

1. Parar a replicação na réplica<br/>
   Essa etapa é necessária para fazer com que o servidor de réplica se torne um servidor autônomo e seja capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será reiniciado e será desvinculado do primário. Depois que você inicia a interrupção da replicação, o processo de back-end normalmente leva alguns minutos para aplicar os logs residuais que ainda não foram aplicados e abrir o banco de dados como um servidor de leitura gravável. Consulte a seção [parar replicação](#stop-replication--promote-replica) deste artigo para entender as implicações dessa ação.
    
2. Aponte seu aplicativo para a réplica (antiga)<br/>
   Cada servidor tem uma cadeia de conexão exclusiva. Atualize a cadeia de conexão do aplicativo para apontar para a réplica (antiga) em vez do primário.
    
Depois que o aplicativo processar leituras e gravações com êxito, você terá concluído o failover. A quantidade de tempo de inatividade com a qual suas experiências de aplicativo dependerão quando você detectar um problema e concluir as etapas 1 e 2 acima.

### <a name="disaster-recovery"></a>Recuperação de desastre

Quando há um grande evento de desastre, como falhas regionais ou no nível de zona de disponibilidade, você pode executar a operação de recuperação de desastres promovendo a réplica de leitura. No portal de interface do usuário, você pode navegar até o servidor de réplica de leitura. Em seguida, clique na guia replicação e você pode parar a réplica para promovê-la para ser um servidor independente. Como alternativa, você pode usar o [CLI do Azure](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) para parar e promover o servidor de réplica.

## <a name="considerations"></a>Considerações

Esta seção resume as considerações sobre o recurso de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
As réplicas de leitura e a [decodificação lógica](concepts-logical.md) dependem do postgres write ahead log (WAL) para obter informações. Esses dois recursos precisam de diferentes níveis de registro em log do Postgres. A decodificação lógica precisa de um nível mais alto de log do que as réplicas de leitura.

Para configurar o nível certo de registro em log, use o parâmetro de suporte de replicação do Azure. O suporte à replicação do Azure tem três opções de configuração:

* **Off** -coloca as informações mínimas no Wal. Essa configuração não está disponível na maioria dos servidores do banco de dados do Azure para PostgreSQL.  
* **Réplica** -mais detalhada do que **desativado**. Esse é o nível mínimo de log necessário para que as [réplicas de leitura](concepts-read-replicas.md) funcionem. Essa configuração é o padrão na maioria dos servidores.
* **Logical** -mais detalhado que a **réplica**. Este é o nível mínimo de registro em log para que a decodificação lógica funcione. As réplicas de leitura também funcionam nessa configuração.


### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para PostgreSQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica
Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o primário. Depois que uma réplica é criada, várias configurações podem ser alteradas, incluindo o período de retenção do armazenamento e do backup.

As regras de firewall, as regras de rede virtual e as configurações de parâmetros não são herdadas do servidor primário para a réplica quando a réplica é criada ou posteriormente.

### <a name="scaling"></a>Dimensionamento
Dimensionamento de vCores ou entre Uso Geral e com otimização de memória:
* O PostgreSQL requer que a `max_connections` configuração em um servidor secundário seja [maior ou igual à configuração no primário](https://www.postgresql.org/docs/current/hot-standby.html), caso contrário, o secundário não será iniciado.
* No banco de dados do Azure para PostgreSQL, o máximo permitido de conexões para cada servidor é corrigido para o SKU de computação, já que as conexões ocupam memória. Você pode saber mais sobre o [mapeamento entre max_connections e SKUs de computação](concepts-limits.md).
* **Escalar verticalmente**: primeiro escalar verticalmente a computação de uma réplica e, em seguida, escalar verticalmente o primário. Essa ordem impedirá que erros violem o `max_connections` requisito.
* **Reduzir verticalmente**: primeiro reduza horizontalmente a computação do primário e reduza a réplica. Se você tentar dimensionar a réplica abaixo do primário, haverá um erro, pois isso viola o `max_connections` requisito.

Armazenamento em escala:
* Todas as réplicas têm o aumento automático de armazenamento habilitado para evitar problemas de replicação de uma réplica de armazenamento completo. Essa configuração não pode ser desabilitada.
* Você também pode escalar verticalmente o armazenamento manualmente, como faria em qualquer outro servidor


### <a name="basic-tier"></a>Camada básica
Os servidores de camada básica oferecem suporte apenas à replicação de mesma região.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL requer](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) que o valor do `max_prepared_transactions` parâmetro na réplica de leitura seja maior ou igual ao valor principal; caso contrário, a réplica não será iniciada. Se você quiser alterar `max_prepared_transactions` o primário, primeiro altere-o nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se você parar a replicação entre um servidor primário e uma réplica de leitura, a réplica será reiniciada para aplicar a alteração. A réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-primary-and-standalone-servers"></a>Servidores primários e autônomos excluídos
Quando um servidor primário é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas são reiniciadas para refletir essa alteração.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
* Saiba como [criar e gerenciar réplicas de leitura no CLI do Azure e na API REST](howto-read-replicas-cli.md).