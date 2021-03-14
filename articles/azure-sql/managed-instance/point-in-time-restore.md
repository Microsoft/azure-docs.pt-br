---
title: Restauração pontual (PITR)
titleSuffix: Azure SQL Managed Instance
description: Restaurar um banco de dados no Azure SQL Instância Gerenciada para um ponto anterior no tempo.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 0a56cfc147d4fb5cbdccf13363ad28bc602d8216
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182750"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Restaurar um banco de dados no Azure SQL Instância Gerenciada para um ponto anterior no tempo
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Use a restauração pontual (PITR) para criar um banco de dados como uma cópia de outro banco de dados de algum tempo no passado. Este artigo descreve como fazer uma restauração pontual de um banco de dados no Azure SQL Instância Gerenciada.

A restauração pontual é útil em cenários de recuperação, como incidentes causados por erros, dados carregados incorretamente ou exclusão de dados cruciais. Você também pode usá-lo simplesmente para teste ou auditoria. Os arquivos de backup são mantidos por 7 a 35 dias, dependendo das configurações do banco de dados.

A restauração pontual pode restaurar um banco de dados:

- de um banco de dados existente.
- de um banco de dados excluído.
- para o mesmo SQL Instância Gerenciada ou para outro Instância Gerenciada do SQL. 

## <a name="limitations"></a>Limitações

A restauração pontual para o SQL Instância Gerenciada tem as seguintes limitações:

- Quando você estiver restaurando de uma instância do SQL Instância Gerenciada para outra, ambas as instâncias deverão estar na mesma assinatura e região. A restauração entre regiões e entre assinaturas não tem suporte no momento.
- A restauração pontual de um SQL Instância Gerenciada inteiro não é possível. Este artigo explica apenas o que é possível: restauração pontual de um banco de dados hospedado no SQL Instância Gerenciada.

> [!WARNING]
> Esteja ciente do tamanho do armazenamento do seu Instância Gerenciada SQL. Dependendo do tamanho dos dados a serem restaurados, você poderá ficar sem armazenamento de instância. Se não houver espaço suficiente para os dados restaurados, use uma abordagem diferente.

A tabela a seguir mostra cenários de restauração pontual para o SQL Instância Gerenciada:

|           |Restaurar o banco de BD existente para a mesma instância do SQL Instância Gerenciada| Restaurar o BD existente para outro SQL Instância Gerenciada|Restaurar o banco de BD removido para o mesmo SQL Instância Gerenciada|Restaurar o BD removido para outro Instância Gerenciada do SQL|
|:----------|:----------|:----------|:----------|:----------|
|**Azure portal**| Sim|Não |Sim|Não|
|**CLI do Azure**|Sim |Sim |Não|Não|
|**PowerShell**| Sim|Sim |Sim|Sim|

## <a name="restore-an-existing-database"></a>Restaurar um banco de dados existente

Restaure um banco de dados existente para o mesmo SQL Instância Gerenciada usando o portal do Azure, o PowerShell ou o CLI do Azure. Para restaurar um banco de dados para outro Instância Gerenciada SQL, use o PowerShell ou o CLI do Azure para que você possa especificar as propriedades para o Instância Gerenciada de destino SQL e o grupo de recursos. Se você não especificar esses parâmetros, o banco de dados será restaurado para o SQL Instância Gerenciada existente por padrão. Atualmente, o portal do Azure não dá suporte à restauração para outro Instância Gerenciada SQL.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Acesse o Instância Gerenciada do SQL e selecione o banco de dados que deseja restaurar.
3. Selecione **restaurar** na página do banco de dados:

    ![Restaurar um banco de dados usando o portal do Azure](./media/point-in-time-restore/restore-database-to-mi.png)

4. Na página **restaurar** , selecione o ponto para a data e a hora em que você deseja restaurar o banco de dados.
5. Selecione **confirmar** para restaurar o banco de dados. Essa ação inicia o processo de restauração, que cria um novo banco de dados e popula-o com o banco de dados original no momento especificado. Para obter mais informações sobre o processo de recuperação, consulte [tempo de recuperação](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se você ainda não tiver Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Para restaurar o banco de dados usando o PowerShell, especifique os valores para os parâmetros no comando a seguir. Em seguida, execute o comando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Para restaurar o banco de dados para outro Instância Gerenciada do SQL, especifique também os nomes do grupo de recursos de destino e o SQL Instância Gerenciada de destino:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Para obter detalhes, consulte [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você ainda não tiver o CLI do Azure instalado, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

Para restaurar o banco de dados usando o CLI do Azure, especifique os valores para os parâmetros no comando a seguir. Em seguida, execute o comando:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Para restaurar o banco de dados para outro Instância Gerenciada do SQL, especifique também os nomes do grupo de recursos de destino e do SQL Instância Gerenciada:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Para obter uma explicação detalhada dos parâmetros disponíveis, consulte a [documentação da CLI para restaurar um banco de dados em um instância gerenciada do SQL](/cli/azure/sql/midb#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído

A restauração de um banco de dados excluído pode ser feita usando o PowerShell ou portal do Azure. Para restaurar um banco de dados excluído para a mesma instância, use o portal do Azure ou o PowerShell. Para restaurar um banco de dados excluído para outra instância, use o PowerShell. 

### <a name="portal"></a>Portal 


Para recuperar um banco de dados gerenciado usando o portal do Azure, abra a página Visão geral do SQL Instância Gerenciada e selecione bancos de dados **excluídos**. Escolha um banco de dados excluído que você deseja restaurar e digite o nome do novo banco de dados que será criado com a data de restauração do backup.

  ![Captura de tela da restauração do banco de dados de instância do SQL do Azure excluído](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Para restaurar um banco de dados para a mesma instância, atualize os valores de parâmetro e, em seguida, execute o seguinte comando do PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Para restaurar o banco de dados para outro Instância Gerenciada do SQL, especifique também os nomes do grupo de recursos de destino e o SQL Instância Gerenciada de destino:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Substituir um banco de dados existente

Para substituir um banco de dados existente, você deve:

1. Remova o banco de dados existente que você deseja substituir.
2. Renomeie o banco de dados restaurado point-in-time para o nome do banco de dados que você removeu.

### <a name="drop-the-original-database"></a>Remover o banco de dados original

Você pode remover o banco de dados usando o portal do Azure, o PowerShell ou o CLI do Azure.

Você também pode remover o banco de dados conectando-se ao SQL Instância Gerenciada diretamente, iniciando o SQL Server Management Studio (SSMS) e, em seguida, executando o seguinte comando Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Use um dos seguintes métodos para se conectar ao banco de dados no SQL Instância Gerenciada:

- [SSMS/Azure Data Studio por meio de uma máquina virtual do Azure](./connect-vm-instance-configure.md)
- [Ponto a site](./point-to-site-p2s-configure.md)
- [Ponto de extremidade público](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, selecione o banco de dados do Instância Gerenciada SQL e, em seguida, selecione **excluir**.

   ![Excluir um banco de dados usando o portal do Azure](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use o seguinte comando do PowerShell para remover um banco de dados existente de um Instância Gerenciada SQL:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o seguinte comando CLI do Azure para remover um banco de dados existente de um Instância Gerenciada SQL:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Alterar o novo nome do banco de dados para corresponder ao nome original do banco de dados

Conecte-se diretamente ao Instância Gerenciada do SQL e inicie o SQL Server Management Studio. Em seguida, execute a seguinte consulta Transact-SQL (T-SQL). A consulta irá alterar o nome do banco de dados restaurado para o do banco de dados removido que você pretende substituir.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Use um dos seguintes métodos para se conectar ao banco de dados no SQL Instância Gerenciada:

- [Máquina virtual do Azure](./connect-vm-instance-configure.md)
- [Ponto a site](./point-to-site-p2s-configure.md)
- [Ponto de extremidade público](./public-endpoint-configure.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [backups automatizados](../database/automated-backups-overview.md).
