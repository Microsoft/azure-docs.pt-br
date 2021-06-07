---
title: Atualizar para a última geração de pool de SQL dedicado (antigo SQL DW)
description: Atualize o pool de SQL dedicado do Azure Synapse Analytics (anteriormente conhecido como SQL DW) para a última geração de arquitetura de armazenamento e hardware do Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b5a9d1781bd0498ac6ad74439b1572c52e3c345a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459259"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Otimizar o desempenho atualizando o pool dedicado do SQL (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

Atualize seu pool SQL dedicado (anteriormente conhecido como SQL DW) para a última geração de arquitetura de armazenamento e de hardware do Azure.

## <a name="why-upgrade"></a>Por que atualizar?

Agora você pode atualizar diretamente para a camada de Gen2 otimizada de computação do pool do SQL dedicado (anteriormente conhecido como SQL DW) no portal do Azure para [regiões com suporte](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Se a região não der suporte à atualização automática, você poderá atualizar para uma região com suporte ou aguardar que a atualização automática esteja disponível em sua região. Atualize agora para aproveitar a última geração de hardware do Azure e arquitetura de armazenamento aprimorada, incluindo um desempenho mais rápido, escalabilidade mais alta e armazenamento em colunas ilimitado.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Essa atualização se aplica aos pools de SQL dedicados da camada Gen1 otimizada (fornmerly SQL DW) em [regiões com suporte](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Antes de começar

1. Verifique se a [região](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) tem suporte para GEN1 para GEN2 para migração. Observe as datas de migração automática. Para evitar conflitos com o processo automatizado, planeje a migração manual antes da data de início do processo automatizado.
2. Se você estiver em uma região que ainda não tenha suporte, continue a verificar sua região a ser adicionada ou [atualize usando restauração](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) para uma região com suporte.
3. Se a região for compatível, [atualize por meio do portal do Azure](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selecione o nível de desempenho sugerido para o** pool do SQL dedicado (antigo SQL DW) com base no nível de desempenho atual na camada de Gen1 otimizada de computação usando o mapeamento abaixo:

   | Camada de Computação Otimizada Gen1 | Camada de Computação Otimizada Gen2 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Níveis de desempenho sugeridos não são uma conversão direta. Por exemplo, é recomendável ele irem de DW600 a DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Atualizar em uma região com suporte usando o portal do Azure

- A migração de Gen1 para Gen2 por meio da portal do Azure é permanente. Não há um processo para retornar ao Gen1.
- O pool dedicado do SQL (anteriormente conhecido como SQL DW) deve estar em execução para migrar para o Gen2

### <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Entre no [portal do Azure](https://portal.azure.com/).
- Verifique se o pool do SQL dedicado (anteriormente conhecido como SQL DW) está em execução-deve ser migrar para o Gen2

### <a name="powershell-upgrade-commands"></a>Comandos de atualização do PowerShell

1. Se o pool de SQL dedicado da camada Gen1 otimizada (anteriormente SQL DW) a ser atualizado estiver em pausa, [retome o pool de SQL dedicado (anteriormente conhecido como SQL DW)](pause-and-resume-compute-portal.md).

2. Esteja preparado para alguns minutos de tempo de inatividade.

3. Identifique quaisquer referências de código para os níveis de desempenho de Computação Otimizada Gen1 e modifique-os para o nível de desempenho de Computação Otimizada Gen2. Abaixo, são apresentados dois exemplos para atualizar as referências de código antes da atualização:

   Comando do PowerShell Gen1 original:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Modificado para:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" alterado para - RequestedServiceObjectiveName "DW300 **c**"
   >

   Comando T-SQL Gen1 original:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Modificado para:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = 'DW300' alterador para SERVICE_OBJECTIVE = 'DW300 **c**'

## <a name="start-the-upgrade"></a>Inicie a atualização

1. Vá para seu pool de SQL dedicado Gen1 com otimização de computação (antigo SQL DW) no portal do Azure. Se o pool de SQL dedicado da camada Gen1 otimizada (anteriormente SQL DW) a ser atualizado estiver em pausa, [retome o pool de SQL dedicado](pause-and-resume-compute-portal.md).
2. Selecione **atualizar para** o cartão Gen2 na guia tarefas: ![ Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Se você não vir o cartão **Fazer upgrade para Gen2** na guia Tarefas, seu tipo de assinatura será limitado na região atual.
   > [Envie um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md) para que sua assinatura seja aprovada.

3. Certifique-se de que sua carga de trabalho teve sua execução concluída e foi confirmada antes de atualizar. Você experimentará tempo de inatividade por alguns minutos antes que seu pool SQL dedicado (anteriormente conhecido como SQL DW) esteja novamente online como um pool de SQL dedicado de camada Gen2 otimizado de computação (anteriormente conhecido como SQL DW). **Selecione Atualizar**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Monitore a atualização** verificando o status no Portal do Azure:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   A primeira etapa do processo de atualização passa pela operação de expansão ("Atualização - Offline") em que todas as sessões serão eliminadas e as conexões serão removidas.

   A segunda etapa do processo de atualização é a migração de dados ("Atualização - Online"). A migração de dados é um processo lento online em segundo plano. Esse processo lentamente move dados de colunas da arquitetura de armazenamento antiga para a nova arquitetura de armazenamento usando um cache SSD local. Durante esse tempo, seu pool SQL dedicado (anteriormente conhecido como SQL DW) estará online para consulta e carregamento. Seus dados estarão disponíveis para consulta, independentemente de se forem migrados ou não. A migração de dados ocorre a uma taxa que varia dependendo do tamanho dos seus dados, do nível de desempenho e do número de segmentos do columnstore.

5. **Recomendação opcional:** Quando a operação de dimensionamento for concluída, você poderá acelerar o processo em segundo plano de migração de dados. Você pode forçar a movimentação de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore principais que você estaria consultando em uma classe maior de SLO e recursos. Essa operação é **offline** em comparação ao processo em segundo plano lento, que pode levar horas para ser concluído, dependendo do número e dos tamanhos de suas tabelas. No entanto, uma vez concluída, a migração de dados será muito mais rápida devido à nova arquitetura de armazenamento aprimorada com rowgroups de alta qualidade.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

A consulta a seguir gera os comandos necessários Alter Index Rebuild para acelerar a migração de dados:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Atualize de uma região geográfica do Azure usando a restauração por meio do portal do Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Criar um ponto de restauração definido pelo usuário usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até o pool dedicado do SQL (antigo SQL DW) para o qual você deseja criar um ponto de restauração.

3. Na parte superior da seção Visão Geral, selecione **+Novo Ponto de Restauração**.

    ![Novo Ponto de Restauração](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Especifique um nome para seu ponto de restauração.

    ![Nome do ponto de restauração](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Restaurar um banco de dados ativo ou pausado usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até o pool dedicado do SQL (antigo SQL DW) do qual você deseja restaurar.
3. Na parte superior da seção Visão Geral, selecione **Restaurar**.

    ![ Visão Geral de Restauração](./media/upgrade-to-latest-generation/restoring_0.png)

4. Selecione **pontos de restauração automáticos** ou **pontos de restauração definidos pelo usuário**. Para pontos de restauração definidos pelo usuário, **Selecione um ponto de restauração definido pelo usuário** ou **crie um novo ponto de restauração definido pelo usuário**. Para o servidor, selecione **criar novo** e escolha um servidor em uma região geográfica com suporte do Gen2.

    ![Pontos de restauração automática](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Restaurar um banco de dados excluído usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para recuperar um banco de dados, use o cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um ServiceObjectiveName de Gen2 (por exemplo, DW1000 **c**) como parâmetro opcional.

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados, especificando um ServiceObjectiveName Gen2.
6. Verifique o status do banco de dados com restauração geográfica.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar o banco de dados após a conclusão da restauração, consulte [Configurar o banco de dados após a recuperação](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.

Se você tiver problemas com seu pool SQL dedicado, crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "atualização Gen2" como a possível causa.

## <a name="next-steps"></a>Próximas etapas

Seu pool SQL dedicado atualizado (anteriormente conhecido como SQL DW) está online. Para aproveitar a arquitetura avançada, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
