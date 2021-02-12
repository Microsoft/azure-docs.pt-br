---
title: Ler réplicas-banco de dados do Azure para MariaDB
description: 'Saiba mais sobre réplicas de leitura no banco de dados do Azure para MariaDB: escolhendo regiões, criando réplicas, conectando a réplicas, monitorando a replicação e parando a replicação.'
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 375db7e5c113c2a48642365624207ce3acbde8a2
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664207"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Réplicas de leitura no Banco de Dados do Azure para MariaDB

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para MariaDB para um servidor somente leitura. Você pode replicar do servidor de origem para até cinco réplicas. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição de arquivo de log (binlog) do mecanismo MariaDB com a ID de transação global (GTID). Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

Réplicas são novos servidores que você gerencia de forma semelhante ao banco de dados do Azure regular para servidores MariaDB. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Para saber mais sobre a replicação do GTID, consulte a [documentação de replicação do MariaDB](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o primário.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como as réplicas são somente leitura, elas não reduzem diretamente as sobrecargas de capacidade de gravação no primário. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso ler réplica usa replicação assíncrona. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre a origem e a réplica. Os dados na réplica eventualmente se tornam consistentes com os dados no primário. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões

Você pode criar uma réplica de leitura em uma região diferente do seu servidor de origem. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou para trazer dados mais próximos dos seus usuários.

Você pode ter um servidor de origem em qualquer [banco de dados do Azure para a região MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Um servidor de origem pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo da sua região de origem.

[![Ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal

Você pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor de origem está localizado. As regiões de réplica universal compatíveis incluem:

Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, oeste do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2, Oeste EUA Central.

### <a name="paired-regions"></a>Regiões emparelhadas

Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor de origem. Se não souber o par da sua região, você pode encontrar essa informação no [artigo Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para o planejamento de recuperação de desastres, recomendamos criar a réplica na região emparelhada, e não em uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam isolamento físico e residência de dados.  

No entanto, há certas limitações a serem consideradas: 

* Disponibilidade regional: o banco de dados do Azure para MariaDB está disponível na França central, Norte dos EAU e na Alemanha central. Entretanto, suas regiões emparelhadas não estão disponíveis.

* Pares unidirecionais: Algumas regiões do Azure são emparelhadas em uma direção apenas. Essas regiões incluem Oeste da Índia, Sul do Brasil e US Gov - Virgínia. 
   Isso significa que um servidor de origem na Índia ocidental pode criar uma réplica na Índia Sul. No entanto, um servidor de origem na Índia Sul não pode criar uma réplica na Índia ocidental. Isso ocorre porque a região secundária do Oeste da Índia é o Sul da Índia, mas a região secundária do Sul da Índia não é o Oeste da Índia.

## <a name="create-a-replica"></a>Criar uma réplica

> [!IMPORTANT]
> O recurso ler réplica só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

Se um servidor de origem não tiver servidores de réplica existentes, a origem será reiniciada primeiro para se preparar para a replicação.

Quando você inicia o fluxo de trabalho criar réplica, é criado um banco de dados do Azure em branco para o servidor MariaDB. O novo servidor é preenchido com os dados que estavam no servidor de origem. O tempo de criação depende da quantidade de dados na origem e da hora desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

> [!NOTE]
> Se você não tem alerta de armazenamento configurado em seus servidores, recomendamos que você faça isso. O alerta informa quando um servidor está se aproximando de seu limite de armazenamento, o que afetará a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Na criação, uma réplica herda as regras de firewall do servidor de origem. Posteriormente, essas regras são independentes do servidor de origem.

A réplica herda a conta do administrador do servidor de origem. Todas as contas de usuário no servidor de origem são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário que estão disponíveis no servidor de origem.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, como faria em um servidor de banco de dados do Azure normal para MariaDB. Para um servidor chamado **myreplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando a CLI do mysql:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

O banco de dados do Azure para MariaDB fornece a métrica **atraso de replicação em segundos** no Azure monitor. Essa métrica está disponível apenas para réplicas.

Essa métrica é calculada usando a `seconds_behind_master` métrica disponível no comando do MariaDB `SHOW SLAVE STATUS` .

Defina um alerta para informá-lo quando o retardo de replicação atingir um valor que não é aceitável para sua carga de trabalho.

## <a name="stop-replication"></a>Parar replicação

Você pode interromper a replicação entre uma origem e uma réplica. Depois que a replicação é interrompida entre um servidor de origem e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não é atualizado com o servidor de origem.

Quando você opta por interromper a replicação em uma réplica, ela perde todos os links para a origem anterior e outras réplicas. Não há nenhum failover automatizado entre uma origem e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Não há nenhum failover automatizado entre os servidores de origem e de réplica.

Como a replicação é assíncrona, há um atraso entre a origem e a réplica. A quantidade de latência pode ser influenciada por vários fatores, como a intensidade da carga de trabalho em execução no servidor de origem e a latência entre os data centers. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos. Você pode acompanhar o retardo de replicação real usando o *retardo de réplica* de métrica, que está disponível para cada réplica. Essa métrica mostra o tempo desde a última transação reproduzida. É recomendável que você identifique o que é o retardo médio, observando o atraso da réplica em um período de tempo. Você pode definir um alerta na latência de réplica, de modo que, se ele ficar fora do intervalo esperado, você poderá executar uma ação.

> [!Tip]
> Se você realizar o failover para a réplica, o retardo no momento em que você desvincular a réplica da fonte indicará a quantidade de dados perdida.

Depois que você decidir que deseja fazer failover para uma réplica,

1. Pare a replicação na réplica.

   Essa etapa é necessária para tornar o servidor de réplica capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será desvinculado do primário. Depois que você inicia a interrupção da replicação, o processo de back-end normalmente leva cerca de 2 minutos para ser concluído. Consulte a seção [parar replicação](#stop-replication) deste artigo para entender as implicações dessa ação.

2. Aponte seu aplicativo para a réplica (antiga).

   Cada servidor tem uma cadeia de conexão exclusiva. Atualize seu aplicativo para apontar para a réplica (antiga) em vez do primário.

Depois que o aplicativo processar leituras e gravações com êxito, você terá concluído o failover. A quantidade de tempo de inatividade com a qual suas experiências de aplicativo dependerão quando você detectar um problema e concluir as etapas 1 e 2 acima.

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Tipos de preço

No momento, as réplicas de leitura estão disponíveis apenas nas camadas de preços de uso geral e de otimização de memória.

> [!NOTE]
> O custo da execução do servidor de réplica é baseado na região em que o servidor de réplica está sendo executado.

### <a name="source-server-restart"></a>Reinicialização do servidor de origem

Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo banco de dados do Azure para o servidor MariaDB. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica

Uma réplica é criada usando a mesma configuração de servidor que a primária. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor de origem: geração de computação, vCores, armazenamento, período de retenção de backup e versão do mecanismo MariaDB. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor de origem ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Essa ação garante que a réplica possa acompanhar as alterações feitas no primário.

As regras de firewall e as configurações de parâmetros são herdadas do servidor de origem para a réplica quando a réplica é criada. As regras da réplica são independentes posteriormente.

### <a name="stopped-replicas"></a>Réplicas paradas

Se você parar a replicação entre um servidor de origem e uma réplica de leitura, a réplica parada se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-source-and-standalone-servers"></a>Servidores de origem e autônomos excluídos

Quando um servidor de origem é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas se tornam servidores autônomos automaticamente e podem aceitar leituras e gravações. O próprio servidor de origem é excluído.

### <a name="user-accounts"></a>Contas de usuário

Os usuários no servidor de origem são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor de origem.

### <a name="server-parameters"></a>Parâmetros do Servidor

Para impedir que os dados fiquem fora de sincronia e evitar possíveis perdas de dados ou danos, alguns parâmetros de servidor estão bloqueados para serem atualizados ao usar réplicas de leitura.

Os seguintes parâmetros de servidor estão bloqueados nos servidores de origem e de réplica:

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

O parâmetro [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) está bloqueado nos servidores de réplica.

Para atualizar um dos parâmetros acima no servidor de origem, exclua os servidores de réplica, atualize o valor do parâmetro no primário e recrie as réplicas.

### <a name="other"></a>Outro

* A criação de uma réplica de uma réplica não é suportada.
* Tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Essa é uma limitação da tecnologia de replicação MariaDB.
* Verifique se as tabelas do servidor de origem têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre a origem e as réplicas.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
* Saiba como [criar e gerenciar réplicas de leitura usando a CLI do Azure e o API REST](howto-read-replicas-cli.md)
