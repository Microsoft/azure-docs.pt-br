---
title: Continuidade de negócios na nuvem-recuperação de banco de dados
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como o banco de dados SQL do Azure e o SQL Instância Gerenciada dão suporte à continuidade de negócios de nuvem e recuperação de banco de dados e ajudar a manter aplicativos de nuvem críticos em
keywords: continuidade dos negócios, continuidade dos negócios em nuvem, recuperação de desastre do banco de dados, recuperação de banco de dados
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 7bd991bd709bb4be69325afe967d7e5600a9e1a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222557"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A **continuidade dos negócios** no banco de dados SQL do Azure e no SQL instância gerenciada refere-se aos mecanismos, políticas e procedimentos que permitem que sua empresa continue operando diante de interrupções, especialmente em sua infraestrutura computacional. Na maioria dos casos, o banco de dados SQL e o SQL Instância Gerenciada lidar com os eventos de interrupção que podem ocorrer no ambiente de nuvem e manter seus aplicativos e processos de negócios em execução. No entanto, há alguns eventos de interrupção que não podem ser manipulados pelo banco de dados SQL automaticamente, como:

- O usuário excluiu ou atualizou acidentalmente uma linha de uma tabela.
- Um invasor mal-intencionado conseguiu excluir dados ou remover um banco de dados.
- O terremoto causou uma interrupção de energia e um datacenter desabilitado temporário.

Esta visão geral descreve os recursos que o banco de dados SQL e o SQL Instância Gerenciada fornecem para a continuidade dos negócios e a recuperação de desastres. Saiba mais sobre as opções, recomendações e tutoriais para recuperação de eventos com interrupção que poderiam causar a perda dos dados ou fazer com que o banco de dados e o aplicativo se tornassem indisponíveis. Aprenda o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Recursos do Banco de Dados SQL que você pode usar para proporcionar a continuidade dos negócios

De uma perspectiva de banco de dados, há quatro cenários principais de interrupção em potencial:

- Falhas locais de hardware ou software que afetam o nó do banco de dados, como uma falha na unidade de disco.
- Corrupção ou exclusão de dados geralmente causada por um erro de aplicativo ou erro humano. Essas falhas são específicas do aplicativo e normalmente não podem ser detectadas pelo serviço de banco de dados.
- Interrupção do datacenter, possivelmente causada por um desastre natural. Esse cenário requer algum nível de redundância geográfica com failover de aplicativo para um datacenter alternativo.
- Erros de atualização ou manutenção, problemas inesperados que ocorrem durante a manutenção de infraestrutura planejada ou atualizações podem exigir uma reversão rápida para um estado de banco de dados anterior.

Para reduzir as falhas de hardware e software locais, o banco de dados SQL inclui uma [arquitetura de alta disponibilidade](high-availability-sla.md), que garante a recuperação automática dessas falhas com um SLA de disponibilidade de até 99,995%.  

Para proteger seu negócio contra perda de dados, o banco de dados SQL e o SQL Instância Gerenciada criam automaticamente backups completos semanalmente, backups de bancos de dados diferenciais a cada 12 horas e backups de log de transações a cada 5-10 minutos. Os backups são armazenados no armazenamento RA-GRS por pelo menos 7 dias para todas as camadas de serviço. Todas as camadas de serviço, exceto suporte Básico período de retenção de backup configurável para restauração pontual, até 35 dias.

O banco de dados SQL e o SQL Instância Gerenciada também fornecem vários recursos de continuidade de negócios que você pode usar para atenuar vários cenários não planejados.

- [Tabelas temporais](../temporal-tables.md) permitem que você restaure versões de linhas de qualquer ponto no tempo.
- [Os backups automatizados internos e a](automated-backups-overview.md) [restauração pontual](recovery-using-backups.md#point-in-time-restore) permitem que você restaure o banco de dados completo para algum momento dentro do período de retenção configurado de até 35 dias.
- Você pode [restaurar um banco de dados excluído](recovery-using-backups.md#deleted-database-restore) para o ponto em que ele foi excluído se o **servidor não tiver sido excluído**.
- [Retenção de backup de longo prazo](long-term-retention-overview.md) permite manter os backups em até 10 anos. Isso está em visualização pública limitada para o SQL Instância Gerenciada
- A [replicação geográfica ativa](active-geo-replication-overview.md) permite que você crie réplicas legíveis e faça failover manualmente para qualquer réplica no caso de uma interrupção do Datacenter ou da atualização do aplicativo.
- O [grupo de failover automático](auto-failover-group-overview.md#terminology-and-capabilities) permite que o aplicativo se recupere automaticamente no caso de uma interrupção do datacenter.

## <a name="recover-a-database-within-the-same-azure-region"></a>Recuperar um banco de dados na mesma região do Azure

Você pode usar backups de banco de dados automáticos para restaurar um banco de dados para um ponto no tempo no passado. Dessa forma, você pode se recuperar de corrompimentos de dados causados por erros humanos. A restauração pontual permite que você crie um novo banco de dados no mesmo servidor que representa o estado dos dados antes do evento corrompido. Para a maioria dos bancos de dados, as operações de restauração levam menos de 12 horas. Pode levar mais tempo para recuperar um banco de dados muito grande ou muito ativo. Para obter mais informações sobre tempo de recuperação, consulte [tempo de recuperação do banco de dados](recovery-using-backups.md#recovery-time).

Se o período máximo de retenção de backup com suporte para a restauração pontual (PITR) não for suficiente para seu aplicativo, você poderá estendê-lo Configurando uma política de retenção de longo prazo (EPD) para os bancos de dados. Para obter mais informações, confira [Retenção de backup de longo prazo](long-term-retention-overview.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Comparar a replicação geográfica com grupos de failover

Os [grupos de failover automático](auto-failover-group-overview.md#terminology-and-capabilities) simplificam a implantação e o uso da [replicação geográfica](active-geo-replication-overview.md) e adicionam os recursos adicionais, conforme descrito na tabela a seguir:

|                                              | Replicação geográfica | Grupos de failover  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatic failover**                          |     Não          |      Sim         |
| **Fazer failover de vários bancos de dados simultaneamente**  |     Não          |      Sim         |
| **O usuário precisa atualizar a cadeia de conexão após o failover**      |     Sim         |      Não          |
| **Suporte à Instância Gerenciada de SQL**                   |     Não          |      Sim         |
| **Pode estar na mesma região que o primário**             |     Sim         |      Não          |
| **Várias réplicas**                            |     Sim         |      Não          |
| **Dá suporte à escala de leitura**                          |     Sim         |      Sim         |


## <a name="recover-a-database-to-the-existing-server"></a>Recuperar um banco de dados para o servidor existente

Embora seja raro, um datacenter do Azure pode ter uma interrupção. Quando uma interrupção ocorre, ela causa uma parada nos negócios, que pode durar alguns minutos ou horas.

- Uma opção é aguardar o banco de dados ficar online novamente quando a interrupção do datacenter terminar. Isso funciona para aplicativos que podem manter o banco de dados offline. Por exemplo, um projeto de desenvolvimento ou uma avaliação gratuita não precisam funcionar constantemente. Quando um datacenter tem uma interrupção, você não sabe por quanto tempo a interrupção pode durar, portanto, essa opção só funcionará se você não precisar do seu banco de dados por algum tempo.
- Outra opção é restaurar um banco de dados em qualquer servidor em qualquer região do Azure usando [backups de banco de dados com redundância geográfica](recovery-using-backups.md#geo-restore) (restauração geográfica). A restauração geográfica usa um backup com redundância geográfica como sua fonte e pode ser usada para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter está inacessível devido a uma interrupção.
- Por fim, você pode se recuperar rapidamente de uma interrupção se tiver configurado um secundário geográfico usando [a replicação geográfica ativa](active-geo-replication-overview.md) ou um [grupo de failover automático](auto-failover-group-overview.md) para seu banco de dados ou bancos de dados. Dependendo de sua escolha dessas tecnologias, você pode usar o failover manual ou automático. Enquanto o próprio failover leva apenas alguns segundos, o serviço levará pelo menos 1 hora para ativá-lo. Isso é necessário para garantir que o failover seja justificado pela escala da interrupção. Além disso, o failover pode resultar em pequena perda de dados devido à natureza da replicação assíncrona.

Na medida em que você desenvolve o plano de continuidade dos negócios, será necessário compreender qual é o tempo máximo aceitável antes que o aplicativo recupere-se completamente após o evento de interrupção. O tempo necessário para a recuperação completa do aplicativo é conhecido como RTO (objetivo de tempo de recuperação). Você também precisa entender o período máximo de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar a perda ao recuperar-se de um evento de interrupção não planejado. A possível perda de dados é conhecida como RPO (objetivo de ponto de recuperação).

Métodos de recuperação diferentes oferecem níveis diferentes de RPO e RTO. Você pode escolher um método de recuperação específico ou usar uma combinação de métodos para obter a recuperação completa do aplicativo. A tabela a seguir compara o RPO e o RTO de cada opção de recuperação. Os grupos de failover automático simplificam a implantação e o uso da replicação geográfica e adicionam os recursos adicionais, conforme descrito na tabela a seguir.

| Método de recuperação | RTO | RPO |
| --- | --- | --- |
| Restauração geográfica de backups replicados geograficamente | 12 h | 1 h |
| Grupos de failover automático | 1 h | 5 s |
| Failover manual de banco de dados | 30 s | 5 s |

> [!NOTE]
> *Failover de banco de dados manual* refere-se ao failover de um banco de dados individual para seu secundário replicado geograficamente usando o [modo não planejado](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities).
Consulte a tabela no início deste artigo para obter detalhes sobre RTO e RPO de failover automático.

Use grupos de failover automático se o aplicativo atender a algum desses critérios:

- Seja crítico.
- Tenha um SLA (Contrato de Nível de Serviço) que não permita um tempo de inatividade de 12 horas ou superior.
- O tempo de inatividade pode resultar em responsabilidade financeira.
- Ter uma alta taxa de alteração de dados e 1 hora de perda de dados não é aceitável.
- Que o custo adicional da replicação geográfica ativa seja menor que a responsabilidade financeira potencial e das perdas associadas do negócio.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Você pode optar por usar uma combinação de backups de banco de dados e replicação geográfica ativa, dependendo dos requisitos do aplicativo. Para obter uma discussão sobre considerações de design para bancos de dados autônomos e pools elásticos usando esses recursos de continuidade de negócios, consulte [criar um aplicativo para recuperação de desastre na nuvem](designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastres do pool elástico](disaster-recovery-strategies-for-applications-with-elastic-pool.md).

As seções a seguir fornecem uma visão geral das etapas para recuperar usando os backups de banco de dados ou a replicação geográfica ativa. Para obter etapas detalhadas, incluindo requisitos de planejamento, etapas de pós-recuperação e informações sobre como simular uma interrupção para executar uma análise de recuperação de desastre, consulte [recuperar um banco de dados no banco de dados SQL de uma interrupção](disaster-recovery-guidance.md).

### <a name="prepare-for-an-outage"></a>Prepare-se para uma interrupção

Independentemente do recurso de continuidade de negócios usados, você deve:

- Identificar e preparar o servidor de destino, incluindo as regras de firewall de IP no nível do servidor, logons e permissões de nível de banco de dados mestre.
- Determinar como redirecionar os clientes e aplicativos de cliente para o novo servidor
- Documentar outras dependências, como as configurações e alertas de auditoria

Se você não se preparar corretamente, colocar seus aplicativos online após um failover ou uma recuperação de banco de dados exigirá um tempo adicional e, provavelmente, a solução de problemas também será exigida em um momento de estresse, ou seja, uma combinação ruim.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Fazer failover em um banco de dados secundário com replicação geográfica

Se você estiver usando a replicação geográfica ativa ou grupos de failover automático como seu mecanismo de recuperação, você poderá configurar uma política de failover automático ou usar o [failover manual não planejado](active-geo-replication-configure-portal.md#initiate-a-failover). Depois de iniciado, o failover faz com que o secundário se torne o novo primário e fique pronto para registrar novas transações e responder à consultas, com perda mínima de dados, para os dados que ainda não foram replicados. Para obter informações sobre como criar o processo de failover, confira [Criar um aplicativo para recuperação de desastre na nuvem](designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando o datacenter volta a ficar online, os primários antigos se reconectam automaticamente à nova primária e se tornam bancos de dados secundários. Se você precisar realocar o primário de volta para a região original, poderá iniciar um failover planejado manualmente (failback).

### <a name="perform-a-geo-restore"></a>Executar uma restauração geográfica

Se estiver usando backups automatizados com o armazenamento com redundância geográfica (habilitado por padrão), você poderá recuperar o banco de dados usando a [restauração geográfica](disaster-recovery-guidance.md#recover-using-geo-restore). A recuperação normalmente ocorre após 12 horas, com perda de dados de até uma hora determinada pelo momento em que o último backup de log ocorreu e foi replicado. Até que a recuperação seja concluída, o banco de dados não poderá registrar nenhuma transação ou responder a qualquer consulta. Observe que a restauração geográfica só restaura o banco de dados para o último momento disponível.

> [!NOTE]
> Se o datacenter voltar a ficar online antes de você alternar seu aplicativo para o banco de dados recuperado, você poderá cancelar a recuperação.

### <a name="perform-post-failover--recovery-tasks"></a>Executar pós-failover / tarefas de recuperação

Após recuperar de um dos mecanismos de recuperação, você deverá executar as seguintes tarefas adicionais antes que os usuários e aplicativos entrem em funcionamento novamente:

- Redirecione clientes e aplicativos cliente para o novo servidor e o banco de dados restaurado.
- Verificar se as regras de firewall de IP do nível de servidor apropriadas estão em vigor para que os usuários se conectem ou use os [firewalls de nível de banco de dados](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) para habilitar as regras apropriadas.
- Verifique se os logons apropriados e as permissões no nível do banco de dados mestre estão em vigor (ou use [usuários independentes](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).
- Configure a auditoria, conforme apropriado.
- Configure alertas, conforme apropriado.

> [!NOTE]
> Se você estiver usando um grupo de failover e se conectar aos bancos de dados usando o ouvinte de leitura/gravação, o redirecionamento após o failover ocorrerá de forma automática e transparente para o aplicativo.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualize um aplicativo com tempo de inatividade mínimo

Às vezes, um aplicativo deve ser colocado offline devido à manutenção planejada, como uma atualização do aplicativo. [Gerenciar atualizações de aplicativos](manage-application-rolling-upgrade.md) descreve como usar a replicação geográfica ativa para habilitar as atualizações sem interrupção do seu aplicativo em nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação caso algo saia errado.

## <a name="next-steps"></a>Próximas etapas

Para obter uma discussão sobre as considerações de design de aplicativos para bancos de dados individuais e pools elásticos, consulte [criar um aplicativo para recuperação de desastre na nuvem](designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastres do pool elástico](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
