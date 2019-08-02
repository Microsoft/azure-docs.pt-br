---
title: 'Início Rápido: Criar um SQL Data Warehouse do Azure – Azure PowerShell| Microsoft Docs'
description: Criar rapidamente um servidor lógico do Banco de Dados SQL, uma regra de firewall de nível de servidor e data warehouse com o Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1f800ade5c5122f0891c9122f6698b6550048c67
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479333"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Início Rápido: Criar e consultar um SQL Data Warehouse do Azure com o Azure PowerShell

Crie rapidamente um SQL Data Warehouse do Azure usando o Azure PowerShell.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na assinatura do Azure usando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Para ver qual assinatura você está usando, execute [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Se você precisar usar uma assinatura diferente da padrão, execute [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Criar variáveis

Defina quais variáveis usar nos scripts neste início rápido.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico do SQL Azure](../sql-database/sql-database-logical-servers.md) usando o comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver). Um servidor lógico contém um grupo de bancos de dados gerenciados conjuntamente. O exemplo a seguir cria um servidor nomeado aleatoriamente no seu grupo de recursos com logon do usuário administrador `ServerAdmin` e senha `ChangeYourAdminPassword1`. Substitua esses valores predefinidos como desejado.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [Regra de firewall no nível do servidor do SQL do Azure](../sql-database/sql-database-firewall-configure.md) usando o comando [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte a um SQL data warehouse através do firewall do serviço do SQL Data Warehouse. No exemplo a seguir, o firewall está aberto somente para os outros recursos do Azure. Para habilitar a conectividade externa, altere o endereço IP para um endereço apropriado para seu ambiente. Para abrir todos os endereços IP, use 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Banco de Dados SQL e SQL Data Warehouse se comunicam pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá se conectar ao servidor do SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>


## <a name="create-a-data-warehouse"></a>Criar um data warehouse
Este exemplo cria um data warehouse usando as variáveis definidas anteriormente.  Ele especifica o objetivo de serviço como DW100c, que é um ponto de partida de baixo custo para o data warehouse. 

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

* **RequestedServiceObjectiveName**: A quantidade de [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) que você está solicitando. Aumentar esse valor aumenta os custos de computação. Para obter uma lista de valores com suporte, consulte [limites de memória e simultaneidade](memory-and-concurrency-limits.md).
* **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
* **ServerName**: o nome do servidor que você está usando para a criação.
* **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
* **Edition**: deve ser "DataWarehouse" para criar um SQL Data Warehouse.

Os parâmetros opcionais são:

- **CollationName**: a ordenação padrão, se não especificada, é SQL_Latin1_General_CP1_CI_AS. A ordenação não pode ser alterada em um banco de dados.
- **MaxSizeBytes**: O tamanho máximo padrão de um banco de dados é 240 TB. O tamanho máximo limita os dados de rowstore. Há armazenamento ilimitado para dados de coluna.

Para obter mais informações sobre as opções de parâmetro, consulte [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Limpar recursos

Outros tutoriais nesta coleção aproveitam este início rápido. 

> [!TIP]
> Se você planeja continuar trabalhando com os tutoriais de início rápido mais recentes, não limpe os recursos criados neste guia de início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no portal do Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Próximas etapas

Agora você criou um data warehouse, criou uma regra de firewall, conectou-se ao data warehouse e executou algumas consultas. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.
> [!div class="nextstepaction"]
>[Carregar dados no SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
