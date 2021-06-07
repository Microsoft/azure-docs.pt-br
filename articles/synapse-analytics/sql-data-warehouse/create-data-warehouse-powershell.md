---
title: 'Início rápido: Criar um pool de SQL dedicado (antigo SQL DW) com o Azure PowerShell'
description: Crie rapidamente um pool de SQL dedicado (antigo SQL DW) com uma regra de firewall no nível do servidor usando o Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
ms.author: xiaoyul
manager: craigg
ms.reviewer: igorstan
ms.date: 4/11/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 65bf509c8eb654a9f7712fdf7b94ff7fa26a3d32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537447"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Início rápido: Criar um pool de SQL dedicado (antigo SQL DW) com o Azure PowerShell

Criar um pool de SQL dedicado (antigo SQL DW) no Azure Synapse Analytics usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT]
> A criação de um pool de SQL dedicado (antigo SQL DW) pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na assinatura do Azure usando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Para ver qual assinatura você está usando, execute [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Se você precisar usar uma assinatura diferente da padrão, execute [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Criar variáveis

Defina quais variáveis usar nos scripts neste início rápido.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Um grupo de recursos é um contêiner no qual os recursos do Azure são implantados e gerenciados como um grupo. O exemplo a seguir cria um grupo de recursos chamado `myResourceGroup` na localização `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Criar um servidor

Crie um [servidor SQL lógico](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) usando o comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Um servidor contém um grupo de bancos de dados gerenciados conjuntamente. O exemplo a seguir cria um servidor nomeado aleatoriamente no seu grupo de recursos com logon do usuário administrador `ServerAdmin` e senha `ChangeYourAdminPassword1`. Substitua esses valores predefinidos como desejado.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

Crie uma [regra de firewall no nível do servidor](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) usando o comando [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte ao pool de SQL dedicado (antigo SQL DW) por meio do firewall do serviço de pool de SQL dedicado.

No exemplo a seguir, o firewall está aberto somente para os outros recursos do Azure. Para habilitar a conectividade externa, altere o endereço IP para um endereço apropriado para seu ambiente. Para abrir todos os endereços IP, use 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Os pontos de extremidade SQL comunicam-se pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isso acontecer, você não conseguirá se conectar ao servidor, a menos que o departamento de TI abra a porta 1433.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Criar um pool de SQL dedicado (antigo SQL DW)

O exemplo a seguir cria um pool de SQL dedicado (antigo SQL DW) usando as variáveis definidas anteriormente.  Ele especifica o objetivo do serviço como DW100c, que é um ponto de partida de custo mais baixo para seu pool de SQL dedicado (antigo SQL DW).

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Os parâmetros requeridos são:

* **RequestedServiceObjectiveName**: A quantidade de [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) que você está solicitando. Aumentar esse valor aumenta os custos de computação. Para obter uma lista de valores com suporte, consulte [limites de memória e simultaneidade](memory-concurrency-limits.md).
* **DatabaseName**: o nome do pool de SQL dedicado (antigo SQL DW) que você está criando.
* **ServerName**: o nome do servidor que você está usando para a criação.
* **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
* **Edition**: precisa ser "DataWarehouse" para criar um pool de SQL dedicado (antigo SQL DW).

Os parâmetros opcionais são:

* **CollationName**: a ordenação padrão, se não especificada, é SQL_Latin1_General_CP1_CI_AS. A ordenação não pode ser alterada em um banco de dados.
* **MaxSizeBytes**: O tamanho máximo padrão de um banco de dados é 240 TB. O tamanho máximo limita os dados de rowstore. Há armazenamento ilimitado para dados de coluna.

Para obter mais informações sobre as opções de parâmetro, consulte [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais nesta coleção aproveitam este início rápido.

> [!TIP]
> Se você planeja continuar trabalhando com os tutoriais de início rápido mais recentes, não limpe os recursos criados neste guia de início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no portal do Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Próximas etapas

Você acabou de criar um pool de SQL dedicado (antigo SQL DW), criar uma regra de firewall e se conectar ao seu pool de SQL dedicado. Para saber mais, prossiga para o artigo [Carregar dados para um pool de SQL dedicado](./load-data-from-azure-blob-storage-using-copy.md).
