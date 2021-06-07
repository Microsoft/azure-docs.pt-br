---
title: Mover recursos para uma nova região
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba como mover seu banco de dados ou instância gerenciada para outra região.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94594157"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Mover recursos para uma nova região – banco de dados SQL do Azure & SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo ensina um fluxo de trabalho genérico sobre como mover seu banco de dados ou instância gerenciada para uma nova região.

## <a name="overview"></a>Visão geral

Há vários cenários em que você deseja mover seu banco de dados ou instância gerenciada existente de uma região para outra. Por exemplo, você está expandindo sua empresa para uma nova região e deseja otimizá-la para a nova base de clientes. Ou você precisa mover as operações para uma região diferente por motivos de conformidade. Ou o Azure lançou uma nova região que fornece uma proximidade melhor e melhora a experiência do cliente.  

Este artigo fornece um fluxo de trabalho geral para mover recursos para uma região diferente. O fluxo de trabalho consiste nas seguintes etapas:

1. Verifique os pré-requisitos para a movimentação.
1. Prepare-se para mover os recursos no escopo.
1. Monitore o processo de preparação.
1. Teste o processo de movimentação.
1. Inicie a movimentação real.
1. Remova os recursos da região de origem.

> [!NOTE]
> Este artigo se aplica a migrações na nuvem pública do Azure ou na mesma nuvem soberanas.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Mover um banco de dados

### <a name="verify-prerequisites"></a>Verificar pré-requisitos

1. Crie um servidor de destino para cada servidor de origem.
1. Configure o firewall com as exceções certas usando o [PowerShell](scripts/create-and-configure-database-powershell.md).  
1. Configure os servidores com os logons corretos. Se você não for o administrador da assinatura ou o administrador do SQL Server, trabalhe com o administrador para atribuir as permissões necessárias. Para obter mais informações, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](active-geo-replication-security-configure.md).
1. Se os bancos de dados forem criptografados com Transparent Data Encryption e usar sua própria chave de criptografia no Azure Key Vault, verifique se o material de criptografia correto está provisionado nas regiões de destino. Para obter mais informações, consulte [criptografia de dados transparente do SQL Azure com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Se a auditoria em nível de banco de dados estiver habilitada, desabilite-a e habilite a auditoria no nível de servidor. Após o failover, a auditoria no nível do banco de dados exigirá o tráfego entre regiões, que não é desejado ou possível após a movimentação.
1. Para auditorias em nível de servidor, verifique se:
   - O contêiner de armazenamento, Log Analytics ou Hub de eventos com os logs de auditoria existentes são movidos para a região de destino.
   - A auditoria está configurada no servidor de destino. Para obter mais informações, consulte Introdução [à auditoria do banco de dados SQL](../../azure-sql/database/auditing-overview.md).
1. Se sua instância tiver uma EPD (política de retenção de longo prazo), os backups EPD existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo se o servidor for excluído.

      > [!NOTE]
      > Isso será insuficiente para a movimentação entre a nuvem soberanas e uma região pública. Essa migração exigirá a movimentação dos backups EPD para o servidor de destino, que não tem suporte no momento.

### <a name="prepare-resources"></a>Preparar recursos

1. Crie um [grupo de failover](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) entre o servidor da origem e o servidor do destino.  
1. Adicione os bancos de dados que você deseja mover para o grupo de failover.
  
    A replicação de todos os bancos de dados adicionados será iniciada automaticamente. Para obter mais informações, consulte [práticas recomendadas para usar grupos de failover com bancos de dados individuais](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Monitorar o processo de preparação

Você pode chamar o [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) periodicamente para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto de saída de `Get-AzSqlDatabaseFailoverGroup` inclui uma propriedade para o **replicationstate**:

- **Replicationstate = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ter o failover com segurança.
- **Replicationstate = 0** (propagação) indica que o banco de dados ainda não foi propagado e uma tentativa de failover falhará.

### <a name="test-synchronization"></a>Sincronização de teste

Depois de **replicationstate** `2` , conecte-se a cada banco de dados ou subconjunto de bancos de dado usando o ponto de extremidade secundário `<fog-name>.secondary.database.windows.net` e execute qualquer consulta em relação aos bancos de dados para garantir a conectividade, a configuração de segurança adequada e a replicação.

### <a name="initiate-the-move"></a>Inicie a movimentação

1. Conecte-se ao servidor de destino usando o ponto de extremidade secundário `<fog-name>.secondary.database.windows.net` .
1. Use o [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerenciada secundária para ser a primária com sincronização completa. Esta operação terá sucesso ou será revertida.
1. Verifique se o comando foi concluído com êxito usando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado.

### <a name="remove-the-source-databases"></a>Remover os bancos de dados de origem

Quando a movimentação for concluída, remova os recursos na região de origem para evitar encargos desnecessários.

1. Exclua o grupo de failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Exclua cada banco de dados de origem usando [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) para cada um dos bancos de dados no servidor de origem. Isso irá encerrar automaticamente os links de replicação geográfica.
1. Exclua o servidor de origem usando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Remova o cofre de chaves, os contêineres de armazenamento de auditoria, o Hub de eventos, a instância do Azure Active Directory (Azure AD) e outros recursos dependentes para parar de ser cobrados por eles.

## <a name="move-elastic-pools"></a>Mover pools elásticos

### <a name="verify-prerequisites"></a>Verificar pré-requisitos

1. Crie um servidor de destino para cada servidor de origem.
1. Configure o firewall com as exceções corretas usando o [PowerShell](scripts/create-and-configure-database-powershell.md).
1. Configure os servidores com os logons corretos. Se você não for o administrador da assinatura ou administrador do servidor, trabalhe com o administrador para atribuir as permissões necessárias. Para obter mais informações, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](active-geo-replication-security-configure.md).
1. Se os bancos de dados forem criptografados com Transparent Data Encryption e usar sua própria chave de criptografia no Azure Key Vault, verifique se o material de criptografia correto está provisionado na região de destino.
1. Crie um pool elástico de destino para cada pool elástico de origem, certificando-se de que o pool seja criado na mesma camada de serviço, com o mesmo nome e o mesmo tamanho.
1. Se uma auditoria no nível do banco de dados estiver habilitada, desabilite-a e habilite a auditoria no nível do servidor. Após o failover, a auditoria no nível do banco de dados exigirá tráfego entre regiões, o que não é desejado ou possível após a movimentação.
1. Para auditorias em nível de servidor, verifique se:
    - O contêiner de armazenamento, Log Analytics ou Hub de eventos com os logs de auditoria existentes são movidos para a região de destino.
    - A configuração de auditoria está configurada no servidor de destino. Para obter mais informações, consulte [auditoria do banco de dados SQL](../../azure-sql/database/auditing-overview.md).
1. Se sua instância tiver uma EPD (política de retenção de longo prazo), os backups EPD existentes permanecerão associados ao servidor atual. Como o servidor de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando o servidor de origem, mesmo que o servidor seja excluído.

      > [!NOTE]
      > Isso será insuficiente para a movimentação entre a nuvem soberanas e uma região pública. Essa migração exigirá a movimentação dos backups EPD para o servidor de destino, que não tem suporte no momento.

### <a name="prepare-to-move"></a>Preparar para mover

1. Crie um [grupo de failover](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) separado entre cada pool elástico no servidor de origem e seu pool elástico de contraparte no servidor de destino.
1. Adicione todos os bancos de dados no pool ao grupo de failover.

    A replicação dos bancos de dados adicionados será iniciada automaticamente. Para obter mais informações, consulte [práticas recomendadas para grupos de failover com pools elásticos](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > Embora seja possível criar um grupo de failover que inclua vários pools elásticos, é altamente recomendável que você crie um grupo de failover separado para cada pool. Se você tiver um grande número de bancos de dados em vários pools elásticos que você precisa mover, poderá executar as etapas de preparação em paralelo e, em seguida, iniciar a etapa de movimentação em paralelo. Esse processo será dimensionado melhor e levará menos tempo em comparação a ter vários pools elásticos no mesmo grupo de failover.

### <a name="monitor-the-preparation-process"></a>Monitorar o processo de preparação

Você pode chamar o [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) periodicamente para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto de saída de `Get-AzSqlDatabaseFailoverGroup` inclui uma propriedade para o **replicationstate**:

- **Replicationstate = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ter o failover com segurança.
- **Replicationstate = 0** (propagação) indica que o banco de dados ainda não foi propagado e uma tentativa de failover falhará.

### <a name="test-synchronization"></a>Sincronização de teste

Depois que **replicationstate** é `2` , conecte-se a cada banco de dados ou subconjunto de bancos de dado usando o ponto de extremidade secundário `<fog-name>.secondary.database.windows.net` e execute qualquer consulta em relação aos bancos de dados para garantir a conectividade, a configuração de segurança adequada e a replicação do dado.

### <a name="initiate-the-move"></a>Inicie a movimentação

1. Conecte-se ao servidor de destino usando o ponto de extremidade secundário `<fog-name>.secondary.database.windows.net` .
1. Use o [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerenciada secundária para ser a primária com sincronização completa. Esta operação terá sucesso ou será revertida.
1. Verifique se o comando foi concluído com êxito usando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado.

### <a name="remove-the-source-elastic-pools"></a>Remover os pools elásticos de origem

Quando a movimentação for concluída, remova os recursos na região de origem para evitar encargos desnecessários.

1. Exclua o grupo de failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Exclua cada pool elástico de origem no servidor de origem usando [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool).
1. Exclua o servidor de origem usando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Remova o cofre de chaves, os contêineres de armazenamento de auditoria, o Hub de eventos, a instância do Azure AD e outros recursos dependentes para parar de ser cobrados por eles.

## <a name="move-a-managed-instance"></a>Mover uma instância gerenciada

### <a name="verify-prerequisites"></a>Verificar pré-requisitos

1. Para cada instância gerenciada de origem, crie uma instância de destino do SQL Instância Gerenciada do mesmo tamanho na região de destino.  
1. Configure a rede para uma instância gerenciada. Para obter mais informações, consulte [configuração de rede](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Configure o banco de dados mestre de destino com os logons corretos. Se você não for a assinatura ou o administrador do SQL Instância Gerenciada, trabalhe com o administrador para atribuir as permissões necessárias.
1. Se os bancos de dados forem criptografados com Transparent Data Encryption e usar sua própria chave de criptografia no Azure Key Vault, verifique se o Azure Key Vault com chaves de criptografia idênticas existe nas regiões de origem e de destino. Para obter mais informações, consulte [Transparent Data Encryption com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Se a auditoria estiver habilitada para a instância gerenciada, verifique se:
    - O contêiner de armazenamento ou Hub de eventos com os logs existentes é movido para a região de destino.
    - A auditoria está configurada na instância de destino. Para obter mais informações, consulte [auditoria com o SQL instância gerenciada](../managed-instance/auditing-configure.md).
1. Se sua instância tiver uma EPD (política de retenção de longo prazo), os backups EPD existentes permanecerão associados à instância atual. Como a instância de destino é diferente, você poderá acessar os backups LTR mais antigos na região de origem usando a instância de origem, mesmo que a instância seja excluída.

  > [!NOTE]
  > Isso será insuficiente para a movimentação entre a nuvem soberanas e uma região pública. Essa migração exigirá a movimentação dos backups EPD para a instância de destino, que não tem suporte no momento.

### <a name="prepare-resources"></a>Preparar recursos

Crie um grupo de failover entre cada instância gerenciada de origem e a instância de destino correspondente do SQL Instância Gerenciada.

A replicação de todos os bancos de dados em cada instância será iniciada automaticamente. Para obter mais informações, consulte [grupos de failover automático](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Monitorar o processo de preparação

Você pode chamar o [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) periodicamente para monitorar a replicação de seus bancos de dados da origem para o destino. O objeto de saída de `Get-AzSqlDatabaseFailoverGroup` inclui uma propriedade para o **replicationstate**:

- **Replicationstate = 2** (CATCH_UP) indica que o banco de dados está sincronizado e pode ter o failover com segurança.
- **Replicationstate = 0** (propagação) indica que o banco de dados ainda não foi propagado e uma tentativa de failover falhará.

### <a name="test-synchronization"></a>Sincronização de teste

Depois que **replicationstate** é `2` , conecte-se a cada banco de dados ou subconjunto de bancos de dado usando o ponto de extremidade secundário `<fog-name>.secondary.database.windows.net` e execute qualquer consulta em relação aos bancos de dados para garantir a conectividade, a configuração de segurança adequada e a replicação do dado.

### <a name="initiate-the-move"></a>Inicie a movimentação

1. Conecte-se à instância gerenciada de destino usando o ponto de extremidade secundário `<fog-name>.secondary.database.windows.net` .
1. Use o [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerenciada secundária para ser a primária com sincronização completa. Esta operação terá sucesso ou será revertida.
1. Verifique se o comando foi concluído com êxito usando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada DNS CNAME aponta para o endereço IP da região de destino. Se o comando switch falhar, o CNAME não será atualizado.

### <a name="remove-the-source-managed-instances"></a>Remover as instâncias gerenciadas de origem

Quando a movimentação for concluída, remova os recursos na região de origem para evitar encargos desnecessários.

1. Exclua o grupo de failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Isso removerá a configuração do grupo de failover e encerrará os links de replicação geográfica entre as duas instâncias.
1. Exclua a instância gerenciada de origem usando [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance).
1. Remova todos os recursos adicionais no grupo de recursos, como o cluster virtual, a rede virtual e o grupo de segurança.

## <a name="next-steps"></a>Próximas etapas

[Gerencie](manage-data-after-migrating-to-database.md) seu banco de dados depois que ele tiver sido migrado.
