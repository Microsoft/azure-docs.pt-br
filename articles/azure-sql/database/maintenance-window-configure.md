---
title: Configurar a janela de manutenção (visualização)
description: Saiba como definir a hora em que a manutenção planejada deve ser executada em seus bancos de dados SQL do Azure, pools elásticos e bancos de dados de instância gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/04/2021
ms.openlocfilehash: 210f0c52a2b27492bfa2181473043df3537157d2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183192"
---
# <a name="configure-maintenance-window-preview"></a>Configurar a janela de manutenção (visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Configure a [janela de manutenção (versão prévia)](maintenance-window.md) para um banco de dados SQL do Azure, um pool elástico ou um banco de dados SQL instância gerenciada do Azure durante a criação do recurso ou a qualquer momento após a criação de um recurso. 

A janela de manutenção *padrão do sistema* é de 17:00 a 8:00 diariamente (hora local da região do Azure em que o recurso está localizado) para evitar interrupções de pico no horário comercial. Se a janela de manutenção *padrão do sistema* não for a melhor hora, selecione uma das outras janelas de manutenção disponíveis.

A capacidade de mudar para uma janela de manutenção diferente não está disponível para cada nível de serviço ou em cada região. Para obter detalhes sobre a disponibilidade, consulte [disponibilidade da janela de manutenção](maintenance-window.md#availability).

> [!Important]
> A configuração da janela de manutenção é uma operação assíncrona de execução longa, semelhante à alteração da camada de serviço do recurso SQL do Azure. O recurso está disponível durante a operação, exceto um failover curto que ocorre no final da operação e normalmente dura até 8 segundos, mesmo no caso de transações de longa execução interrompidas. Para minimizar o impacto do failover, você deve executar a operação fora do horário de pico.

## <a name="configure-maintenance-window-during-database-creation"></a>Configurar a janela de manutenção durante a criação do banco de dados 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para configurar a janela de manutenção quando você cria um banco de dados, um pool elástico ou uma instância gerenciada, defina a **janela de manutenção** desejada na página **configurações adicionais** . 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Definir a janela de manutenção ao criar um único banco de dados ou pool elástico

Para obter informações passo a passo sobre como criar um novo banco de dados ou pool, consulte criar um banco de dados [SQL do Azure](single-database-create-quickstart.md)banco de dados individual.

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Guia Criar configurações adicionais do banco de dados":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Definir a janela de manutenção ao criar uma instância gerenciada

Para obter informações passo a passo sobre como criar uma nova instância gerenciada, consulte [criar um instância gerenciada de dados SQL do Azure](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Guia Criar configurações adicionais da instância gerenciada":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os exemplos a seguir mostram como configurar a janela de manutenção usando Azure PowerShell. Você pode [instalar Azure PowerShell](/powershell/azure/install-az-ps)ou usar o Azure cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.


## <a name="discover-available-maintenance-windows"></a>Descobrir as janelas de manutenção disponíveis

Ao definir a janela de manutenção, cada região tem suas próprias opções de janela de manutenção que correspondem ao fuso horário da região em que o banco de dados ou o pool está localizado. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Descobrir o banco de dados SQL e as janelas de manutenção do pool elástico 

O exemplo a seguir retorna as janelas de manutenção disponíveis para a região *eastus2* usando o cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Para bancos de dados e pools elásticos, defina `MaintenanceScope` como `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Descobrir janelas de manutenção do SQL Instância Gerenciada 

O exemplo a seguir retorna as janelas de manutenção disponíveis para a região *eastus2* usando o cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Para instâncias gerenciadas, defina `MaintenanceScope` como `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Definir a janela de manutenção ao criar um banco de dados individual

O exemplo a seguir cria um novo banco de dados e define a janela de manutenção usando o cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) . O `-MaintenanceConfigurationId` deve ser definido como um valor válido para a região do seu banco de dados. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Definir a janela de manutenção ao criar um pool elástico

O exemplo a seguir cria um novo pool elástico e define a janela de manutenção usando o cmdlet [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) . A janela de manutenção é definida no pool elástico, de modo que todos os bancos de dados no pool têm a agenda da janela de manutenção do pool. O `-MaintenanceConfigurationId` deve ser definido como um valor válido para a região do pool. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Definir a janela de manutenção ao criar uma instância gerenciada

O exemplo a seguir cria uma nova instância gerenciada e define a janela de manutenção usando o cmdlet [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) . A janela de manutenção é definida na instância, de modo que todos os bancos de dados na instância têm a agenda da janela de manutenção da instância. Para `-MaintenanceConfigurationId` , o *MaintenanceConfigName* deve ser um valor válido para a região da sua instância. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Os exemplos a seguir mostram como configurar a janela de manutenção usando CLI do Azure. Você pode [instalar o CLI do Azure](/cli/azure/install-azure-cli)ou usar o Azure cloud Shell. 

A configuração da janela de manutenção com o CLI do Azure só está disponível para SQL Instância Gerenciada.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/cli](https://shell.azure.com/cli). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="discover-available-maintenance-windows"></a>Descobrir as janelas de manutenção disponíveis

Ao definir a janela de manutenção, cada região tem suas próprias opções de janela de manutenção que correspondem ao fuso horário da região em que o banco de dados ou o pool está localizado.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Descobrir o banco de dados SQL e as janelas de manutenção do pool elástico

O exemplo a seguir retorna as janelas de manutenção disponíveis para a região *eastus2* usando o comando [AZ Maintenance Public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . Para bancos de dados e pools elásticos, defina `maintenanceScope` como `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Descobrir janelas de manutenção do SQL Instância Gerenciada

O exemplo a seguir retorna as janelas de manutenção disponíveis para a região *eastus2* usando o comando [AZ Maintenance Public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . Para instâncias gerenciadas, defina `maintenanceScope` como `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Definir a janela de manutenção ao criar um banco de dados individual

O exemplo a seguir cria um novo banco de dados e define a janela de manutenção usando o comando [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) . O `--maint-config-id` (ou `-m` ) deve ser definido como um valor válido para a região do banco de dados. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Definir a janela de manutenção ao criar um pool elástico

O exemplo a seguir cria um novo pool elástico e define a janela de manutenção usando o cmdlet [AZ SQL elástico-pool Create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) . A janela de manutenção é definida no pool elástico, de modo que todos os bancos de dados no pool têm a agenda da janela de manutenção do pool. O `--maint-config-id` (ou `-m` ) deve ser definido como um valor válido para a região do pool. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Definir a janela de manutenção ao criar uma instância gerenciada

O exemplo a seguir cria uma nova instância gerenciada e define a janela de manutenção usando [AZ SQL mi Create](/cli/azure/sql/mi#az_sql_mi_create). A janela de manutenção é definida na instância, de modo que todos os bancos de dados na instância têm a agenda da janela de manutenção da instância. *MaintenanceConfigName* deve ser um valor válido para a região da sua instância. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Configurar a janela de manutenção para bancos de dados existentes


Ao aplicar uma seleção de janela de manutenção a um banco de dados, um breve failover (vários segundos) pode ser experimentado em alguns casos, pois o Azure aplica as alterações necessárias.

# <a name="portal"></a>[Portal](#tab/azure-portal)

As etapas a seguir definem a janela de manutenção em um banco de dados existente, pool elástico ou instância gerenciada usando o portal do Azure:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Definir a janela de manutenção para um banco de dados ou pool elástico existente

1. Navegue até o banco de dados SQL ou o pool elástico para o qual você deseja definir a janela de manutenção.
1. No menu **configurações** , selecione **manutenção** e, em seguida, selecione a janela de manutenção desejada.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Página de manutenção do banco de dados SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Definir a janela de manutenção para uma instância gerenciada existente

1. Navegue até a instância gerenciada para a qual você deseja definir a janela de manutenção.
1. No menu **configurações** , selecione **manutenção** e, em seguida, selecione a janela de manutenção desejada.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Página de manutenção da instância gerenciada do SQL":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Definir a janela de manutenção para um banco de dados existente

O exemplo a seguir define a janela de manutenção em um banco de dados existente usando o cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . O `-MaintenanceConfigurationId` deve ser definido como um valor válido para a região do seu banco de dados. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Definir a janela de manutenção em um pool elástico existente

O exemplo a seguir define a janela de manutenção em um pool elástico existente usando o cmdlet [set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) . É importante certificar-se de que o `$maintenanceConfig` valor é um valor válido para a região do pool.  Para obter valores válidos para uma região, consulte [descobrir as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Definir a janela de manutenção em uma instância gerenciada existente

O exemplo a seguir define a janela de manutenção em uma instância gerenciada existente usando o cmdlet [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) . É importante certificar-se de que o `$maintenanceConfig` valor deve ser um valor válido para a região da sua instância.  Para obter valores válidos para uma região, consulte [descobrir as janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Os exemplos a seguir mostram como configurar a janela de manutenção usando CLI do Azure. Você pode [instalar o CLI do Azure](/cli/azure/install-azure-cli)ou usar o Azure cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Definir a janela de manutenção para um banco de dados existente

O exemplo a seguir define a janela de manutenção em um banco de dados existente usando o comando [AZ SQL DB Update](/cli/azure/sql/db#az_sql_db_update) . O `--maint-config-id` (ou `-m` ) deve ser definido como um valor válido para a região do banco de dados. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Definir a janela de manutenção em um pool elástico existente

O exemplo a seguir define a janela de manutenção em um pool elástico existente usando o comando [AZ SQL elástico-pool Update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) . É importante certificar-se de que o `maintenanceConfig` valor é um valor válido para a região do pool.  Para obter valores válidos para uma região, consulte [descobrir as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Definir a janela de manutenção em uma instância gerenciada existente

O exemplo a seguir define a janela de manutenção usando [AZ SQL Mi Update](/cli/azure/sql/mi#az_sql_mi_update). A janela de manutenção é definida na instância, de modo que todos os bancos de dados na instância têm a agenda da janela de manutenção da instância. Para `-MaintenanceConfigurationId` , o *MaintenanceConfigName* deve ser um valor válido para a região da sua instância. Para obter valores válidos para sua região, consulte [descobrir janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Recursos de limpeza

Certifique-se de excluir os recursos desnecessários depois de terminar de usá-los para evitar encargos desnecessários.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue até o banco de dados SQL ou o pool elástico que você não precisa mais.
1. No menu **visão geral** , selecione a opção para excluir o recurso.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a janela de manutenção, consulte [janela de manutenção (versão prévia)](maintenance-window.md).
- Para obter mais informações, consulte [FAQ da janela de manutenção](maintenance-window-faq.yml).
- Para saber mais sobre como otimizar o desempenho, consulte [monitoramento e ajuste de desempenho no banco de dados SQL do Azure e Azure sql instância gerenciada](monitor-tune-overview.md).
