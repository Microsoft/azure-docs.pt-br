---
title: 'Tutorial: Adicionar um banco de dados a um grupo de failover'
description: Adicione um banco de dados do Banco de Dados SQL do Azure a um grupo de failover automático usando o portal do Azure, o PowerShell ou a CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/19/2019
ms.openlocfilehash: 68fa089713c3dd89b4699011ded7d667bca6f73f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178059"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Tutorial: Adicionar um Banco de Dados SQL do Azure a um grupo de failover automático
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Um [grupo de failover](auto-failover-group-overview.md) é uma camada de abstração declarativa que permite agrupar vários bancos de dados com replicação geográfica. Saiba como configurar um grupo de failover para um Banco de Dados SQL do Azure e testar o failover usando o portal do Azure, o PowerShell ou a CLI do Azure.  Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um banco de dados no Banco de Dados SQL do Azure
> - Criar um grupo de failover para o banco de dados entre dois servidores.
> - Testar o failover.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Para concluir este tutorial, verifique se você tem:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/), caso ainda não tenha uma.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para concluir o tutorial, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/), caso ainda não tenha uma.
- [PowerShell do Azure](/powershell/azure/)

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para concluir o tutorial, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/), caso ainda não tenha uma.
- A última versão da [CLI do Azure](/cli/azure/install-azure-cli).

---

## <a name="1---create-a-database"></a>1 – Criar um banco de dados

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 – Criar o grupo de failover

Nesta etapa, você criará um [grupo de failover](auto-failover-group-overview.md) entre um servidor existente e um novo servidor em outra região. Em seguida, adicione o banco de dados ao grupo de failover.

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Crie o grupo de failover e adicione o banco de dados a ele usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o banco de dados criado na seção 1, como `mySampleDatabase`.
1. Os grupos de failover podem ser configurados no nível do servidor. Selecione o nome do servidor em **Nome do servidor** para abrir as configurações do servidor.

   ![Abrir o servidor para o banco de dados](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecione **Grupos de failover** no painel **Configurações** e escolha **Adicionar grupo** para criar um grupo de failover.

   ![Adicionar um novo grupo de failover](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Na página **Grupo de Failover**, insira ou selecione os seguintes valores e selecione **Criar**:

   - **Nome do grupo de failover**: digite um nome de grupo de failover exclusivo, como `failovergrouptutorial`.
   - **Servidor secundário**: selecione a opção para *definir as configurações necessárias* e escolha **Criar um servidor**. Como alternativa, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os valores a seguir, escolha **Selecionar**.
      - **Nome do servidor**: digite um nome exclusivo para o servidor secundário, como `mysqlsecondary`.
      - **Logon de administrador do servidor**: Digite `azureuser`
      - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
      - **Localização**: Escolha uma localização na lista suspensa, como `East US`. Essa localização não pode ser a mesma do servidor primário.

     > [!NOTE]
     > As configurações de logon e firewall do servidor precisam corresponder àquelas do servidor primário.

     ![Criar um servidor secundário para o grupo de failover](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Bancos de dados dentro do grupo**: depois que um servidor secundário é selecionado, essa opção fica desbloqueada. Escolha-a para **Selecionar os bancos de dados a serem adicionados** e selecione o banco de dados criado na seção 1. A adição do banco de dados ao grupo de failover iniciará automaticamente o processo de replicação geográfica.

   ![Adicionar o Banco de Dados SQL ao grupo de failover](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o grupo de failover e adicione o banco de dados a ele usando o PowerShell.

   > [!NOTE]
   > As configurações de logon e firewall do servidor precisam corresponder àquelas do servidor primário.

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor no Banco de Dados SQL do Azure que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor no Banco de Dados SQL do Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um banco de dados individual no Banco de Dados SQL do Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover no Banco de Dados SQL do Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados do Banco de Dados SQL do Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados a um grupo de failover do Banco de Dados SQL do Azure. |

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Crie o grupo de failover e adicione o banco de dados a ele usando a CLI do Azure.

   > [!NOTE]
   > As configurações de logon e firewall do servidor precisam corresponder àquelas do servidor primário.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Esta parte do tutorial usa os seguintes cmdlets da CLI do Azure:

| Comando | Observações |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor que hospeda bancos de dados e pools elásticos. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Cria um grupo de failover. |

---

## <a name="3---test-failover"></a>3 – Testar o failover

Nesta etapa, você fará failover do grupo de failover para o servidor secundário e fará failback usando o portal do Azure.

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Teste o failover usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o banco de dados criado na seção 2, como `mySampleDatbase`.
1. Selecione o nome do servidor em **Nome do servidor** para abrir as configurações do servidor.

   ![Abrir o servidor para o banco de dados](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecione **Grupos de failover** no painel **Configurações** e escolha o grupo de failover criado na seção 2.
  
   ![Selecionar o grupo de failover no portal](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual é secundário.
1. Selecione **Failover** no painel de tarefas para fazer failover do grupo de failover que contém o banco de dados de exemplo.
1. Selecione **Sim** no aviso que notifica você de que as sessões do TDS serão desconectadas.

   ![Fazer failover do grupo de failover que contém o banco de dados](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Examine qual servidor agora é primário e qual é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter trocado as funções.
1. Selecione **Failover** novamente para faça failover os servidores às funções originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover usando o PowerShell.

Verifique a função da réplica secundária:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Faça failover para o servidor secundário:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

Reverta o grupo de failover de volta para o servidor primário:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do Banco de Dados SQL do Azure. |

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Teste o failover usando a CLI do Azure.

Confirme qual servidor é o secundário:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Faça failover para o servidor secundário:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Reverta o grupo de failover de volta para o servidor primário:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Esta parte do tutorial usa os seguintes cmdlets da CLI do Azure:

| Comando | Observações |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Lista grupos de failover em um servidor. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Define o primário do grupo de failover fazendo failover de todos os bancos de dados do servidor primário atual. |

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos excluindo o grupo de recursos.

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Exclua o grupo de recursos usando o portal do Azure.

1. Procure o grupo de recursos no [portal do Azure](https://portal.azure.com).
1. Selecione **Excluir grupo de recursos** para excluir todos os recursos do grupo, bem como o próprio grupo de recursos.
1. Digite o nome do grupo de recursos, `myResourceGroup`, na caixa de texto e selecione **Excluir** para excluir o grupo de recursos.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Exclua o grupo de recursos usando o PowerShell.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos |

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Exclua o grupo de recursos usando a CLI do Azure.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Esta parte do tutorial usa os seguintes cmdlets da CLI do Azure:

| Comando | Observações |
|---|---|
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

---

> [!IMPORTANT]
> Caso deseje manter o grupo de recursos, mas excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes da remoção dele do grupo de failover pode causar um comportamento imprevisível.

## <a name="full-scripts"></a>Scripts completos

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que hospeda bancos de dados individuais e pools elásticos no Banco de Dados SQL do Azure. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor no Banco de Dados SQL do Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um banco de dados no Banco de Dados SQL do Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover no Banco de Dados SQL do Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados do Banco de Dados SQL do Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados a um grupo de failover do Banco de Dados SQL do Azure. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover no Banco de Dados SQL do Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos do Banco de Dados SQL do Azure.|

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az account set](/cli/azure/account#az-account-set) | Define uma assinatura como a assinatura ativa atual. |
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor que hospeda bancos de dados individuais e pools elásticos no Banco de Dados SQL do Azure. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de IP no nível do servidor no Banco de Dados SQL do Azure. |
| [az sql db create](/cli/azure/sql/db) | Cria um banco de dados no Banco de Dados SQL do Azure. |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Cria um grupo de failover no Banco de Dados SQL do Azure. |
| [az sql failover-group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Lista os grupos de failover de um servidor no Banco de Dados SQL do Azure. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Define o primário do grupo de failover fazendo failover de todos os bancos de dados do servidor primário atual. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Não há nenhum script disponível para o portal do Azure.

---

Encontre outros scripts do Banco de Dados SQL do Azure aqui: [Azure PowerShell](powershell-script-content-guide.md) e [CLI do Azure](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um banco de dados no Banco de Dados SQL do Azure a um grupo de failover e testou o failover. Você aprendeu a:

> [!div class="checklist"]
>
> - Criar um banco de dados no Banco de Dados SQL do Azure
> - Criar um grupo de failover para o banco de dados entre dois servidores.
> - Testar o failover.

Avance para o próximo tutorial sobre como adicionar o pool elástico a um grupo de failover.

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um pool elástico do Banco de Dados SQL do Azure a um grupo de failover](failover-group-add-elastic-pool-tutorial.md)