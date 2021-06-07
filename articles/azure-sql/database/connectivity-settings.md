---
title: Configurações de conectividade para o banco de dados SQL do Azure e o Azure Synapse Analytics
description: Este artigo explica a opção de versão do protocolo TLS e o proxy versus as configurações de redirecionamento para o banco de dados SQL do Azure e a análise de Synapse do Azure.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 0c45a48e6cafa722945400554f2f81916da13775
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627601"
---
# <a name="azure-sql-connectivity-settings"></a>Configurações de conectividade do SQL do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo apresenta as configurações que controlam a conectividade com o servidor para o banco de dados SQL do Azure e o [pool de SQL dedicado (anteriormente conhecido como SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) no Azure Synapse Analytics. Essas configurações se aplicam a todos os bancos de dados SQL e do pool dedicado do SQL (anteriormente SQL DW) associados ao servidor.

> [!IMPORTANT]
> Este artigo não se aplica ao Azure SQL Instância Gerenciada.

As configurações de conectividade podem ser acessadas na tela **firewalls e redes virtuais** , conforme mostrado na seguinte captura:

 ![Captura de tela da janela de configurações de conectividade.][1]

> [!NOTE]
> Essas configurações entram em vigor imediatamente depois que são aplicadas. Seus clientes poderão enfrentar perda de conexão se não atenderem aos requisitos de cada configuração.

## <a name="deny-public-network-access"></a>Negar acesso à rede pública

Quando **negar acesso à rede pública** é definido como **Sim**, somente as conexões por meio de pontos de extremidade privados são permitidas. Quando essa configuração é **não** (padrão), os clientes podem se conectar usando pontos de extremidade públicos (com regras de firewall baseadas em IP ou com regras de firewall baseadas em rede virtual) ou pontos de extremidade privados (usando o link privado do Azure), conforme descrito na [visão geral de acesso à rede](network-access-controls-overview.md).

 ![Diagrama mostrando a conectividade quando negar acesso à rede pública está definido como Sim versus quando negar acesso à rede pública está definido como não.][2]

Qualquer tentativa de definir o **acesso de rede pública Deny** como **Sim** sem nenhum ponto de extremidade privado existente no servidor lógico falhará com uma mensagem de erro semelhante a:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Para definir regras de firewall de rede virtual em um servidor lógico que já foi configurado com pontos de extremidade privados, defina **negar acesso à rede pública** como **não**.

Quando **negar acesso à rede pública** é definido como **Sim**, somente as conexões por meio de pontos de extremidade privados são permitidas. Todas as conexões por meio de pontos de extremidade públicos serão negadas com uma mensagem de erro semelhante a:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Quando **negar acesso à rede pública** estiver definido como **Sim**, qualquer tentativa de adicionar ou atualizar regras de firewall será negada com uma mensagem de erro semelhante a:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar o acesso à rede pública por meio do PowerShell

> [!IMPORTANT]
> O banco de dados SQL do Azure ainda dá suporte ao módulo Azure Resource Manager do PowerShell, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como `Get` e `Set` a propriedade de **acesso à rede pública** no nível do servidor:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Alterar o acesso à rede pública via CLI

> [!IMPORTANT]
> Todos os scripts nesta seção exigem o [CLI do Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash

O script da CLI a seguir mostra como alterar a configuração de **acesso à rede pública** em um shell bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Versão mínima do TLS 

A configuração de versão mínima do [protocolo TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite que os clientes escolham qual versão do TLS seu banco de dados SQL usa.

No momento, damos suporte ao TLS 1.0, 1.1 e 1.2. A definição de uma versão mínima do TLS garante compatibilidade com as versões mais recentes do TLS. Por exemplo, a escolha de uma versão de TLS 1,1 significa que somente as conexões com TLS 1,1 e 1,2 são aceitas e as conexões com TLS 1,0 são rejeitadas. Depois de você confirmar que os aplicativos dão suporte ao protocolo, é recomendável definir a versão mínima do TLS como 1.2. Essa versão inclui a correção das vulnerabilidades de versões anteriores, além de ser a versão mais recente do TLS compatível no banco de dados SQL do Azure.

> [!IMPORTANT]
> O padrão para a versão mínima do TLS é permitir todas as versões. Depois de aplicar uma versão do TLS, não é possível reverter para o padrão.

Para clientes com aplicativos que dependem de versões anteriores do TLS, é recomendável definir a versão mínima do TLS de acordo com os requisitos dos aplicativos. Para clientes que dependem de aplicativos para se conectarem usando uma conexão não criptografada, recomendamos não definir uma versão mínima do TLS.

Para obter mais informações, consulte [Considerações sobre TLS para conectividade de banco de dados SQL](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Depois de definir a versão mínima do TLS, as tentativas de logon de clientes que estão usando uma versão TLS inferior à versão mínima do TLS do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>Definir a versão mínima do TLS por meio do PowerShell

> [!IMPORTANT]
> O banco de dados SQL do Azure ainda dá suporte ao módulo Azure Resource Manager do PowerShell, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como `Get` e `Set` a propriedade de **versão mínima do TLS** no nível do servidor lógico:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Definir a versão mínima do TLS por meio do CLI do Azure

> [!IMPORTANT]
> Todos os scripts nesta seção exigem o [CLI do Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash

O script de CLI a seguir mostra como alterar a configuração de **versão mínima do TLS** em um shell bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Alterar a política de conexão

A [política de conexão](connectivity-architecture.md#connection-policy) determina como os clientes se conectam ao banco de dados SQL do Azure.

## <a name="change-the-connection-policy-via-powershell"></a>Alterar a política de conexão por meio do PowerShell

> [!IMPORTANT]
> O banco de dados SQL do Azure ainda dá suporte ao módulo Azure Resource Manager do PowerShell, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como alterar a política de conexão usando o PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Alterar a política de conexão por meio do CLI do Azure

> [!IMPORTANT]
> Todos os scripts nesta seção exigem o [CLI do Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash

O script da CLI a seguir mostra como alterar a política de conexão em um shell bash:

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>CLI do Azure de um prompt de comando do Windows

O script da CLI a seguir mostra como alterar a política de conexão de um prompt de comando do Windows (com o CLI do Azure instalado):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral de como funciona a conectividade no banco de dados SQL do Azure, consulte [arquitetura de conectividade](connectivity-architecture.md).
- Para obter informações sobre como alterar a política de conexão para um servidor, consulte [Conn-Policy](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
