---
title: "Recuperação de desastre do Banco de Dados SQL | Microsoft Docs"
description: "Saiba como recuperar um banco de dados de uma interrupção do datacenter regional ou de uma falha com os recursos de Restauração geográfica e Replicação geográfica ativa do Banco de dados SQL do Azure."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 4800960e-3f9d-40ce-9e55-fb7f2784c067
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/14/2017
ms.author: sashan
ms.openlocfilehash: 535dcd8b03a0fd92eaa5800a924ca17fc958d630
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar um Banco de Dados SQL ou fazer failover para um secundário
O Banco de Dados SQL do Azure oferece os seguintes recursos para a recuperação de uma paralisação:

* [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
* [Restauração geográfica](sql-database-recovery-using-backups.md#point-in-time-restore)

Para saber mais sobre os cenários de continuidade dos negócios e os recursos com suporte para esses cenários, confira [Continuidade dos negócios](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Prepare-se para o caso de uma interrupção
Para ter êxito com a recuperação para outra região de dados usando a replicação geográfica ativa ou os backups com redundância geográfica, você precisará preparar um servidor em outra interrupção de data center para que ele se torne o novo servidor primário, caso seja necessário, bem como definir as etapas documentadas e testadas para garantir uma recuperação simples. Essas etapas de preparação incluem:

* Identificar o servidor lógico em outra região para que ele se torne o novo servidor primário. Com a replicação geográfica ativa, será pelo menos um e, talvez, cada um dos servidores secundários. Para a restauração geográfica, geralmente será um servidor na [região emparelhada](../best-practices-availability-paired-regions.md) para a região na qual o banco de dados está localizado.
* Identificar e, como alternativa, definir as regras de firewall no nível de servidor necessárias para que os usuários acessem o novo banco de dados primário.
* Determinar como você pretende redirecionar os usuários para o novo servidor primário, como ao alterar as cadeias de conexão ou as entradas DNS.
* Identificar e, como alternativa, criar os logons que devem estar presentes no banco de dados mestre no novo servidor primário e verificar se esses logons têm permissões apropriadas no banco de dados mestre, se houver. Para obter mais informações, confira [SQL Database security after disaster recovery](sql-database-geo-replication-security-config.md)
* Identificar as regras de alerta que precisarão ser atualizadas para mapear para o novo banco de dados primário.
* Documentar a configuração de auditoria no banco de dados primário atual
* Executar uma [análise de recuperação de desastre](sql-database-disaster-recovery-drills.md). Para simular uma interrupção para a restauração geográfica, você pode excluir ou renomear o banco de dados de origem para causar a falha de conectividade do aplicativo. Para simular uma interrupção na replicação geográfica ativa, você pode desabilitar o aplicativo Web ou a máquina virtual conectada ao banco de dados ou fazer failover no banco de dados para causar falhas de conectividade no aplicativo.

## <a name="when-to-initiate-recovery"></a>Quando iniciar a recuperação
A operação de recuperação afeta o aplicativo. Ela exige a alteração da cadeia de conexão SQL, ou o redirecionamento usando DNS, e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o objetivo do tempo de recuperação do aplicativo. Quando o aplicativo for implantado na produção você deverá executar o monitoramento regular da integridade do aplicativo e usar os seguintes pontos de dados para ter certeza de a recuperação é garantida:

1. Falha de conectividade permanente da camada do aplicativo com o banco de dados.
2. O Portal do Azure mostra um alerta sobre um incidente na região com grande impacto.
3. O servidor de Banco de Dados SQL do Azure está marcado como degradado.

Dependendo da tolerância a tempo de inatividade de seu aplicativo e possível responsabilidade comercial, você pode considerar as opções de recuperação a seguir.

Use [Obter Banco de Dados Recuperável](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obter o ponto de restauração com replicação geográfica mais recente.

## <a name="wait-for-service-recovery"></a>Aguarde a recuperação de serviço
As equipes do Azure trabalham cuidadosamente para restaurar a disponibilidade do serviço o mais rapidamente possível, mas dependendo da causa raiz, isso pode levar horas ou dias.  Se seu aplicativo pode tolerar tempo de inatividade significativo, você pode simplesmente esperar a conclusão da recuperação. Nesse caso, nenhuma ação sua é necessária. Você pode ver o status atual do serviço no nosso [Painel de Integridade do Serviço Azure](https://azure.microsoft.com/status/). Após a recuperação da região, a disponibilidade do aplicativo será restaurada.

## <a name="fail-over-to-geo-replicated-secondary-database"></a>Failover para banco de dados secundário replicado geograficamente
Se o tempo de inatividade do aplicativo puder resultar em responsabilidade comercial, você deverá usar bancos de dados replicados geograficamente em seu aplicativo. Isso permitirá que o aplicativo restaure rapidamente a disponibilidade em uma região diferente em caso de uma interrupção. Saiba como [configurar a replicação geográfica](sql-database-geo-replication-portal.md).

Para restaurar a disponibilidade do(s) banco(s) de dados, você precisa iniciar o failover para o secundário replicado geograficamente usando um dos métodos com suporte.

Use um dos guias a seguir para fazer failover para um banco de dados secundário replicado geograficamente:

* [Fazer failover para um secundário replicado geograficamente usando o Portal do Azure](sql-database-geo-replication-portal.md)
* [Fazer failover para um secundário replicado geograficamente usando o PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
* [Fazer failover para um secundário replicado geograficamente usando o T-SQL](/sql/t-sql/statements/alter-database-azure-sql-database)

## <a name="recover-using-geo-restore"></a>Recuperação usando a restauração geográfica
Se o tempo de inatividade do aplicativo não resultar em responsabilidade de negócios, você poderá usar a [restauração geográfica](sql-database-recovery-using-backups.md) como método de recuperar os bancos de dados do aplicativo. Ela cria uma cópia do banco de dados com base em seu último backup com redundância geográfica.

## <a name="configure-your-database-after-recovery"></a>Configurar o banco de dados após a recuperação
Se estiver usando failover de replicação geográfica ou a restauração geográfica para se recuperar de uma interrupção, você deverá se certificar de que a conectividade com os novos bancos de dados esteja configurada corretamente para que o funcionamento normal do aplicativo possa ser retomado. Esta é uma lista de verificação de tarefas para preparar para produção o seu banco de dados recuperado.

### <a name="update-connection-strings"></a>Atualizar cadeias de conexão
Já que o banco de dados recuperado residirá em um servidor diferente, você precisa atualizar a cadeia de conexão do seu aplicativo para apontar para esse servidor.

Para saber mais sobre como alterar as cadeias de conexão, confira a linguagem de desenvolvimento apropriada para sua [biblioteca de conexão](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurar regras de firewall
Você precisa certificar-se de que as regras de firewall configuradas no servidor e no banco de dados correspondam àquelas que foram configuradas no servidor primário e no banco de dados primário. Para saber mais, consulte [Como definir configurações de firewall (Banco de Dados SQL do Azure)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Configurar logons e usuários do banco de dados
Você deve verificar se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Para saber mais, confira [Configuração de segurança para replicação geográfica](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Você deve configurar e testar suas regras de firewall de servidor e logons (e suas permissões) durante uma análise de recuperação de desastre. Esses objetos no nível de servidor e sua configuração podem não estar disponíveis durante a interrupção.
> 
> 

### <a name="setup-telemetry-alerts"></a>Configurar alertas de telemetria
Você precisa certificar-se de que as configurações de regra de alerta existentes sejam atualizadas para mapear para o banco de dados recuperado e para o outro servidor.

Para obter mais informações sobre regras de alerta de banco de dados, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) e [Acompanhar a integridade do serviço](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Habilitar a auditoria
Se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a auditoria após a recuperação do banco de dados. Para obter mais informações, consulte [Auditoria de banco de dados](sql-database-auditing.md).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, confira [Cenários de continuidade](sql-database-business-continuity.md)
* Para saber mais sobre como usar backups automatizados para recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)

