---
title: 'Tutorial: Adicionar um pool elástico a um grupo de failover'
description: Adicione um pool elástico do Banco de Dados SQL do Azure a um grupo de failover usando o portal do Azure, o PowerShell ou a CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: dd0532469cd4390d9a72900dcebc22994239325b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479144"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Tutorial: Adicionar um pool elástico do Banco de Dados SQL do Azure a um grupo de failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Configure um grupo de failover para um pool elástico do Banco de Dados SQL do Azure e um failover de teste usando o portal do Azure.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> - Criar um banco de dados individual.
> - Adicionar o banco de dados a um pool elástico.
> - Criar um [grupo de failover](auto-failover-group-overview.md) para dois pools elásticos entre dois servidores.
> - Testar o failover.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/), caso ainda não tenha uma.

## <a name="1---create-a-single-database"></a>1 – Criar um banco de dados individual

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 – Adicionar o banco de dados a um pool elástico

Nesta etapa, você criará um pool elástico e adicionará o banco de dados a ele.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o pool elástico usando o portal do Azure.

1. Selecione **SQL do Azure** no menu à esquerda do portal do Azure. Se **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e digite "SQL do Azure" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione **+ Adicionar** para abrir a página **Selecionar opção de implantação do SQL**. Você pode exibir informações adicionais sobre os diferentes bancos de dados selecionando Mostrar detalhes no bloco Bancos de dados.
1. Selecione **Pool elástico** na lista suspensa **Tipo de recurso** no bloco **Bancos de Dados SQL**. Selecione **Criar** para criar o pool elástico.

    ![Selecionar o pool elástico](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Configure o pool elástico com os seguintes valores:
   - **Nome**: forneça um nome exclusivo para o pool elástico, como `myElasticPool`.
   - **Assinatura**: Selecione sua assinatura na lista suspensa.
   - **ResourceGroup**: escolha `myResourceGroup` na lista suspensa, o grupo de recursos criado na seção 1.
   - **Servidor**: escolha o servidor criado na seção 1 na lista suspensa.  

       ![Criar um servidor para o pool elástico](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Computação + armazenamento**: escolha **Configurar o pool elástico** para configurar a computação, o armazenamento e adicionar seu banco de dados individual ao pool elástico. Na guia **Configurações do Pool**, mantenha o padrão de Gen5, com 2 vCores e 32 GB.

1. Na página **Configurar**, selecione a guia **Bancos de Dados** e escolha **Adicionar banco de dados**. Escolha o banco de dados criado na seção 1 e selecione **Aplicar** para adicioná-lo ao pool elástico. Selecione **Aplicar** novamente para aplicar as configurações do pool elástico e feche a página **Configurar**.

    ![Adicionar um banco de dados ao pool elástico](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Selecione **Examinar + criar** para examinar as configurações do pool elástico e selecione **Criar** para criar o pool elástico.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie os pools elásticos e o servidor secundário usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um Banco de Dados SQL do Azure.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico. |

---

## <a name="3---create-the-failover-group"></a>3 – Criar o grupo de failover

Nesta etapa, você criará um [grupo de failover](auto-failover-group-overview.md) entre um servidor existente e um novo servidor em outra região. Em seguida, você adicionará o pool elástico ao grupo de failover.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o grupo de failover usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o pool elástico criado na seção anterior, como `myElasticPool`.
1. No painel **Visão Geral**, escolha o nome do servidor em **Nome do servidor** para abrir as configurações do servidor.
  
    ![Abrir o servidor para o pool elástico](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Selecione **Grupos de failover** no painel **Configurações** e escolha **Adicionar grupo** para criar um grupo de failover.

    ![Adicionar um novo grupo de failover](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. Na página **Grupo de Failover**, insira ou selecione os seguintes valores e selecione **Criar**:
    - **Nome do grupo de failover**: digite um nome de grupo de failover exclusivo, como `failovergrouptutorial`.
    - **Servidor secundário**: selecione a opção para *definir as configurações necessárias* e escolha **Criar um servidor**. Como alternativa, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os seguintes valores para o novo servidor secundário, escolha **Selecionar**.
        - **Nome do servidor**: digite um nome exclusivo para o servidor secundário, como `mysqlsecondary`.
        - **Logon de administrador do servidor**: Digite `azureuser`
        - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
        - **Localização**: Escolha uma localização na lista suspensa, como `East US`. Essa localização não pode ser a mesma do servidor primário.

       > [!NOTE]
       > As configurações de logon e firewall do servidor precisam corresponder àquelas do servidor primário.

       ![Criar um servidor secundário para o grupo de failover](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Selecione **Bancos de dados dentro do grupo** e escolha o pool elástico criado na seção 2. Um aviso será exibido, solicitando a criação de um pool elástico no servidor secundário. Selecione o aviso e escolha **OK** para criar o pool elástico no servidor secundário. 

   ![Adicionar o pool elástico ao grupo de failover](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Escolha **Selecionar** para aplicar as configurações de pool elástico ao grupo de failover e selecione **Criar** para criar o grupo de failover. A adição do pool elástico ao grupo de failover iniciará automaticamente o processo de replicação geográfica.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o grupo de failover usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured"

   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
     â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   $failoverGroup
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que hospeda bancos de dados e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool elástico para um Banco de Dados SQL do Azure.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados SQL do Azure a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |

---

## <a name="4---test-failover"></a>4 – Testar o failover

Nesta etapa, você fará failover do grupo de failover para o servidor secundário e fará failback usando o portal do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste o failover do grupo de failover usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o pool elástico criado na seção anterior, como `myElasticPool`.
1. Selecione o nome do servidor em **Nome do servidor** para abrir as configurações do servidor.

    ![Abrir o servidor para o pool elástico](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Selecione **Grupos de failover** no painel **Configurações** e escolha o grupo de failover criado na seção 2.
  
   ![Selecionar o grupo de failover no portal](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual é secundário.
1. Selecione **Failover** no painel de tarefas para fazer failover do grupo de failover que contém o pool elástico.
1. Selecione **Sim** no aviso que notifica você de que as sessões do TDS serão desconectadas.

   ![Fazer failover do grupo de failover que contém o banco de dados](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Examine qual servidor é primário e qual é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter trocado as funções.
1. Selecione **Failover** novamente para fazer failback do grupo de failover para as configurações originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover do grupo de failover usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName
   ```

Faça failover do grupo de failover para o servidor secundário e faça failback dele usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary"
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do Banco de Dados SQL do Azure. |

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos excluindo o grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Procure o grupo de recursos no [portal do Azure](https://portal.azure.com).
1. Selecione **Excluir grupo de recursos** para excluir todos os recursos do grupo, bem como o próprio grupo de recursos.
1. Digite o nome do grupo de recursos, `myResourceGroup`, na caixa de texto e selecione **Excluir** para excluir o grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Limpe os recursos usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Esta parte do tutorial usa o seguinte cmdlet do PowerShell:

| Comando | Observações |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos |

---

> [!IMPORTANT]
> Caso deseje manter o grupo de recursos, mas excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes da remoção dele do grupo de failover pode causar um comportamento imprevisível.

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que hospeda bancos de dados e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um banco de dados. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um Banco de Dados SQL do Azure.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados do Banco de Dados SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados SQL do Azure a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do Banco de Dados SQL do Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Não há nenhum script disponível para o portal do Azure.

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um pool elástico do Banco de Dados SQL do Azure a um grupo de failover e testou o failover. Você aprendeu a:

> [!div class="checklist"]
>
> - Criar um banco de dados individual.
> - Adicionar o banco de dados a um pool elástico.
> - Criar um [grupo de failover](auto-failover-group-overview.md) para dois pools elásticos entre dois servidores.
> - Testar o failover.

Avance para o próximo tutorial sobre como migrar usando DMS.

> [!div class="nextstepaction"]
> [Tutorial: Migrar o SQL Server para um banco de dados em pool usando o DMS](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
