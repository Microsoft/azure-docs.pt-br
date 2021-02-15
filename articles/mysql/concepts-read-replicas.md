---
title: Ler réplicas-banco de dados do Azure para MySQL
description: 'Saiba mais sobre réplicas de leitura no Banco de Dados do Azure para MySQL: como escolher regiões, criar réplicas, conectar réplicas, monitorar replicação e parar replicação.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2021
ms.custom: references_regions
ms.openlocfilehash: c380a3edb556adb72d067cb2910c8afbf66b99a0
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250257"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Leia réplicas no Banco de Dados do Azure para MySQL

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para MySQL para um servidor somente leitura. Você pode replicar do servidor de origem para até cinco réplicas. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição do arquivo binário nativo (log binário) do mecanismo MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas são novos servidores que você gerencia de modo semelhante aos servidores normais do Banco de Dados do Azure para PostgreSQL. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Para saber mais sobre recursos e problemas de replicação do MySQL, consulte a [documentação de replicação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.
>

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para a origem.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como as réplicas são somente leitura, elas não reduzem diretamente as sobrecargas de capacidade de gravação na origem. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do MySQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre a origem e a réplica. Os dados na réplica eventualmente se tornam consistentes com os dados na origem. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões

Você pode criar uma réplica de leitura em uma região diferente do seu servidor de origem. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou para trazer dados mais próximos dos seus usuários.

Você pode ter um servidor de origem em qualquer [região do banco de dados do Azure para MySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Um servidor de origem pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A imagem a seguir mostra quais regiões de réplica estão disponíveis dependendo da sua região de origem.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Ler regiões de réplica":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal

Você pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor de origem está localizado. As regiões de réplica universal compatíveis incluem:

Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, oeste do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2, Oeste EUA Central.

### <a name="paired-regions"></a>Regiões emparelhadas

Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor de origem. Se não souber o par da sua região, você pode encontrar essa informação no [artigo Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para planejamento de recuperação de desastres, recomendamos que você crie a réplica na região emparelhada em vez de uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam isolamento físico e residência de dados.  

No entanto, há certas limitações a serem consideradas: 

* Disponibilidade regional: o banco de dados do Azure para MySQL está disponível na França central, Norte dos EAU e na Alemanha central. No entanto, suas regiões emparelhadas não estão disponíveis.

* Pares unidirecionais: Algumas regiões do Azure são emparelhadas em uma direção apenas. Essas regiões incluem Oeste da Índia, Sul do Brasil e US Gov - Virgínia.
   Isso significa que um servidor de origem na Índia ocidental pode criar uma réplica na Índia Sul. No entanto, um servidor de origem na Índia Sul não pode criar uma réplica na Índia ocidental. Isso ocorre porque a região secundária da Índia ocidental é sul da Índia, mas a região secundária do Sul da Índia não é oeste da Índia.

## <a name="create-a-replica"></a>Criar uma réplica

> [!IMPORTANT]
> O recurso de réplica de leitura está disponível apenas para bancos de dados do Azure para servidores MySQL nas camadas de preços de uso geral ou de otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

Se um servidor de origem não tiver servidores de réplica existentes, a origem será reiniciada primeiro para se preparar para a replicação.

Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para MySQL é criado. O novo servidor é preenchido com os dados que estavam no servidor de origem. O tempo de criação depende da quantidade de dados na origem e da hora desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma assinatura que o servidor de origem. Se você quiser criar um servidor de réplica para um grupo de recursos diferente ou uma assinatura diferente, você pode [mover o servidor de réplica](../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação.

Cada réplica é habilitada para armazenamento de [aumento automático](concepts-pricing-tiers.md#storage-auto-grow). O recurso de aumento automático permite que a réplica acompanhe os dados replicados e evita uma interrupção na replicação causada por erros de falta de armazenamento.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Na criação, uma réplica herda as regras de firewall do servidor de origem. Posteriormente, essas regras são independentes do servidor de origem.

A réplica herda a conta do administrador do servidor de origem. Todas as contas de usuário no servidor de origem são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário que estão disponíveis no servidor de origem.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para MySQL. Para um servidor chamado **myreplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando a CLI do mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

O Banco de Dados do Azure para MySQL fornece a métrica **Atraso da replicação em segundos** no Azure Monitor. Essa métrica está disponível apenas para réplicas. Essa métrica é calculada com o uso da métrica `seconds_behind_master` disponível no comando `SHOW SLAVE STATUS` do MySQL. Defina um alerta para informá-lo quando o retardo de replicação atingir um valor que não é aceitável para sua carga de trabalho.

Se você vir maior atraso de replicação, consulte [Solucionando problemas de latência de replicação](howto-troubleshoot-replication-latency.md) para solucionar problemas e entender as possíveis causas.

## <a name="stop-replication"></a>Parar replicação

Você pode interromper a replicação entre uma origem e uma réplica. Depois que a replicação é interrompida entre um servidor de origem e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não é atualizado com o servidor de origem.

Quando você opta por interromper a replicação em uma réplica, ela perde todos os links para a origem anterior e outras réplicas. Não há nenhum failover automatizado entre uma origem e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Não há nenhum failover automatizado entre os servidores de origem e de réplica.

Como a replicação é assíncrona, há um atraso entre a origem e a réplica. A quantidade de latência pode ser influenciada por muitos fatores, como a intensidade da carga de trabalho em execução no servidor de origem e a latência entre os data centers. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos. Você pode acompanhar o retardo de replicação real usando o *retardo de réplica* de métrica, que está disponível para cada réplica. Essa métrica mostra o tempo desde a última transação reproduzida. É recomendável que você identifique o que é o retardo médio, observando o atraso da réplica em um período de tempo. Você pode definir um alerta na latência de réplica, de modo que, se ele ficar fora do intervalo esperado, você poderá executar uma ação.

> [!Tip]
> Se você realizar o failover para a réplica, o retardo no momento em que você desvincular a réplica da fonte indicará a quantidade de dados perdida.

Depois de decidir que deseja fazer failover para uma réplica:

1. Parar a replicação na réplica<br/>
   Essa etapa é necessária para tornar o servidor de réplica capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será desvinculado da origem. Depois que você inicia a interrupção da replicação, o processo de back-end normalmente leva cerca de 2 minutos para ser concluído. Consulte a seção [parar replicação](#stop-replication) deste artigo para entender as implicações dessa ação.

2. Aponte seu aplicativo para a réplica (antiga)<br/>
   Cada servidor tem uma cadeia de conexão exclusiva. Atualize seu aplicativo para apontar para a réplica (antiga) em vez da origem.

Depois que o aplicativo processar leituras e gravações com êxito, você concluiu o failover. A quantidade de tempo de inatividade com a qual suas experiências de aplicativo dependerão quando você detectar um problema e concluir as etapas 1 e 2 listadas anteriormente.

## <a name="global-transaction-identifier-gtid"></a>GTID (identificador de transação global)

O GTID (identificador de transação global) é um identificador exclusivo criado com cada transação confirmada em um servidor de origem e está desativado por padrão no banco de dados do Azure para MySQL. O GTID tem suporte nas versões 5,7 e 8,0 e somente em servidores que dão suporte a armazenamento de até 16 TB. Para saber mais sobre o GTID e como ele é usado na replicação, confira replicação do MySQL [com a](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) documentação do GTID.

O MySQL dá suporte a dois tipos de transações: transações GTID (identificadas com GTID) e transações anônimas (não têm um GTID alocado)

Os seguintes parâmetros de servidor estão disponíveis para configurar o GTID: 

|**Parâmetro de servidor**|**Descrição**|**Valor padrão**|**Valores**|
|--|--|--|--|
|`gtid_mode`|Indica se GTIDs são usadas para identificar transações. As alterações entre os modos só podem ser feitas uma etapa por vez em ordem crescente (por exemplo, `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: As transações de replicação e novas devem ser anônimas <br> `OFF_PERMISSIVE`: As novas transações são anônimas. As transações replicadas podem ser transações anônimas ou GTIDs. <br> `ON_PERMISSIVE`: As novas transações são transações GTID. As transações replicadas podem ser transações anônimas ou GTIDs. <br> `ON`: As transações novas e replicadas devem ser transações GTID.|
|`enforce_gtid_consistency`|Impõe a consistência do GTID permitindo a execução apenas das instruções que podem ser registradas de forma transacional segura. Esse valor deve ser definido como `ON` antes de habilitar a replicação GTID. |`OFF`|`OFF`: Todas as transações têm permissão para violar a consistência de GTID.  <br> `ON`: Nenhuma transação tem permissão para violar a consistência de GTID. <br> `WARN`: Todas as transações têm permissão para violar a consistência de GTID, mas um aviso é gerado. | 

> [!NOTE]
> Depois que o GTID estiver habilitado, você não poderá desativá-lo. Se você precisar desativar GTID, entre em contato com o suporte. 

Para habilitar o GTID e configurar o comportamento de consistência, atualize os `gtid_mode` parâmetros do e do `enforce_gtid_consistency` servidor usando o [portal do Azure](howto-server-parameters.md), o [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou o [PowerShell](howto-configure-server-parameters-using-powershell.md).

Se GTID estiver habilitado em um servidor de origem ( `gtid_mode` = on), as réplicas recém-criadas também terão GTID habilitado e usarão a replicação GTID. Para manter a replicação consistente, você não pode atualizar `gtid_mode` no (s) servidor (es) de origem ou de réplica.

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Tipos de preço

No momento, as réplicas de leitura estão disponíveis apenas nas camadas de preços de uso geral e de otimização de memória.

> [!NOTE]
> O custo da execução do servidor de réplica é baseado na região em que o servidor de réplica está sendo executado.

### <a name="source-server-restart"></a>Reinicialização do servidor de origem

Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para MySQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica

Uma réplica é criada usando a mesma configuração de servidor que a origem. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor de origem: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor de origem ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica possa acompanhar as alterações realizadas na origem.

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

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

O parâmetro [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) está bloqueado nos servidores de réplica.

Para atualizar um dos parâmetros acima no servidor de origem, exclua os servidores de réplica, atualize o valor do parâmetro na origem e recrie as réplicas.

### <a name="gtid"></a>GTID

GTID tem suporte em:

* MySQL versões 5,7 e 8,0.
* Servidores que dão suporte a armazenamento de até 16 TB. Consulte o artigo [tipo de preço](concepts-pricing-tiers.md#storage) para obter a lista completa de regiões que dão suporte ao armazenamento de 16 TB.

GTID está desativado por padrão. Depois que o GTID estiver habilitado, você não poderá desativá-lo. Se você precisar desativar GTID, entre em contato com o suporte.

Se o GTID estiver habilitado em um servidor de origem, as réplicas recém-criadas também terão GTID habilitado e usarão a replicação GTID. Para manter a replicação consistente, você não pode atualizar `gtid_mode` no (s) servidor (es) de origem ou de réplica.

### <a name="other"></a>Outro

* Não há suporte para a criação de uma réplica de uma réplica.
* Tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Esta é uma limitação da tecnologia de replicação do MySQL. Leia mais na [documentação de referência do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para mais informações.
* Verifique se as tabelas do servidor de origem têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre a origem e as réplicas.
* Analise a lista completa das limitações de replicação do MySQL no [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
* Saiba como [criar e gerenciar réplicas de leitura usando a CLI do Azure e o API REST](howto-read-replicas-cli.md)