---
title: 'Tutorial: Adicionar um banco de dados SQL do Azure para um grupo de failover | Microsoft Docs'
description: Adicione um banco de dados SQL do banco de dados do Azure a um grupo de failover usando o portal do Azure, o PowerShell ou o CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
manager: jroth
ms.date: 06/19/2019
ms.openlocfilehash: 6125fd087d40f9ac6ae24d9200fc0cd1e7843f82
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444975"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Tutorial: Adicionar um banco de dados SQL do Azure para um grupo de failover

Configure um grupo de failover para um banco de dados SQL do Azure e failover de teste usando o portal do Azure, o PowerShell ou o CLI do Azure.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> - Crie um banco de dados individual do banco de dados SQL do Azure.
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para um único banco de dados entre dois SQL Servers lógicos.
> - Failover de teste.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)
Para concluir este tutorial, verifique se você tem: 

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Para concluir o tutorial, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- [PowerShell do Azure](/powershell/azureps-cmdlets-docs)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Para concluir o tutorial, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- A versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1-criar um banco de dados individual 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-criar o grupo de failover 
Nesta etapa, você criará um [grupo de failover](sql-database-auto-failover-group.md) entre um SQL Server do Azure existente e um novo SQL Server do Azure em outra região. Em seguida, adicione o banco de dados ao grupo de failover. 

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)
Crie seu grupo de failover e adicione seu banco de dados a ele usando o portal do Azure. 


1. Selecione **todos os serviços** no canto superior esquerdo do [portal do Azure](https://portal.azure.com). 
1. Digite `sql servers` na caixa de pesquisa. 
1. Adicional Selecione o ícone de estrela ao lado de servidores SQL para os **SQL Servers** favoritos e adicione-o ao painel de navegação à esquerda. 
    
    ![Localizar servidores SQL](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Selecione **SQL** Servers e escolha o servidor que você criou na seção 1, `mysqlserver`como.
1. Selecione **grupos de failover** no painel **configurações** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os seguintes valores e, em seguida, selecione **criar**:
    - **Nome do grupo de failover**: Digite um nome de grupo de failover exclusivo, `failovergrouptutorial`como. 
    - **Servidor secundário**: Selecione a opção para *definir as configurações necessárias* e, em seguida, escolha **criar um novo servidor**. Como alternativa, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os valores a seguir, selecione **selecionar**. 
        - **Nome do servidor**: Digite um nome exclusivo para o servidor secundário, `mysqlsecondary`como. 
        - **Logon de administrador do servidor**: Escreva`azureuser`
        - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
        - **Localização**: Escolha um local na lista suspensa, como leste dos EUA 2. Esse local não pode ser o mesmo local que o servidor primário.

    > [!NOTE]
    > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário. 
    
      ![Criar um servidor secundário para o grupo de failover](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **Bancos de dados dentro do grupo**: Depois que um servidor secundário é selecionado, essa opção fica desbloqueada. Selecione-o para **selecionar os bancos de dados a serem adicionados** e escolha o Database criado na seção 1. Adicionar o banco de dados ao grupo de failover iniciará automaticamente o processo de replicação geográfica. 
        
    ![Adicionar Banco de BD SQL ao grupo de failover](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Crie seu grupo de failover e adicione seu banco de dados individual a ele usando o PowerShell. 

   > [!NOTE]
   > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   
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

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Crie seu grupo de failover e adicione seu banco de dados individual a ele usando AZ CLI. 

   > [!NOTE]
   > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3-failover de teste 
Nesta etapa, você falhará no grupo de failover para o servidor secundário e, em seguida, fará o failback usando o portal do Azure. 

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)
Failover de teste usando o portal do Azure. 

1. Navegue até o servidor de **servidores SQL** dentro do [portal do Azure](https://portal.azure.com). 
1. Selecione **grupos de failover** no painel **configurações** e escolha o grupo de failover que você criou na seção 2. 
  
   ![Selecione o grupo de failover no portal](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual servidor é secundário. 
1. Selecione **failover** no painel de tarefas para fazer failover do grupo de failover que contém o banco de dados individual de exemplo. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Fazer failover do grupo de failover que contém o banco de dados SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Examine qual servidor agora é primário e qual servidor é secundário. Se o failover for bem-sucedido, os dois servidores deverão ter funções trocadas. 
1. Selecione **failover** novamente para reprovar os servidores de volta para suas funções originalmente. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Failover de teste usando o PowerShell. 


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


Fazer failover para o servidor secundário: 

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
   Write-host "Failed failover group to sucessfully to" $drServerName 
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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Teste o failover usando o AZ CLI. 


Verifique qual servidor é o secundário:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Fazer failover para o servidor secundário: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Reverta o grupo de failover de volta para o servidor primário:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>Limpar recursos 
Limpe os recursos excluindo o grupo de recursos. 

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)
Exclua o grupo de recursos usando o portal do Azure. 


1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com).
1. Selecione **excluir grupo de recursos** para excluir todos os recursos no grupo, bem como o próprio grupo de recursos. 
1. Digite o nome do grupo de recursos, `myResourceGroup`, na caixa de texto e, em seguida, selecione **excluir** para excluir o grupo de recursos.  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Exclua o grupo de recursos usando o PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Exclua o grupo de recursos usando AZ CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>Scripts completos

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)
Não há nenhum script disponível para o portal do Azure.
 
---

Você pode encontrar outros scripts do banco de dados SQL do Azure aqui: [Azure PowerShell](sql-database-powershell-samples.md) e [CLI do Azure](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um banco de dados do banco de dados SQL do Azure a um grupo de failover e testou o failover. Você aprendeu como:

> [!div class="checklist"]
> - Crie um banco de dados individual do banco de dados SQL do Azure. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para um único banco de dados entre dois SQL Servers lógicos.
> - Failover de teste.

Avance para o próximo tutorial sobre como adicionar seu pool elástico a um grupo de failover. 

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um pool elástico do banco de dados SQL do Azure a um grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md)
