---
title: Ler réplicas-banco de dados do Azure para MySQL-servidor flexível
description: 'Saiba mais sobre réplicas de leitura no banco de dados do Azure para MySQL servidor flexível: criando réplicas, conectando-se a réplicas, monitorando a replicação e parando a replicação.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: e51b9667e3bb20a6bd463d3286888085a927f2c0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109651"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Ler réplicas no banco de dados do Azure para MySQL-servidor flexível

> [!IMPORTANT]
> Ler réplicas no banco de dados do Azure para MySQL-o servidor flexível está em versão prévia.

O MySQL é um dos mecanismos de banco de dados populares para a execução de aplicativos Web e móveis de escala da Internet. Muitos de nossos clientes o utilizam para os serviços de treinamento online, serviços de streaming de vídeo, soluções de pagamento digital, plataformas de comércio eletrônico, serviços de jogos, portais de notícias, governo e sites de saúde. Esses serviços são necessários para serem fornecidos e dimensionados conforme o tráfego no aplicativo Web ou móvel aumenta.

No lado dos aplicativos, o aplicativo é normalmente desenvolvido em Java ou php e migrado para ser executado em conjuntos de dimensionamento de máquinas virtuais do Azure ou serviços de Azure App ou são incluídos em contêineres para serem executados no AKS (serviço de kubernetes do Azure). Com o conjunto de dimensionamento de máquinas virtuais, o Serviço de Aplicativo ou o AKS como infraestrutura subjacente, a escala do aplicativo é simplificada provisionando instantaneamente novas VMs e replicando os componentes de aplicativos sem estado para atender às solicitações, mas geralmente o banco de dados acaba sendo um gargalo como componente com estado centralizado.

O recurso de réplica de leitura permite replicar dados de um servidor flexível do Banco de Dados do Azure para MySQL para um servidor somente leitura. Você pode replicar do servidor de origem para até **10** réplicas. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição do arquivo binário nativo (log binário) do mecanismo MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Réplicas são novos servidores que você gerencia de forma semelhante ao banco de dados do Azure de origem para servidores flexíveis do MySQL. Você incorrerá em cobranças de cada réplica de leitura com base na computação provisionada no vCores e no armazenamento em GB/mês. Para obter mais informações, consulte [preços](./concepts-compute-storage.md#pricing).

Para saber mais sobre recursos e problemas de replicação do MySQL, consulte a [documentação de replicação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.
>

## <a name="common-use-cases-for-read-replica"></a>Casos de uso comuns para réplica de leitura

O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para a origem.

Os cenários comuns são:

* Dimensionamento de leitura-cargas de trabalho provenientes do aplicativo usando o proxy de conexão leve como [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) ou usando o padrão baseado em microserviços para escalar horizontalmente suas consultas de leitura provenientes do aplicativo para ler as réplicas
* As cargas de trabalho de BI ou de relatório analítico podem usar réplicas de leitura como fonte de dados para relatórios
* Para o cenário de produção ou IoT em que as informações de telemetria são ingeridas no mecanismo de banco de dados MySQL enquanto várias réplicas de leitura são usadas para relatórios de dados

Como as réplicas são somente leitura, elas não reduzem diretamente as sobrecargas de capacidade de gravação na origem. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do MySQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre a origem e a réplica. Os dados na réplica eventualmente se tornam consistentes com os dados na origem. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor de origem não tiver servidores de réplica existentes, a origem será reiniciada primeiro para se preparar para a replicação.

Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para MySQL é criado. O novo servidor é preenchido com os dados que estavam no servidor de origem. O tempo de criação depende da quantidade de dados na origem e da hora desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração de servidor que a origem. A configuração do servidor de réplica pode ser alterada depois de criada. O servidor de réplica é sempre criado no mesmo grupo de recursos, no mesmo local e na mesma assinatura que o servidor de origem. Se você quiser criar um servidor de réplica para um grupo de recursos diferente ou uma assinatura diferente, poderá [mover o servidor de réplica](../../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar a origem.

Saiba como [criar uma réplica de leitura no portal do Azure](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Na criação, uma réplica herda o método de conectividade do servidor de origem. Não é possível alterar o método de conectividade da réplica. Por exemplo, se o servidor de origem tiver **acesso privado (integração VNet)** , a réplica não poderá estar no **acesso público (endereços IP permitidos)**.

A réplica herda a conta do administrador do servidor de origem. Todas as contas de usuário no servidor de origem são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário que estão disponíveis no servidor de origem.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, como faria em um servidor flexível do banco de dados do Azure para MySQL. Para um servidor chamado **myreplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando a CLI do mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

O banco de dados do Azure para MySQL servidor flexível fornece a métrica **atraso de replicação em segundos** no Azure monitor. Essa métrica está disponível apenas para réplicas. Essa métrica é calculada com o uso da métrica `seconds_behind_master` disponível no comando `SHOW SLAVE STATUS` do MySQL. Defina um alerta para informá-lo quando o retardo de replicação atingir um valor que não é aceitável para sua carga de trabalho.

Se você vir maior atraso de replicação, consulte [Solucionando problemas de latência de replicação](./../howto-troubleshoot-replication-latency.md) para solucionar problemas e entender as possíveis causas.

## <a name="stop-replication"></a>Parar replicação

Você pode interromper a replicação entre uma origem e uma réplica. Depois que a replicação é interrompida entre um servidor de origem e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não é atualizado com o servidor de origem.

Quando você opta por interromper a replicação em uma réplica, ela perde todos os links para a origem anterior e outras réplicas. Não há nenhum failover automatizado entre uma origem e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](how-to-read-replicas-portal.md).

## <a name="failover"></a>Failover

Não há nenhum failover automatizado entre os servidores de origem e de réplica.

As réplicas de leitura destinam-se ao dimensionamento de cargas de trabalho de leitura intensiva e não são projetadas para atender às necessidades de alta disponibilidade de um servidor. Não há nenhum failover automatizado entre os servidores de origem e de réplica. Parar a replicação na réplica de leitura para colocá-la online no modo de leitura/gravação é o meio pelo qual esse failover manual é executado.

Como a replicação é assíncrona, há um atraso entre a origem e a réplica. A quantidade de latência pode ser influenciada por muitos fatores, como a intensidade da carga de trabalho em execução no servidor de origem e a latência entre os data centers. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos. Você pode acompanhar o retardo de replicação real usando o *retardo de réplica* de métrica, que está disponível para cada réplica. Essa métrica mostra o tempo desde a última transação reproduzida. É recomendável que você identifique o que é o retardo médio, observando o atraso da réplica em um período de tempo. Você pode definir um alerta na latência de réplica, de modo que, se ele ficar fora do intervalo esperado, você poderá executar uma ação.

> [!Tip]
> Se você realizar o failover para a réplica, o retardo no momento em que você desvincular a réplica da fonte indicará a quantidade de dados perdida.

Depois que você decidir que deseja fazer failover para uma réplica:

1. Parar a replicação na réplica<br/>
   Essa etapa é necessária para tornar o servidor de réplica capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será desvinculado da origem. Depois que você inicia a interrupção da replicação, o processo de back-end normalmente leva cerca de 2 minutos para ser concluído. Consulte a seção [parar replicação](#stop-replication) deste artigo para entender as implicações dessa ação.

2. Aponte seu aplicativo para a réplica (antiga)<br/>
   Cada servidor tem uma cadeia de conexão exclusiva. Atualize seu aplicativo para apontar para a réplica (antiga) em vez da origem.

Depois que o aplicativo processar leituras e gravações com êxito, você terá concluído o failover. A quantidade de tempo de inatividade com a qual suas experiências de aplicativo dependerão quando você detectar um problema e concluir as etapas 1 e 2 acima.

## <a name="considerations-and-limitations"></a>Considerações e limitações

| Cenário | Limitação/consideração |
|:-|:-|
| Réplica no servidor com HA com redundância de zona habilitada | Sem suporte |
| Replicação de leitura entre regiões | Sem suporte |
| Preços | O custo da execução do servidor de réplica é baseado na região em que o servidor de réplica está em execução |
| Reinicialização do servidor de origem | Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e execute essas operações durante um período de fora de pico |
| Novas réplicas | Uma réplica de leitura é criada como um novo servidor flexível do banco de dados do Azure para MySQL. Um servidor existente não pode se tornar uma réplica. Não é possível criar uma réplica de outra réplica de leitura |
| Configuração da réplica | Uma réplica é criada usando a mesma configuração de servidor que a origem. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor de origem: geração de computação, vCores, armazenamento e período de retenção de backup. A camada de computação também pode ser alterada de forma independente.<br> <br> **IMPORTANTE**  <br> -Antes que uma configuração de servidor de origem seja atualizada para novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica possa acompanhar as alterações realizadas na origem. <br/> As configurações de método e parâmetro de conectividade são herdadas do servidor de origem para a réplica quando a réplica é criada. As regras da réplica são independentes posteriormente. |
| Réplicas paradas | Se você parar a replicação entre um servidor de origem e uma réplica de leitura, a réplica parada se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente. |
| Servidores de origem e autônomos excluídos | Quando um servidor de origem é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas se tornam servidores autônomos automaticamente e podem aceitar leituras e gravações. O próprio servidor de origem é excluído. |
| Contas de usuário | Os usuários no servidor de origem são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor de origem. |
| Parâmetros do Servidor | Para impedir que os dados fiquem fora de sincronia e evitar possíveis perdas de dados ou danos, alguns parâmetros de servidor estão bloqueados para serem atualizados ao usar réplicas de leitura. <br> Os seguintes parâmetros de servidor estão bloqueados nos servidores de origem e de réplica:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> O parâmetro [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) está bloqueado nos servidores de réplica. <br> Para atualizar um dos parâmetros acima no servidor de origem, exclua os servidores de réplica, atualize o valor do parâmetro na origem e recrie as réplicas. |
| Outro | -Não há suporte para a criação de uma réplica de uma réplica. <br> -As tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Essa é uma limitação da tecnologia de replicação do MySQL. Leia mais na [documentação de referência do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para mais informações. <br>-Verifique se as tabelas do servidor de origem têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre a origem e as réplicas.<br>-Examinar a lista completa de limitações de replicação do MySQL na [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](how-to-read-replicas-portal.md)
* Saiba como [criar e gerenciar réplicas de leitura usando a CLI do Azure](how-to-read-replicas-cli.md)