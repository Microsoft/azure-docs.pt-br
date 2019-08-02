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
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/25/2019
ms.openlocfilehash: 361613c52c00b7a7e468eccbb52bf113b6adb434
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444503"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure

**Continuidade dos negócios** no Banco de Dados SQL do Azure refere-se aos mecanismos, políticas e procedimentos que permitem que seu negócio continue operando em caso de interrupção, particularmente em sua infraestrutura de computação. Na maioria dos casos, o Banco de Dados SQL do Azure lidará com os eventos de interrupção que podem acontecer no ambiente de nuvem e manterá seus aplicativos e processos empresariais em execução. No entanto, há alguns eventos de interrupção que não podem ser manipulados pelo banco de dados SQL automaticamente, como:

- O usuário excluiu ou atualizou acidentalmente uma linha de uma tabela.
- Um invasor mal-intencionado conseguiu excluir dados ou remover um banco de dados.
- Terremoto causou uma interrupção de energia e datacenter temporário desabilitado.

Esta visão geral descreve os recursos que o Banco de Dados SQL do Azure fornece para a continuidade dos negócios e a recuperação de desastre. Saiba mais sobre as opções, recomendações e tutoriais para recuperação de eventos com interrupção que poderiam causar a perda dos dados ou fazer com que o banco de dados e o aplicativo se tornassem indisponíveis. Aprenda o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Recursos do Banco de Dados SQL que você pode usar para proporcionar a continuidade dos negócios

De uma perspectiva de banco de dados, há quatro cenários principais de interrupção em potencial:

- Falhas locais de hardware ou software que afetam o nó do banco de dados, como uma falha na unidade de disco.
- Corrupção ou exclusão de dados geralmente causada por um erro de aplicativo ou erro humano. Essas falhas são específicas do aplicativo e normalmente não podem ser detectadas pelo serviço de banco de dados.
- Interrupção do datacenter, possivelmente causada por um desastre natural. Esse cenário requer algum nível de redundância geográfica com failover de aplicativo para um datacenter alternativo.
- Erros de atualização ou manutenção, problemas inesperados que ocorrem durante a manutenção de infraestrutura planejada ou atualizações podem exigir uma reversão rápida para um estado de banco de dados anterior.

Para reduzir as falhas de hardware e software locais, o banco de dados SQL inclui uma [arquitetura de alta disponibilidade](sql-database-high-availability.md), que garante a recuperação automática dessas falhas com um SLA de disponibilidade de até 99,995%.  

Para proteger seu negócio contra perda de dados, o banco de dados SQL cria automaticamente backups completos semanalmente, backups de banco de dado diferenciais a cada 12 horas e backups de log de transações a cada 5-10 minutos. Os backups são armazenados no armazenamento RA-GRS por pelo menos 7 dias para todas as camadas de serviço. Todas as camadas de serviço, exceto suporte Básico período de retenção de backup configurável para restauração pontual, até 35 dias. 

O banco de dados SQL também fornece vários recursos de continuidade de negócios, que você pode usar para atenuar vários cenários não planejados. 

- [Tabelas temporais](sql-database-temporal-tables.md) permitem que você restaure versões de linhas de qualquer ponto no tempo.
- [Os backups automatizados internos e a](sql-database-automated-backups.md) [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore) permitem que você restaure o banco de dados completo para algum momento dentro do período de retenção configurado de até 35 dias.
- Você poderá [restaurar um banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore) para o ponto em que ele foi excluído se o **servidor do Banco de Dados SQL não tiver sido excluído**.
- [Retenção de backup de longo prazo](sql-database-long-term-retention.md) permite manter os backups em até 10 anos.
- [Replicação geográfica ativa](sql-database-active-geo-replication.md) permite que você crie réplicas legíveis e faça o failover manualmente para qualquer réplica no caso de uma atualização de aplicativo ou interrupção do data center.
- [Grupo de failover automático](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permite que o aplicativo seja automaticamente recuperado em caso de interrupção do data center.

## <a name="recover-a-database-within-the-same-azure-region"></a>Recuperar um banco de dados na mesma região do Azure

Você pode usar backups de banco de dados automáticos para restaurar um banco de dados para um ponto no tempo no passado. Dessa forma, você pode se recuperar de corrompimentos de dados causados por erros humanos. A restauração ponto-in-time permite que você crie um novo banco de dados no mesmo servidor que represente o estado dos dados antes do evento corrompido. Para a maioria dos bancos de dados, as operações de restauração levam menos de 12 horas. Pode levar mais tempo para recuperar um banco de dados muito grande ou muito ativo. Para obter mais informações sobre tempo de recuperação, consulte [tempo de recuperação do banco de dados](sql-database-recovery-using-backups.md#recovery-time). 

Se o período máximo de retenção de backup com suporte para a restauração pontual (PITR) não for suficiente para seu aplicativo, você poderá estendê-lo Configurando uma política de retenção de longo prazo (EPD) para os bancos de dados. Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Comparar a replicação geográfica com grupos de failover

Os [grupos de failover automático](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) simplificam a implantação e o uso da [replicação geográfica](sql-database-active-geo-replication.md) e adicionam os recursos adicionais, conforme descrito na tabela a seguir:

|                                              | Replicação Geográfica | Grupos de failover  |
|:---------------------------------------------| :-------------- | :----------------|
| Failover automático                           |     Não          |      Sim         |
| Fazer failover de vários bancos de dados simultaneamente  |     Não          |      Sim         |
| Atualizar cadeia de conexão após o failover      |     Sim         |      Não          |
| Instância gerenciada com suporte                   |     Não          |      Sim         |
| Pode estar na mesma região que o primário             |     Sim         |      Não          |
| Várias réplicas                            |     Sim         |      Não          |
| Dá suporte à escala de leitura                          |     Sim         |      Sim         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Recuperar um banco de dados para o servidor existente

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando uma interrupção ocorre, ela causa uma parada nos negócios, que pode durar alguns minutos ou horas.

- Uma opção é esperar que seu banco de dados volte a ficar online quando a interrupção do data center terminar. Isso funciona para aplicativos que podem manter o banco de dados offline. Por exemplo, um projeto de desenvolvimento ou uma avaliação gratuita não precisam funcionar constantemente. Quando um data center tiver uma interrupção, você não saberá quanto tempo ela durará. Portanto, essa opção só funcionará se o banco de dados não for necessário por um tempo.
- Outra opção é restaurar um banco de dados em qualquer servidor em qualquer região do Azure usando [backups de banco de dados com redundância geográfica](sql-database-recovery-using-backups.md#geo-restore) (restauração geográfica). A restauração geográfica usa um backup com redundância geográfica como sua fonte e pode ser usada para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter está inacessível devido a uma interrupção.
- Por fim, você pode se recuperar rapidamente de uma interrupção se tiver configurado um secundário geográfico usando [a replicação geográfica ativa](sql-database-active-geo-replication.md) ou um [grupo de failover automático](sql-database-auto-failover-group.md) para seu banco de dados ou bancos de dados. Dependendo de sua escolha dessas tecnologias, você pode usar o failover manual ou automático. Enquanto o próprio failover leva apenas alguns segundos, o serviço levará pelo menos 1 hora para ativá-lo. Isso é necessário para garantir que o failover seja justificado pela escala da interrupção. Além disso, o failover pode resultar em pequena perda de dados devido à natureza da replicação assíncrona. 

Na medida em que você desenvolve o plano de continuidade dos negócios, será necessário compreender qual é o tempo máximo aceitável antes que o aplicativo recupere-se completamente após o evento de interrupção. O tempo necessário para a recuperação completa do aplicativo é conhecido como RTO (objetivo de tempo de recuperação). Você também precisa entender o período máximo de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar a perda ao recuperar-se de um evento de interrupção não planejado. A possível perda de dados é conhecida como RPO (objetivo de ponto de recuperação).

Métodos de recuperação diferentes oferecem níveis diferentes de RPO e RTO. Você pode escolher um método de recuperação específico ou usar uma combinação de métodos para obter a recuperação completa do aplicativo. A tabela a seguir compara o RPO e o RTO de cada opção de recuperação. Os grupos de failover automático simplificam a implantação e o uso da replicação geográfica e adicionam os recursos adicionais, conforme descrito na tabela a seguir.

| Método de recuperação | RTO | RPO |
| --- | --- | --- | 
| Restauração geográfica de backups replicados geograficamente | 12 h | 1 h |
| Grupos de failover automático | 1 h | 5 s |
| Failover manual de banco de dados | 30 s | 5 s |

> [!NOTE]
> *Failover de banco de dados manual* refere-se ao failover de um banco de dados individual para seu secundário replicado geograficamente usando o [modo não planejado](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Consulte a tabela no início deste artigo para obter detalhes sobre RTO e RPO de failover automático.


Use grupos de failover automático se o aplicativo atender a algum desses critérios:

- Seja crítico.
- Tenha um SLA (Contrato de Nível de Serviço) que não permita um tempo de inatividade de 12 horas ou superior.
- O tempo de inatividade pode resultar em responsabilidade financeira.
- Ter uma alta taxa de alteração de dados e 1 hora de perda de dados não é aceitável.
- Que o custo adicional da replicação geográfica ativa seja menor que a responsabilidade financeira potencial e das perdas associadas do negócio.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Você pode optar por usar uma combinação de backups de banco de dados e replicação geográfica ativa, dependendo dos requisitos do aplicativo. Para obter uma discussão sobre considerações de design para bancos de dados autônomos e pools elásticos usando esses recursos de continuidade de negócios, consulte [criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e estratégias de recuperação de desastres do [pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

As seções a seguir fornecem uma visão geral das etapas para recuperar usando os backups de banco de dados ou a replicação geográfica ativa. Para obter as etapas detalhadas, incluindo os requisitos de planejamento, as etapas pós-recuperação e as informações sobre como simular uma interrupção para executar uma análise de recuperação de desastre, confira [Recover a SQL Database from an outage (Recuperar um Banco de Dados SQL de uma interrupção)](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepare-se para uma interrupção

Independentemente do recurso de continuidade de negócios usados, você deve:

- Identificar e preparar o servidor de destino, incluindo as regras de firewall de IP no nível do servidor, logons e permissões de nível de banco de dados mestre.
- Determinar como redirecionar os clientes e aplicativos de cliente para o novo servidor
- Documentar outras dependências, como as configurações e alertas de auditoria

Se você não se preparar corretamente, colocar seus aplicativos online após um failover ou uma recuperação de banco de dados exigirá um tempo adicional e, provavelmente, a solução de problemas também será exigida em um momento de estresse, ou seja, uma combinação ruim.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Fazer failover em um banco de dados secundário com replicação geográfica

Se você estiver usando a replicação geográfica ativa ou grupos de failover automático como seu mecanismo de recuperação, você poderá configurar uma política de failover automático ou usar o [failover manual não planejado](sql-database-active-geo-replication-portal.md#initiate-a-failover). Depois de iniciado, o failover faz com que o secundário se torne o novo primário e fique pronto para registrar novas transações e responder à consultas, com perda mínima de dados, para os dados que ainda não foram replicados. Para obter informações sobre como criar o processo de failover, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

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

## <a name="next-steps"></a>Próximas etapas

Para obter uma discussão sobre as considerações de design de aplicativo para bancos de dados independentes e para pools elásticos, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação de desastre para pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
