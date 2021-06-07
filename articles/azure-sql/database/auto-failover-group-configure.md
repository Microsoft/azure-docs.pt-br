---
title: Configurar um grupo de failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como configurar um grupo de failover automático para um banco de dados SQL do Azure (único e em pool) e SQL Instância Gerenciada, usando o portal do Azure, o CLI do Azure e o PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: 09bb7cb2344e3e708a64842916e6e483136da3bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94594276"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configurar um grupo de failover para o banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este tópico ensina como configurar um grupo de [failover automático](auto-failover-group-overview.md) para o banco de dados SQL do Azure e o azure SQL instância gerenciada.

## <a name="single-database"></a>Banco de dados individual

Crie o grupo de failover e adicione um banco de dados individual a ele usando o portal do Azure ou o PowerShell.

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As configurações de logon e firewall do servidor para o servidor secundário devem corresponder à do servidor primário.

### <a name="create-failover-group"></a>Criar grupo de failover

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie seu grupo de failover e adicione seu banco de dados a ele usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o banco de dados que você deseja adicionar ao grupo de failover.
1. Selecione o nome do servidor em **Nome do servidor** para abrir as configurações do servidor.

   ![Abrir servidor para banco de BD único](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Selecione **Grupos de failover** no painel **Configurações** e escolha **Adicionar grupo** para criar um grupo de failover.

   ![Adicionar um novo grupo de failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os valores necessários e, em seguida, selecione **criar**.

   - **Bancos de dados dentro do grupo**: escolha o banco que deseja adicionar ao grupo de failover. A adição do banco de dados ao grupo de failover iniciará automaticamente o processo de replicação geográfica.

   ![Adicionar o Banco de Dados SQL ao grupo de failover](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o grupo de failover e adicione o banco de dados a ele usando o PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
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

---

### <a name="test-failover"></a>Failover de Teste

Teste o failover de seu grupo de failover usando o portal do Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste o failover do grupo de failover usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e digite "SQL do Azure" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o banco de dados que você deseja adicionar ao grupo de failover.

   ![Abrir servidor para banco de BD único](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Selecione **grupos de failover** no painel **configurações** e escolha o grupo de failover que você acabou de criar.
  
   ![Selecionar o grupo de failover no portal](./media/auto-failover-group-configure/select-failover-group.png)

1. Examine qual servidor é primário e qual é secundário.
1. Selecione **failover** no painel de tarefas para fazer failover do grupo de failover que contém o banco de dados.
1. Selecione **Sim** no aviso que notifica você de que as sessões do TDS serão desconectadas.

   ![Fazer failover do grupo de failover que contém o banco de dados](./media/auto-failover-group-configure/failover-sql-db.png)

1. Examine qual servidor agora é primário e qual é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter trocado as funções.
1. Selecione **Failover** novamente para faça failover os servidores às funções originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover do grupo de failover usando o PowerShell.  

Verifique a função da réplica secundária:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

Reverta o grupo de failover de volta para o servidor primário:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Se você precisar excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes da remoção dele do grupo de failover pode causar um comportamento imprevisível.

## <a name="elastic-pool"></a>Pool elástico

Crie o grupo de failover e adicione um pool elástico a ele usando o portal do Azure ou o PowerShell.  

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- As configurações de logon e firewall do servidor para o servidor secundário devem corresponder à do servidor primário.

### <a name="create-the-failover-group"></a>Criar o grupo de failover

Crie o grupo de failover para seu pool elástico usando o portal do Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie seu grupo de failover e adicione seu pool elástico a ele usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e digite "SQL do Azure" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o pool elástico que você deseja adicionar ao grupo de failover.
1. No painel **Visão Geral**, escolha o nome do servidor em **Nome do servidor** para abrir as configurações do servidor.
  
   ![Abrir o servidor para o pool elástico](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Selecione **Grupos de failover** no painel **Configurações** e escolha **Adicionar grupo** para criar um grupo de failover.

   ![Adicionar um novo grupo de failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os valores necessários e, em seguida, selecione **criar**. Crie um novo servidor secundário ou selecione um servidor secundário existente.

1. Selecione **bancos de dados dentro do grupo** e escolha o pool elástico que você deseja adicionar ao grupo de failover. Se um pool elástico ainda não existir no servidor secundário, será exibido um aviso solicitando que você crie um pool elástico no servidor secundário. Selecione o aviso e escolha **OK** para criar o pool elástico no servidor secundário.

   ![Adicionar o pool elástico ao grupo de failover](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Escolha **Selecionar** para aplicar as configurações de pool elástico ao grupo de failover e selecione **Criar** para criar o grupo de failover. A adição do pool elástico ao grupo de failover iniciará automaticamente o processo de replicação geográfica.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie seu grupo de failover e adicione seu pool elástico a ele usando o PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
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
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Failover de Teste

Teste o failover de seu pool elástico usando o portal do Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Falhe o grupo de failover para o servidor secundário e, em seguida, faça o failback usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e digite "SQL do Azure" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o pool elástico que você deseja adicionar ao grupo de failover.
1. No painel **Visão Geral**, escolha o nome do servidor em **Nome do servidor** para abrir as configurações do servidor.

   ![Abrir o servidor para o pool elástico](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Selecione **Grupos de failover** no painel **Configurações** e escolha o grupo de failover criado na seção 2.
  
   ![Selecionar o grupo de failover no portal](./media/auto-failover-group-configure/select-failover-group.png)

1. Examine qual servidor é primário e qual é secundário.
1. Selecione **Failover** no painel de tarefas para fazer failover do grupo de failover que contém o pool elástico.
1. Selecione **Sim** no aviso que notifica você de que as sessões do TDS serão desconectadas.

   ![Fazer failover do grupo de failover que contém o banco de dados](./media/auto-failover-group-configure/failover-sql-db.png)

1. Examine qual servidor é primário e qual é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter trocado as funções.
1. Selecione **Failover** novamente para fazer failback do grupo de failover para as configurações originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover do grupo de failover usando o PowerShell.

Verifique a função da réplica secundária:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> Se você precisar excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes da remoção dele do grupo de failover pode causar um comportamento imprevisível.

## <a name="sql-managed-instance"></a>Instância Gerenciada de SQL

Crie um grupo de failover entre duas instâncias gerenciadas no Azure SQL Instância Gerenciada usando o portal do Azure ou o PowerShell.

Será necessário configurar o [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ou criar um gateway para a rede virtual de cada SQL instância gerenciada, conectar os dois gateways e, em seguida, criar o grupo de failover. 

Implante ambas as instâncias gerenciadas em [regiões emparelhadas](../../best-practices-availability-paired-regions.md) por motivos de desempenho. As instâncias gerenciadas que residem em regiões emparelhadas geograficamente têm um desempenho muito melhor em comparação com regiões não emparelhadas. 

### <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes pré-requisitos:

- A instância gerenciada secundária deve estar vazia.
- O intervalo de sub-rede para a rede virtual secundária não deve sobrepor o intervalo de sub-rede da rede virtual primária.
- O agrupamento e o fuso horário da instância gerenciada secundária devem corresponder ao da instância gerenciada primária.
- Ao conectar os dois gateways, a **chave compartilhada** deve ser a mesma para ambas as conexões.

### <a name="create-primary-virtual-network-gateway"></a>Criar gateway de rede virtual primária

Se você não tiver configurado o [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md), poderá criar o gateway de rede virtual primária com o portal do Azure ou o PowerShell.

> [!NOTE]
> O SKU do gateway afeta o desempenho da taxa de transferência. Este artigo implanta um gateway com o SKU mais básico ( `HwGw1` ). Implante um SKU mais alto (exemplo: `VpnGw3`) para obter uma taxa de transferência mais alta. Para todas as opções disponíveis, confira [SKUs de gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o gateway de rede virtual primária usando o portal do Azure.

1. No [portal do Azure](https://portal.azure.com), procure o grupo de recursos e selecione o recurso **Rede virtual** da instância gerenciada primária.
1. Selecione **Sub-redes** em **Configurações** e escolha a opção para adicionar uma nova **Sub-rede de gateway**. Mantenha os valores padrão.

   ![Adicionar um gateway à instância gerenciada primária](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Depois que o gateway de sub-rede for criado, selecione **Criar um recurso** no painel de navegação esquerdo e digite `Virtual network gateway` na caixa de pesquisa. Selecione o recurso **Gateway de rede virtual** publicado pela **Microsoft**.

   ![Criar um gateway de rede virtual](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Preencha os campos obrigatórios para configurar o gateway de sua instância gerenciada primária.

   A seguinte tabela mostra os valores necessários para o gateway da instância gerenciada primária:

    | **Campo** | Valor |
    | --- | --- |
    | **Assinatura** |  A assinatura na qual a instância gerenciada primária está localizada. |
    | **Nome** | O nome do seu gateway de rede virtual. |
    | **Região** | A região em que a instância gerenciada primária está localizada. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo de VPN** | Selecionar **baseado em rota** |
    | **SKU**| Mantenha o padrão `VpnGw1`. |
    | **Localidade**| O local onde a instância gerenciada secundária e a rede virtual secundária são.   |
    | **Rede virtual**| Selecione a rede virtual para sua instância gerenciada secundária. |
    | **Endereço IP público**| Selecione **Criar**. |
    | **Nome do endereço IP público**| Insira um nome para seu endereço IP. |
    | &nbsp; | &nbsp; |

1. Mantenha os outros valores como padrão e selecione **Examinar + criar** para examinar as configurações do gateway de rede virtual.

   ![Configurações do gateway primário](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Selecione **Criar** para criar o gateway de rede virtual.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway de rede virtual primário usando o PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Criar um gateway de rede virtual secundário

Crie o gateway de rede virtual secundário usando o portal do Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Repita as etapas na seção anterior para criar a sub-rede de rede virtual e o gateway para a instância gerenciada secundária. Preencha os campos obrigatórios para configurar o gateway da instância gerenciada secundária.

A seguinte tabela mostra os valores necessários para o gateway da instância gerenciada secundária:

   | **Campo** | Valor |
   | --- | --- |
   | **Assinatura** |  A assinatura na qual a instância gerenciada secundária está localizada. |
   | **Nome** | O nome do gateway de rede virtual, como `secondary-mi-gateway`. |
   | **Região** | A região em que a instância gerenciada secundária está localizada. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo de VPN** | Selecionar **baseado em rota** |
   | **SKU**| Mantenha o padrão `VpnGw1`. |
   | **Localidade**| O local onde a instância gerenciada secundária e a rede virtual secundária são.   |
   | **Rede virtual**| Selecione a rede virtual criada na seção 2, como `vnet-sql-mi-secondary`. |
   | **Endereço IP público**| Selecione **Criar**. |
   | **Nome do endereço IP público**| Insira um nome para o endereço IP, como `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Configurações do gateway secundário](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o gateway de rede virtual secundário usando o PowerShell.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>Conectar os gateways

Crie conexões entre os dois gateways usando o portal do Azure ou o PowerShell.

Duas conexões precisam ser criadas-a conexão do gateway primário para o gateway secundário e, em seguida, a conexão do gateway secundário com o gateway primário.

A chave compartilhada usada para ambas as conexões deve ser a mesma para cada conexão.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie conexões entre os dois gateways usando o portal do Azure.

1. Selecione **Criar um recurso** no [portal do Azure](https://portal.azure.com).
1. Digite `connection` na caixa de pesquisa e selecione ENTER para pesquisar, que levará você para o recurso **Conexão**, publicado pela Microsoft.
1. Selecione **Criar** para criar a conexão.
1. Na guia **noções básicas** , selecione os valores a seguir e, em seguida, selecione **OK**.
    1. Selecione `VNet-to-VNet` em **Tipo de conexão**.
    1. Selecione sua assinatura na lista suspensa.
    1. Selecione o grupo de recursos para sua instância gerenciada na lista suspensa.
    1. Selecione a localização da instância gerenciada primária na lista suspensa.
1. Na guia **configurações** , selecione ou insira os seguintes valores e, em seguida, selecione **OK**:
    1. Escolha o gateway de rede primário para o **Primeiro gateway de rede virtual**, como `Primary-Gateway`.  
    1. Escolha o gateway de rede secundário para o **Segundo gateway de rede virtual**, como `Secondary-Gateway`.
    1. Marque a caixa de seleção ao lado de **Estabelecer conectividade bidirecional**.
    1. Mantenha o nome da conexão primária padrão ou renomeie-a com um valor de sua escolha.
    1. Forneça uma **PSK (chave compartilhada)** para a conexão, como `mi1m2psk`.

   ![Criar uma conexão de gateway](./media/auto-failover-group-configure/create-gateway-connection.png)

1. Na guia **Resumo** , examine as configurações da conexão bidirecional e, em seguida, selecione **OK** para criar a conexão.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie conexões entre os dois gateways usando o PowerShell.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Criar o grupo de failover

Crie o grupo de failover para suas instâncias gerenciadas usando o portal do Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o grupo de failover para suas instâncias gerenciadas do SQL usando o portal do Azure.

1. Selecione **SQL do Azure** no menu de navegação do [portal do Azure](https://portal.azure.com) à esquerda. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione a instância gerenciada primária que você deseja adicionar ao grupo de failover.  
1. Em **configurações**, navegue até **instância grupos de failover** e, em seguida, escolha **Adicionar grupo** para abrir a página **grupo de failover de instância** .

   ![Adicionar um grupo de failover](./media/auto-failover-group-configure/add-failover-group.png)

1. Na página **grupo de failover de instância** , digite o nome do grupo de failover e escolha a instância gerenciada secundária na lista suspensa. Selecione **Criar** para criar o grupo de failover.

   ![Criar grupo de failover](./media/auto-failover-group-configure/create-failover-group.png)

1. Após a conclusão da implantação do grupo de failover, você será levado novamente à página **Grupo de failover**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o grupo de failover para suas instâncias gerenciadas usando o PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>Failover de Teste

Teste o failover de seu grupo de failover usando o portal do Azure ou o PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste o failover do grupo de failover usando o portal do Azure.

1. Procure a instância gerenciada _secundária_ no [portal do Azure](https://portal.azure.com) e selecione **Grupos de Failover da Instância** em configurações.
1. Examine qual instância gerenciada é a primária e qual instância gerenciada é a secundária.
1. Selecione **Failover** e escolha **Sim** no aviso sobre a desconexão das sessões do TDS.

   ![Fazer failover do grupo de failover](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Examine qual instância gerenciada é a primária e qual é a instância secundária. Se o failover tiver sido bem-sucedido, as duas instâncias deverão ter alternado as funções.

   ![As instâncias gerenciadas têm funções alternadas após o failover](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Acesse a nova _instância gerenciada_ secundária e selecione **failover** novamente para fazer failback da instância primária na função primária.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover do grupo de failover usando o PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Usar o Link Privado

O uso de um link privado permite associar um servidor lógico a um endereço IP privado específico dentro da rede virtual e da sub-rede. 

Para usar um link privado com o grupo de failover, faça o seguinte:

1. Verifique se os servidores primários e secundários estão em uma [região emparelhada](../../best-practices-availability-paired-regions.md). 
1. Crie a rede virtual e a sub-rede em cada região para hospedar pontos de extremidade privados para servidores primários e secundários, de modo que eles tenham espaços de endereço IP não sobrepostos. Por exemplo, o intervalo de endereços de rede virtual primária de 10.0.0.0/16 e o intervalo de endereços de rede virtual secundário de 10.0.0.1/16 se sobrepõem. Para obter mais informações sobre intervalos de endereços de rede virtual, consulte o blog [projetando redes virtuais do Azure](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/).
1. Crie um [ponto de extremidade privado e o Azure DNS privado Zone para o servidor primário](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint). 
1. Crie um ponto de extremidade privado para o servidor secundário também, mas, desta vez, escolha reutilizar a mesma zona de DNS privado que foi criada para o servidor primário. 
1. Depois que o link privado for estabelecido, você poderá criar o grupo de failover seguindo as etapas descritas anteriormente neste artigo. 


## <a name="locate-listener-endpoint"></a>Localizar ponto de extremidade do ouvinte

Quando o grupo de failover estiver configurado, atualize a cadeia de conexão do seu aplicativo para o ponto de extremidade do ouvinte. Isso manterá seu aplicativo conectado ao ouvinte do grupo de failover, em vez do banco de dados primário, do pool elástico ou do banco de dados de instância. Dessa forma, você não precisa atualizar manualmente a cadeia de conexão toda vez que a entidade do banco de dados faz failover e o tráfego é roteado para qualquer entidade que esteja primária no momento.

O ponto de extremidade do ouvinte está na forma de `fog-name.database.windows.net` e fica visível na portal do Azure, ao exibir o grupo de failover:

![Cadeia de conexão do grupo de failover](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Comentários

- A remoção de um grupo de failover para um banco de dados único ou em pool não interrompe a replicação e não exclui o banco de dados replicado. Você precisará parar manualmente a replicação geográfica e excluir o banco de dados do servidor secundário se desejar adicionar um banco de dados individual ou em pool de volta a um grupo de failover depois que ele tiver sido removido. A falha em qualquer um pode resultar em um erro semelhante ao da `The operation cannot be performed due to multiple errors` tentativa de adicionar o banco de dados ao grupo de failover.

## <a name="next-steps"></a>Próximas etapas

Para obter etapas detalhadas sobre como configurar um grupo de failover, consulte os seguintes tutoriais:

- [Adicionar um banco de dados individual a um grupo de failover](failover-group-add-single-database-tutorial.md)
- [Adicionar um pool elástico a um grupo de failover](failover-group-add-elastic-pool-tutorial.md)
- [Adicionar uma instância gerenciada a um grupo de failover](../managed-instance/failover-group-add-instance-tutorial.md)

Para obter uma visão geral das opções de alta disponibilidade do banco de dados SQL do Azure, consulte [replicação geográfica](active-geo-replication-overview.md) e [grupos de failover automático](auto-failover-group-overview.md).