---
title: Continuidade dos negócios em nuvem - recuperação de banco de dados - Banco de Dados SQL | Microsoft Docs
description: Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução.
keywords: continuidade dos negócios, continuidade dos negócios em nuvem, recuperação de desastre do banco de dados, recuperação de banco de dados
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: mathoma, carlrab
manager: digimobile
origin.date: 04/04/2019
ms.date: 04/15/2019
ms.openlocfilehash: dfa5d4cb2d782f1466329300157a64fd17765460
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412336"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure

**Continuidade dos negócios** no Banco de Dados SQL do Azure refere-se aos mecanismos, políticas e procedimentos que permitem que seu negócio continue operando em caso de interrupção, particularmente em sua infraestrutura de computação. Na maioria dos casos, o Banco de Dados SQL do Azure lidará com os eventos de interrupção que podem acontecer no ambiente de nuvem e manterá seus aplicativos e processos empresariais em execução. No entanto, há alguns eventos de interrupção que não podem ser manipulados pelo Banco de Dados SQL como:

- O usuário excluiu ou atualizou acidentalmente uma linha de uma tabela.
- Um invasor mal-intencionado conseguiu excluir dados ou remover um banco de dados.
- Terremoto causou uma interrupção de energia e datacenter temporário desabilitado.

Esses casos não podem ser controlados pelo Banco de Dados SQL do Azure, portanto, você precisaria usar os recursos de continuidade de negócios no Banco de Dados SQL que permite recuperar seus dados e manter seus aplicativos em execução.

Esta visão geral descreve os recursos que o Banco de Dados SQL do Azure fornece para a continuidade dos negócios e a recuperação de desastre. Saiba mais sobre as opções, recomendações e tutoriais para recuperação de eventos com interrupção que poderiam causar a perda dos dados ou fazer com que o banco de dados e o aplicativo se tornassem indisponíveis. Aprenda o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Recursos do Banco de Dados SQL que você pode usar para proporcionar a continuidade dos negócios

De uma perspectiva de banco de dados, há quatro cenários principais de interrupção em potencial:

- Falhas locais de hardware ou software que afetam o nó do banco de dados, como uma falha na unidade de disco.
- Corrupção ou exclusão de dados geralmente causada por um erro de aplicativo ou erro humano. Essas falhas são intrinsecamente específicas do aplicativo e, em geral, não podem ser detectadas ou atenuadas automaticamente pela infraestrutura.
- Interrupção do datacenter, possivelmente causada por um desastre natural. Esse cenário requer algum nível de redundância geográfica com failover de aplicativo para um datacenter alternativo.
- Erros de atualização ou manutenção, problemas imprevistos que ocorrem durante atualizações planejadas ou manutenção em um aplicativo ou banco de dados podem exigir a rápida reversão de um estado anterior do banco de dados.

O Banco de Dados SQL fornece vários recursos de continuidade de negócios, incluindo backups automatizados e replicação de banco de dados opcional que podem atenuar esses cenários. Primeiro, é necessário compreender como a [arquitetura de alta disponibilidade](sql-database-high-availability.md) do Banco de Dados SQL fornece 99,99% de disponibilidade e resiliência a alguns eventos de interrupção que podem afetar o processo empresarial.
Em seguida, você pode aprender sobre os mecanismos adicionais que podem ser usados para recuperar-se dos eventos de interrupção que não podem ser manipulados pela arquitetura de alta disponibilidade do Banco de Dados SQL, como:

- [Tabelas temporais](sql-database-temporal-tables.md) permitem que você restaure versões de linhas de qualquer ponto no tempo.
- [Backups automatizados internos](sql-database-automated-backups.md) e [Recuperação Pontual](sql-database-recovery-using-backups.md#point-in-time-restore) permitem que você restaure o banco de dados completo em algum ponto no tempo nos últimos 35 dias.
- Você poderá [restaurar um banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore) para o ponto em que ele foi excluído se o **servidor do Banco de Dados SQL não tiver sido excluído**.
- [Retenção de backup de longo prazo](sql-database-long-term-retention.md) permite manter os backups em até 10 anos.
- [Replicação geográfica ativa](sql-database-active-geo-replication.md) permite que você crie réplicas legíveis e faça o failover manualmente para qualquer réplica no caso de uma atualização de aplicativo ou interrupção do data center.
- [Grupo de failover automático](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permite que o aplicativo seja automaticamente recuperado em caso de interrupção do data center.

Cada um deles tem características diferentes para o ERT (tempo de recuperação estimado) e para a perda potencial de dados em transações recentes. Depois de compreender essas opções, você poderá escolher entre elas e, na maioria dos cenários, usá-las simultaneamente em cenários diferentes. Na medida em que você desenvolve o plano de continuidade dos negócios, será necessário compreender qual é o tempo máximo aceitável antes que o aplicativo recupere-se completamente após o evento de interrupção. O tempo necessário para o aplicativo recuperar totalmente é conhecido como RTO (objetivo de tempo de recuperação). Também é necessário reconhecer o período máximo de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar perder durante a recuperação após o evento de interrupção. O período de tempo de atualizações que você pode perder é conhecido como RPO (objetivo de ponto de recuperação).

A tabela a seguir compara o ERT e RPO para cada camada de serviço para os cenários mais comuns.

| Recurso | Basic | Standard | Premium | Uso geral | Comercialmente Crítico
| --- | --- | --- | --- |--- |--- |
| Recuperação Pontual do backup |Qualquer ponto de restauração no prazo de sete dias |Qualquer ponto de restauração dentro de 35 dias |Qualquer ponto de restauração dentro de 35 dias |Qualquer ponto de restauração dentro do período configurado (até 35 dias)|Qualquer ponto de restauração dentro do período configurado (até 35 dias)|
| Restauração geográfica de backups replicados geograficamente |ERT < 12 h<br> RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h|ERT < 12 h<br>RPO < 1 h|
| Grupos de failover automático |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s|RTO = 1 h<br>RPO < 5 s|
| Failover manual do banco de dados |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s|ERT = 30 s<br>RPO < 5 s|

> [!NOTE]
> *Failover manual do banco de dados* refere-se ao failover de um banco de dados para seu com replicação geográfica secundário usando o [modo planejado](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).

## <a name="recover-a-database-to-the-existing-server"></a>Recuperar um banco de dados para o servidor existente

O Banco de Dados SQL executa automaticamente uma combinação de backups de banco de dados completos semanais, backups de bancos de dados diferenciais geralmente a cada 12 horas e backups de logs de transações a cada 5 a 10 minutos para proteger sua empresa contra perda de dados. Os backups são armazenados no armazenamento RA-GRS por 35 dias para todas as camadas de serviço, exceto camadas de serviço de DTU Básicas, nas quais os backups são armazenados por 7 dias. Para saber mais, consulte [backups de banco de dados automáticos](sql-database-automated-backups.md). É possível restaurar um formulário do banco de dados existente, backups automatizados para um ponto anterior no tempo, como um novo banco de dados no mesmo servidor do Banco de Dados SQL, usando o portal do Azure, o PowerShell ou a API REST. Para obter mais informações, consulte [Recuperação pontual](sql-database-recovery-using-backups.md#point-in-time-restore).

Se o período máximo de retenção de PITR com suporte não for suficiente para o aplicativo, será possível estendê-lo configurando uma política LTR (retenção de longo prazo) para o(s) banco(s) de dados. Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

Você pode usar esses backups automáticos do banco de dados para recuperar um banco de dados de diversos eventos de interrupção, tanto em seu data center quanto em outro. Normalmente, o tempo de recuperação é menor do que 12 horas. Pode levar mais tempo para recuperar um banco de dados muito grande ou ativo. Ao usar os backups automáticos de banco de dados, o tempo estimado de recuperação dependerá de vários fatores, incluindo o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo, o tamanho do banco de dados, o tamanho do log de transações e a largura de banda da rede. Para obter mais informações sobre tempo de recuperação, consulte [tempo de recuperação do banco de dados](sql-database-recovery-using-backups.md#recovery-time). Ao recuperar em outra região de dados, a possível perda de dados é limitada a uma hora com o uso dos backups com redundância geográfica.

Utilize backups automatizados e [recuperação pontual](sql-database-recovery-using-backups.md#point-in-time-restore) como mecanismo de recuperação e continuidade dos negócios, se o aplicativo:

- Não for considerado crítico.
- Não tem um SLA associado - um tempo de inatividade de 24 horas ou mais não resulta em responsabilidade financeira.
- Tiver uma taxa baixa de alteração de dados (poucas transações por hora), uma vez que perder até uma hora de alterações é uma perda de dados aceitável.
- Seja suscetível aos custos.

Se você precisar de uma recuperação mais rápida, use [replicação geográfica ativa](sql-database-active-geo-replication.md) ou [grupos de failover automático](sql-database-auto-failover-group.md). Se for necessário recuperar dados de um período anterior a 35 dias, use [Retenção de longo prazo](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region"></a>Recuperar um banco de dados para outra região

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando uma interrupção ocorre, ela causa uma parada nos negócios, que pode durar alguns minutos ou horas.

- Uma opção é esperar que seu banco de dados volte a ficar online quando a interrupção do data center terminar. Isso funciona para aplicativos que podem manter o banco de dados offline. Por exemplo, um projeto de desenvolvimento ou uma avaliação gratuita não precisam funcionar constantemente. Quando um data center tiver uma interrupção, você não saberá quanto tempo ela durará. Portanto, essa opção só funcionará se o banco de dados não for necessário por um tempo.
- Outra opção é restaurar um banco de dados em qualquer servidor em qualquer região do Azure usando [backups de banco de dados com redundância geográfica](sql-database-recovery-using-backups.md#geo-restore) (restauração geográfica). A restauração geográfica usa um backup com redundância geográfica como sua fonte e pode ser usada para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter está inacessível devido a uma interrupção.
- Por fim, você pode se recuperar rapidamente de uma interrupção se você tiver configurado o secundário geográfico usando [replicação geográfica ativa](sql-database-active-geo-replication.md) ou um [grupo de failover automático](sql-database-auto-failover-group.md) para seu banco de dados ou bancos de dados. Dependendo de sua escolha dessas tecnologias, você pode usar o failover manual ou automático. Enquanto o próprio failover leva apenas alguns segundos, o serviço levará pelo menos 1 hora para ativá-lo. Isso é necessário para garantir que o failover seja justificado pela escala da interrupção. Além disso, o failover pode resultar em pequena perda de dados devido à natureza da replicação assíncrona. Consulte a tabela no início deste artigo para obter detalhes sobre RTO e RPO de failover automático.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> Para usar a replicação geográfica ativa e grupos de failover automático, você deverá ser o proprietário da assinatura ou ter permissões administrativas no SQL Server. Você pode configurar e fazer failover usando o Portal do Azure, o PowerShell ou a API REST utilizando permissões da assinatura do Azure, ou utilizando o Transact-SQL com permissões no SQL Server.

Use grupos de failover automático se o aplicativo atender a algum desses critérios:

- Seja crítico.
- Tenha um SLA (Contrato de Nível de Serviço) que não permita um tempo de inatividade de 12 horas ou superior.
- O tempo de inatividade pode resultar em responsabilidade financeira.
- Ter uma alta taxa de alteração de dados e 1 hora de perda de dados não é aceitável.
- Que o custo adicional da replicação geográfica ativa seja menor que a responsabilidade financeira potencial e das perdas associadas do negócio.

Ao executar uma ação, o tempo que levará para você recuperar e a quantidade de dados perdidos dependerá de como você decide usar os recursos de continuidade dos negócios em seu aplicativo. Na verdade, você poderá escolher por usar uma combinação de backups de banco de dados e a replicação geográfica ativa dependendo dos requisitos do seus aplicativo. Para obter uma discussão sobre as considerações de design do aplicativo para bancos de dados independentes e pools elásticos que usam esses recursos de continuidade de negócios, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação de desastre para pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).


As seções a seguir fornecem uma visão geral das etapas para recuperar usando os backups de banco de dados ou a replicação geográfica ativa. Para obter as etapas detalhadas, incluindo os requisitos de planejamento, as etapas pós-recuperação e as informações sobre como simular uma interrupção para executar uma análise de recuperação de desastre, confira [Recover a SQL Database from an outage (Recuperar um Banco de Dados SQL de uma interrupção)](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepare-se para uma interrupção

Independentemente do recurso de continuidade de negócios usados, você deve:

- Identificar e preparar o servidor de destino, incluindo as regras de firewall de IP no nível do servidor, logons e permissões de nível de banco de dados mestre.
- Determinar como redirecionar os clientes e aplicativos de cliente para o novo servidor
- Documentar outras dependências, como as configurações e alertas de auditoria

Se você não se preparar corretamente, colocar seus aplicativos online após um failover ou uma recuperação de banco de dados exigirá um tempo adicional e, provavelmente, a solução de problemas também será exigida em um momento de estresse, ou seja, uma combinação ruim.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Fazer failover em um banco de dados secundário com replicação geográfica

Se você estiver usando replicação geográfica ativa ou grupos de failover automático como mecanismos de recuperação, você pode configurar uma política de failover automático ou usar [manual failover não planejado](sql-database-active-geo-replication-portal.md#initiate-a-failover). Depois de iniciado, o failover faz com que o secundário se torne o novo primário e fique pronto para registrar novas transações e responder à consultas, com perda mínima de dados, para os dados que ainda não foram replicados. Para obter informações sobre como criar o processo de failover, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando o data center volta a ficar online, os primários antigos reconectam-se automaticamente ao novo primário e se tornam bancos de dados secundários. Se você precisar realocar o primário de volta para a região original, poderá iniciar um failover planejado manualmente (failback).

### <a name="perform-a-geo-restore"></a>Executar uma restauração geográfica

Se estiver usando backups automatizados com o armazenamento com redundância geográfica (habilitado por padrão), você poderá recuperar o banco de dados usando a [restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). A recuperação normalmente ocorre após 12 horas, com perda de dados de até uma hora determinada pelo momento em que o último backup de log ocorreu e foi replicado. Até que a recuperação seja concluída, o banco de dados não poderá registrar nenhuma transação ou responder a qualquer consulta. Observe que a restauração geográfica só restaura o banco de dados para o último momento disponível.

> [!NOTE]
> Se o data center voltar a ficar online antes de você transferir seu aplicativo para o banco de dados recuperado, você poderá cancelar a recuperação.

### <a name="perform-post-failover--recovery-tasks"></a>Executar pós-failover / tarefas de recuperação

Após recuperar de um dos mecanismos de recuperação, você deverá executar as seguintes tarefas adicionais antes que os usuários e aplicativos entrem em funcionamento novamente:

- Redirecionar clientes e aplicativos de cliente para o novo servidor e banco de dados restaurado
- Verificar se as regras de firewall de IP do nível de servidor apropriadas estão em vigor para que os usuários se conectem ou use os [firewalls de nível de banco de dados](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) para habilitar as regras apropriadas.
- Verificar se os logons apropriados e as permissões nível de banco de dados mestre estão em vigor (ou usar os [usuários independentes](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Configurar a auditoria, conforme apropriado
- Configurar os alertas, conforme apropriado

> [!NOTE]
> Se você estiver usando um grupo de failover e se conectar aos bancos de dados usando o lstener de leitura / gravação, o redirecionamento após o failover ocorrerá de maneira automática e transparente no aplicativo.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualize um aplicativo com tempo de inatividade mínimo

Às vezes, um aplicativo deve ser colocado offline devido à manutenção planejada, como uma atualização do aplicativo. [Gerenciar atualizações de aplicativos](sql-database-manage-application-rolling-upgrade.md) descreve como usar a replicação geográfica ativa para habilitar as atualizações sem interrupção do seu aplicativo em nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação caso algo saia errado.

## <a name="next-steps"></a>Próximos passos

Para obter uma discussão sobre as considerações de design de aplicativo para bancos de dados independentes e para pools elásticos, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação de desastre para pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
