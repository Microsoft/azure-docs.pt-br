---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: dd511375c6b007222185f25610aecbd9931a742b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640062"
---
Nesta etapa, você criará o grupo de recursos e um banco de dados individual do Banco de Dados SQL do Azure.

> [!IMPORTANT]
> Não se esqueça de configurar regras de firewall para usar o endereço IP público do computador em que você está executando as etapas neste artigo. 
>
> Para obter informações, confira [Criar uma regra de firewall de nível de banco de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou, para determinar o endereço IP usado para a regra de firewall de nível de servidor para seu computador, confira [Criar um firewall de nível de servidor](../sql-database-server-level-firewall-rule.md).  

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)

Crie o grupo de recursos e o banco de dados individual usando o portal do Azure.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Bancos de Dados** e, em seguida, **Banco de Dados SQL** para abrir a página **Criar Banco de Dados SQL**.

   ![Criar banco de dados individual](../media/sql-database-get-started-portal/create-database-1.png)

3. Na guia **Informações Básicas** da seção **Detalhes do Projeto**, digite ou selecione os seguintes valores:

   - **Assinatura**: Selecione a lista suspensa e escolha a assinatura correta, caso ela não seja exibida.
   - **Grupo de recursos**: Selecione **Criar novo**, digite `myResourceGroup` e selecione **OK**.

     ![Novo banco de dados SQL – guia Informações Básicas](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Na seção **Detalhes do Banco de Dados**, digite ou selecione os seguintes valores:

   - **Nome do banco de dados**: Digite `mySampleDatabase`.
   - **Servidor**: Selecione **Criar**, insira os valores a seguir e, em seguida, escolha **Selecionar**.
       - **Nome do servidor**: Digite `mysqlserver`, juntamente com alguns números para exclusividade.
       - **Logon de administrador do servidor**: Digite `azureuser`.
       - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
       - **Localização**: Escolha uma localização na lista suspensa, como `West US 2`.

         ![Novo servidor](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Lembre-se de registrar o logon de administrador do servidor e a senha para fazer logon no servidor e nos bancos de dados deste e de outros inícios rápidos. Caso esqueça seu logon ou a senha, obtenha o nome de logon ou redefina a senha na página **SQL Server**. Para abrir a página **SQL Server**, selecione o nome do servidor na página **Visão Geral** do banco de dados após a criação do banco de dados.

   - **Deseja usar o pool elástico SQL?** : Selecione a opção **Não**.
   - **Computação + armazenamento**: Selecione **Configurar o banco de dados**. 

     ![Detalhes do Banco de Dados SQL](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Selecione **Provisionado** e **Gen5**.

     ![Gen4 provisionado](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Examine as configurações para **Máximo de vCores**, **Mínimo de vCores**, **Atraso de pausa automática** e **Tamanho máximo de dados**. Altere-as conforme desejado.
   - Aceite os termos de versão prévia e clique em **OK**.
   - Escolha **Aplicar**.

5. Selecione a guia **Configurações adicionais**. 
6. Na seção **Fonte de dados**, em **Usar dados existentes**, selecione `Sample`.

   ![Configurações adicionais do BD SQL](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Selecione os dados da **Amostra (AdventureWorksLT)** para seguir com facilidade este e outros Guias de Início Rápido do Banco de Dados SQL do Azure que usam esses dados.

7. Mantenha o restante dos valores como padrão e selecione **Examinar + Criar** na parte inferior do formulário.
8. Examine as configurações finais e selecione **Criar**.

9. No formulário **Banco de Dados SQL**, selecione **Criar** para implantar e provisionar o grupo de recursos, o servidor e o banco de dados.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Crie o grupo de recursos e o banco de dados individual usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US 2"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

# <a name="az-clitabbash"></a>[CLI do Azure](#tab/bash)

Crie o grupo de recursos e o banco de dados individual usando a CLI do Azure.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

---
