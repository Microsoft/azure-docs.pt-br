---
title: Recuperação de desastre
description: Saiba como recuperar um banco de dados de uma interrupção regional data center ou uma falha com a replicação geográfica ativa do banco de dados SQL do Azure e recursos de restauração geográfica.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: 11c83a6ec364865eb3478112c9f33add22a5c09d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643263"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar o banco de dados SQL do Azure ou fazer failover para um secundário
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O Banco de Dados SQL do Azure oferece os seguintes recursos para a recuperação de uma paralisação:

- [Replicação geográfica ativa](active-geo-replication-overview.md)
- [Grupos de failover automático](auto-failover-group-overview.md)
- [Restauração geográfica](recovery-using-backups.md#point-in-time-restore)
- [Bancos de dados com redundância de zona](high-availability-sla.md)

Para saber mais sobre os cenários de continuidade dos negócios e os recursos com suporte para esses cenários, confira [Continuidade dos negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Se você estiver usando pools ou banco de dados Comercialmente Crítico ou Premium com redundância de zona, o processo de recuperação é automatizado e o restante deste material não se aplica.
>
> Os bancos de dados primário e secundário devem ter a mesma camada de serviço. Também é altamente recomendável que o banco de dados secundário seja criado com o mesmo tamanho de computação (DTUs ou vCores) que o primário. Para obter mais informações, consulte [atualizando ou fazendo downgrade como banco de dados primário](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database).
>
> Use um ou vários grupos de failover para gerenciar o failover de vários bancos de dados.
> Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, certifique-se de que o geograficamente secundário esteja configurado com o mesmo nível de serviço e tamanho da computação do primário. Para obter mais informações, consulte [usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Prepare-se para o caso de uma interrupção

Para ter êxito com a recuperação para outra região de dados usando a replicação os grupos de failover ou os backups com redundância geográfica, você precisará preparar um servidor em outra interrupção de data center para que ele se torne o novo servidor primário, caso seja necessário, bem como definir as etapas documentadas e testadas para garantir uma recuperação simples. Essas etapas de preparação incluem:

- Identifique o servidor em outra região para se tornar o novo servidor primário. Para a restauração geográfica, geralmente será um servidor na [região emparelhada](../../best-practices-availability-paired-regions.md) para a região na qual o banco de dados está localizado. Isso eliminará o custo de tráfego adicional durante as operações de restauração geográfica.
- Identificar e, como alternativa, definir as regras de firewall de IP no nível de servidor necessárias para que os usuários acessem o novo banco de dados primário.
- Determinar como você pretende redirecionar os usuários para o novo servidor primário, como ao alterar as cadeias de conexão ou as entradas DNS.
- Identificar e, como alternativa, criar os logons que devem estar presentes no banco de dados mestre no novo servidor primário e verificar se esses logons têm permissões apropriadas no banco de dados mestre, se houver. Para obter mais informações, confira [SQL Database security after disaster recovery](active-geo-replication-security-configure.md)
- Identificar as regras de alerta que precisarão ser atualizadas para mapear para o novo banco de dados primário.
- Documentar a configuração de auditoria no banco de dados primário atual
- Execute uma [análise de recuperação de desastre](disaster-recovery-drills.md). Para simular uma interrupção para a restauração geográfica, você pode excluir ou renomear o banco de dados de origem para causar a falha de conectividade do aplicativo. Para simular uma interrupção usando grupos de failover, você pode desabilitar o aplicativo Web ou a máquina virtual conectada ao banco de dados ou fazer failover no banco de dados para causar falhas de conectividade no aplicativo.

## <a name="when-to-initiate-recovery"></a>Quando iniciar a recuperação

A operação de recuperação afeta o aplicativo. Ela exige a alteração da cadeia de conexão SQL, ou o redirecionamento usando DNS, e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o objetivo do tempo de recuperação do aplicativo. Quando o aplicativo for implantado na produção você deverá executar o monitoramento regular da integridade do aplicativo e usar os seguintes pontos de dados para ter certeza de a recuperação é garantida:

1. Falha de conectividade permanente da camada do aplicativo com o banco de dados.
2. O Portal do Azure mostra um alerta sobre um incidente na região com grande impacto.

> [!NOTE]
> Se você estiver usando grupos de failover e escolher o failover automático, o processo de recuperação é automático e transparente para o aplicativo.

Dependendo da tolerância a tempo de inatividade de seu aplicativo e possível responsabilidade comercial, você pode considerar as opções de recuperação a seguir.

Use [Obter Banco de Dados Recuperável](/previous-versions/azure/reference/dn800985(v=azure.100)) (*LastAvailableBackupDate*) para obter o ponto de restauração com replicação geográfica mais recente.

## <a name="wait-for-service-recovery"></a>Aguarde a recuperação de serviço

As equipes do Azure trabalham cuidadosamente para restaurar a disponibilidade do serviço o mais rapidamente possível, mas dependendo da causa raiz, isso pode levar horas ou dias.  Se seu aplicativo pode tolerar tempo de inatividade significativo, você pode simplesmente esperar a conclusão da recuperação. Nesse caso, nenhuma ação sua é necessária. Você pode ver o status atual do serviço no nosso [Painel de Integridade do Serviço Azure](https://azure.microsoft.com/status/). Após a recuperação da região, a disponibilidade do aplicativo é restaurada.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Fazer failover para servidor secundário replicado geograficamente no grupo de failover

Se o tempo de inatividade do aplicativo puder resultar em responsabilidade comercial, você deverá usar grupos de failover. Isso permitirá que o aplicativo restaure rapidamente a disponibilidade em uma região diferente em caso de uma interrupção. Para um tutorial, confira [Implementar um banco de dados distribuído geograficamente](geo-distributed-application-configure-tutorial.md).

Para restaurar a disponibilidade do(s) banco(s) de dados, você precisa iniciar o failover para o servidor secundário usando um dos métodos com suporte.

Use um dos guias a seguir para fazer failover para um banco de dados secundário replicado geograficamente:

- [Fazer failover para um servidor secundário replicado geograficamente usando o portal do Azure](active-geo-replication-configure-portal.md)
- [Failover para o servidor secundário usando o PowerShell](scripts/setup-geodr-and-failover-database-powershell.md)
- [Fazer failover para um servidor secundário usando Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Recuperação usando a restauração geográfica

Se o tempo de inatividade do aplicativo não resultar em responsabilidade comercial, você poderá usar a [restauração geográfica](recovery-using-backups.md) como um método para recuperar seus bancos de dados de aplicativo. Ela cria uma cópia do banco de dados com base em seu último backup com redundância geográfica.

## <a name="configure-your-database-after-recovery"></a>Configurar o banco de dados após a recuperação

Se estiver usando a restauração geográfica para se recuperar de uma interrupção, você deverá se certificar de que a conectividade com os novos bancos de dados esteja configurada corretamente para que o funcionamento normal do aplicativo possa ser retomado. Esta é uma lista de verificação de tarefas para preparar para produção o seu banco de dados recuperado.

### <a name="update-connection-strings"></a>Atualizar cadeias de conexão

Como o banco de dados recuperado reside em um servidor diferente, você precisa atualizar a cadeia de conexão do aplicativo para apontar para esse servidor.

Para saber mais sobre como alterar as cadeias de conexão, confira a linguagem de desenvolvimento apropriada para sua [biblioteca de conexão](connect-query-content-reference-guide.md#libraries).

### <a name="configure-firewall-rules"></a>Configurar regras de firewall

Você precisa certificar-se de que as regras de firewall configuradas no servidor e no banco de dados correspondam àquelas que foram configuradas no servidor primário e no banco de dados primário. Para saber mais, consulte [Como definir configurações de firewall (Banco de Dados SQL do Azure)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Configurar logons e usuários do banco de dados

Você deve verificar se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Para obter mais informações, consulte [configuração de segurança para replicação geográfica](active-geo-replication-security-configure.md).

> [!NOTE]
> Você deve configurar e testar suas regras de firewall de servidor e logons (e suas permissões) durante uma análise de recuperação de desastre. Esses objetos no nível de servidor e sua configuração podem não estar disponíveis durante a interrupção.

### <a name="setup-telemetry-alerts"></a>Configurar alertas de telemetria

Você precisa certificar-se de que as configurações de regra de alerta existentes sejam atualizadas para mapear para o banco de dados recuperado e para o outro servidor.

Para obter mais informações sobre regras de alerta de banco de dados, consulte [Receber notificações de alerta](../../azure-monitor/alerts/alerts-overview.md) e [Acompanhar a integridade do serviço](../../service-health/service-notifications.md).

### <a name="enable-auditing"></a>Habilitar a auditoria

Se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a auditoria após a recuperação do banco de dados. Para obter mais informações, consulte [Auditoria de banco de dados](../../azure-sql/database/auditing-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os backups automatizados do banco de dados SQL do Azure, confira [backups automatizados](automated-backups-overview.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, confira [Cenários de continuidade](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](recovery-using-backups.md)