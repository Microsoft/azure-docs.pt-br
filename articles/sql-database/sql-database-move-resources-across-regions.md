---
title: Como mover recursos do banco de dados SQL do Azure para outra região | Microsoft Docs
description: Saiba como mover o banco de dados SQL do Azure, o pool elástico do SQL do Azure ou a instância gerenciada do SQL do Azure para outra região.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 06/25/2019
ms.openlocfilehash: 26d3377767a99a7291e73522152d4c6dbf389067
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444845"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Como mover recursos do SQL do Azure para outra região

Este artigo ensina um fluxo de trabalho genérico sobre como mover o banco de dados do banco de dados SQL do Azure, o pool elástico e a instância gerenciada para uma nova região. 

## <a name="overview"></a>Visão geral

Há vários cenários em que você deseja mover seus recursos do SQL do Azure existentes de uma região para outra. Por exemplo, você expande sua empresa para uma nova região e deseja otimizá-la para a nova base de clientes. Ou você precisa mover as operações para uma região diferente por motivos de conformidade. Ou o Azure lançou uma região totalmente nova que fornece uma proximidade melhor e melhora a experiência do cliente.  

Este artigo fornece um fluxo de trabalho geral para mover recursos para uma região diferente. O fluxo de trabalho consiste nas seguintes etapas: 

- Verificar os pré-requisitos para a movimentação 
- Preparar para mover os recursos no escopo
- Monitorar o processo de preparação
- Testar o processo de movimentação
- Iniciar a movimentação real 
- Remover os recursos da região de origem 


> [!NOTE]
> Este artigo se aplica a migrações na nuvem pública do Azure ou dentro da mesma nuvem soberanas. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Mover banco de dados individual

### <a name="verify-prerequisites"></a>Verificar pré-requisitos 

1. Crie um servidor lógico de destino para cada servidor de origem. 
1. Configure o firewall com as exceções corretas usando o [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configure os servidores lógicos com os logons corretos. Se você não for o administrador da assinatura ou o administrador do SQL Server, trabalhe com o administrador para atribuir as permissões necessárias. Para obter mais informações, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md). 
1. Se seus bancos de dados forem criptografados com TDE e usar sua própria chave de criptografia no cofre de chaves do Azure, verifique se o material de criptografia correto está provisionado nas regiões de destino. Para obter mais informações, consulte [Azure SQL Transparent Data Encryption com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Se a auditoria em nível de banco de dados estiver habilitada, desabilite-a e habilite a auditoria no nível de servidor. Após o failover, a auditoria no nível do banco de dados exigirá o tráfego entre regiões, que não será desejado ou possível após a movimentação. 
1. Para auditorias em nível de servidor, verifique se:
   - O contêiner de armazenamento, Log Analytics ou Hub de eventos com os logs de auditoria existentes são movidos para a região de destino. 
   - A auditoria está configurada no servidor de destino. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing.md). 
1. Se sua instância tiver uma EPD (política de retenção de longo prazo), os backups EPD existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo que o servidor seja excluído. 

  > [!NOTE]
  > Isso será insuficiente para a movimentação entre a nuvem soberanas e uma região pública. Essa migração exigirá a movimentação dos backups EPD para o servidor de destino, que não tem suporte no momento. 

### <a name="prepare-resources"></a>Preparar recursos

1. Crie um [grupo de failover](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) entre o servidor lógico da origem e o servidor lógico do destino.  
1. Adicione os bancos de dados que você deseja mover para o grupo de failover. 
    - A replicação de todos os bancos de dados adicionados será iniciada automaticamente. Para obter mais informações, consulte [práticas recomendadas para usar grupos de failover com bancos de dados individuais](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Monitorar o processo de preparação

Você pode chamar o [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) periodicamente para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto de saída `Get-AzSqlDatabaseFailoverGroup` de inclui uma propriedade para o replicationstate: 
   - **Replicationstate = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ser realizado com êxito. 
   - **Replicationstate = 0** (Propagação) indica que o banco de dados ainda não foi propagado e uma tentativa de failover falhará. 

### <a name="test-synchronization"></a>Sincronização de teste

Depois  que replicationstate `2`é, conecte-se a cada banco de dados ou subconjunto de bancos de `<fog-name>.secondary.database.windows.net` dado usando o ponto de extremidade secundário e execute qualquer consulta em relação aos bancos de dados para garantir a conectividade, a configuração de segurança apropriada e a data replicação. 

### <a name="initiate-the-move"></a>Iniciar a movimentação

1. Conecte-se ao servidor de destino usando o `<fog-name>.secondary.database.windows.net`ponto de extremidade secundário.
1. Use o [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerenciada secundária para ser a primária com sincronização completa. Esta operação terá sucesso ou será revertida. 
1. Verifique se o comando foi concluído com êxito usando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-databases"></a>Remover os bancos de dados de origem

Quando a movimentação for concluída, remova os recursos na região de origem para evitar encargos desnecessários. 

1. Exclua o grupo de failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Exclua cada banco de dados de origem usando [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) para cada um dos bancos de dados no servidor de origem. Isso irá encerrar automaticamente os links de replicação geográfica. 
1. Exclua o servidor de origem usando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Remova o cofre de chaves, os contêineres de armazenamento de auditoria, o Hub de eventos, a instância do AAD e outros recursos dependentes para parar de ser cobrados por eles. 

## <a name="move-elastic-pools"></a>Mover pools elásticos

### <a name="verify-prerequisites"></a>Verificar pré-requisitos 

1. Crie um servidor lógico de destino para cada servidor de origem. 
1. Configure o firewall com as exceções corretas usando o [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configure os servidores lógicos com os logons corretos. Se você não for o administrador da assinatura ou o administrador do SQL Server, trabalhe com o administrador para atribuir as permissões necessárias. Para obter mais informações, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md). 
1. Se seus bancos de dados forem criptografados com TDE e usar sua própria chave de criptografia no cofre de chaves do Azure, verifique se o material de criptografia correto está provisionado na região de destino.
1. Crie um pool elástico de destino para cada pool elástico de origem, certificando-se de que o pool seja criado na mesma camada de serviço, com o mesmo nome e o mesmo tamanho. 
1. Se uma auditoria no nível do banco de dados estiver habilitada, desabilite-a e habilite a auditoria no nível do servidor. Após o failover, a auditoria no nível do banco de dados exigirá tráfego entre regiões, o que não é desejado ou possível após a movimentação. 
1. Para auditorias em nível de servidor, verifique se:
    - O contêiner de armazenamento, Log Analytics ou Hub de eventos com os logs de auditoria existentes são movidos para a região de destino.
    - A configuração de auditoria está configurada no servidor de destino. Para obter mais informações, consulte [auditoria do banco de dados SQL](sql-database-auditing.md).
1. Se sua instância tiver uma EPD (política de retenção de longo prazo), os backups EPD existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo que o servidor seja excluído. 

  > [!NOTE]
  > Isso será insuficiente para a movimentação entre a nuvem soberanas e uma região pública. Essa migração exigirá a movimentação dos backups EPD para o servidor de destino, que não tem suporte no momento. 

### <a name="prepare-to-move"></a>Preparar para mover
 
1.  Crie um [grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) separado entre cada pool elástico no servidor lógico de origem e seu pool elástico de contraparte no servidor de destino. 
1.  Adicione todos os bancos de dados no pool ao grupo de failover. 
    - A replicação dos bancos de dados adicionados será iniciada automaticamente. Para obter mais informações, consulte [práticas recomendadas para grupos de failover com pools elásticos](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Embora seja possível criar um grupo de failover que inclua vários pools elásticos, é altamente recomendável que você crie um grupo de failover separado para cada pool. Se você tiver um grande número de bancos de dados em vários pools elásticos que você precisa mover, poderá executar as etapas de preparação em paralelo e, em seguida, iniciar a etapa de movimentação em paralelo. Esse processo será dimensionado melhor e levará menos tempo em comparação a ter vários pools elásticos no mesmo grupo de failover. 

### <a name="monitor-the-preparation-process"></a>Monitorar o processo de preparação

Você pode chamar o [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) periodicamente para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto de saída `Get-AzSqlDatabaseFailoverGroup` de inclui uma propriedade para o replicationstate: 
   - **Replicationstate = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ser realizado com êxito. 
   - **Replicationstate = 0** (Propagação) indica que o banco de dados ainda não foi propagado e uma tentativa de failover falhará. 

### <a name="test-synchronization"></a>Sincronização de teste
 
Depois  que replicationstate `2`é, conecte-se a cada banco de dados ou subconjunto de bancos de `<fog-name>.secondary.database.windows.net` dado usando o ponto de extremidade secundário e execute qualquer consulta em relação aos bancos de dados para garantir a conectividade, a configuração de segurança apropriada e a data replicação. 

### <a name="initiate-the-move"></a>Iniciar a movimentação
 
1. Conecte-se ao servidor de destino usando o `<fog-name>.secondary.database.windows.net`ponto de extremidade secundário.
1. Use o [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerenciada secundária para ser a primária com sincronização completa. Esta operação terá sucesso ou será revertida. 
1. Verifique se o comando foi concluído com êxito usando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-elastic-pools"></a>Remover os pools elásticos de origem
 
Quando a movimentação for concluída, remova os recursos na região de origem para evitar encargos desnecessários. 

1. Exclua o grupo de failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Exclua cada pool elástico de origem no servidor de origem usando [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Exclua o servidor de origem usando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Remova o cofre de chaves, os contêineres de armazenamento de auditoria, o Hub de eventos, a instância do AAD e outros recursos dependentes para parar de ser cobrados por eles. 

## <a name="move-managed-instance"></a>Mover instância gerenciada

### <a name="verify-prerequisites"></a>Verificar pré-requisitos
 
1. Para cada instância gerenciada de origem, crie uma instância gerenciada de destino do mesmo tamanho na região de destino.  
1. Configure a rede para uma instância gerenciada. Para obter mais informações, consulte [configuração de rede](sql-database-howto-managed-instance.md#network-configuration).
1. Configure o banco de dados mestre de destino com os logons corretos. Se você não for o administrador da assinatura ou o administrador do SQL Server, trabalhe com o administrador para atribuir as permissões necessárias. 
1. Se seus bancos de dados forem criptografados com TDE e usar sua própria chave de criptografia no cofre de chaves do Azure, verifique se o AKV com chaves de criptografia idênticas existe nas regiões de origem e de destino. Para obter mais informações, consulte [TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Se a auditoria estiver habilitada para a instância, verifique se:
    - O contêiner de armazenamento ou Hub de eventos com os logs existentes é movido para a região de destino. 
    - A auditoria está configurada na instância de destino. Para obter mais informações, consulte [auditoria com instância gerenciada](sql-database-managed-instance-auditing.md).
1. Se sua instância tiver uma EPD (política de retenção de longo prazo), os backups EPD existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo que o servidor seja excluído. 

  > [!NOTE]
  > Isso será insuficiente para a movimentação entre a nuvem soberanas e uma região pública. Essa migração exigirá a movimentação dos backups EPD para o servidor de destino, que não tem suporte no momento. 

### <a name="prepare-resources"></a>Preparar recursos

Crie um grupo de failover entre cada instância de origem e a instância de destino correspondente.
    - A replicação de todos os bancos de dados em cada instância será iniciada automaticamente. Consulte [grupos de failover automático](sql-database-auto-failover-group.md) para obter mais informações.

 
### <a name="monitor-the-preparation-process"></a>Monitorar o processo de preparação

Você pode chamar o [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) periodicamente para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto de saída `Get-AzSqlDatabaseFailoverGroup` de inclui uma propriedade para o replicationstate: 
   - **Replicationstate = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ser realizado com êxito. 
   - **Replicationstate = 0** (Propagação) indica que o banco de dados ainda não foi propagado e uma tentativa de failover falhará. 

### <a name="test-synchronization"></a>Sincronização de teste

Depois  que replicationstate `2`é, conecte-se a cada banco de dados ou subconjunto de bancos de `<fog-name>.secondary.database.windows.net` dado usando o ponto de extremidade secundário e execute qualquer consulta em relação aos bancos de dados para garantir a conectividade, a configuração de segurança apropriada e a data replicação. 

### <a name="initiate-the-move"></a>Iniciar a movimentação 

1. Conecte-se ao servidor de destino usando o `<fog-name>.secondary.database.windows.net`ponto de extremidade secundário.
1. Use o [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) para mudar a instância gerenciada secundária para ser a primária com sincronização completa. Esta operação terá sucesso ou será revertida. 
1. Verifique se o comando foi concluído com êxito usando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-managed-instances"></a>Remover as instâncias gerenciadas de origem
Quando a movimentação for concluída, remova os recursos na região de origem para evitar encargos desnecessários. 

1. Exclua o grupo de failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Isso removerá a configuração do grupo de failover e encerrará os links de replicação geográfica entre as duas instâncias. 
1. Exclua a instância gerenciada de origem usando [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Remova todos os recursos adicionais no grupo de recursos, como o cluster virtual, a rede virtual e o grupo de segurança. 

## <a name="next-steps"></a>Próximas etapas 

[Gerencie](sql-database-manage-after-migration.md) seu banco de dados SQL do Azure depois que ele tiver sido migrado. 

