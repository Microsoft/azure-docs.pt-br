---
title: Alias DNS (PowerShell & CLI do Azure)
description: Os cmdlets do PowerShell e do CLI do Azure permitem que você redirecione novas conexões de cliente para um SQL Server diferente no Azure, sem precisar tocar em nenhuma configuração de cliente.
keywords: banco de dados sql dns
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 02cfd839ed1b75fd85553f2e5a5150cadc29ff8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790450"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para alias do DNS para o Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo fornece um script do PowerShell que demonstra como você pode gerenciar um alias DNS para o [SQL Server](logical-servers.md) que hospeda o banco de dados SQL do Azure.

> [!NOTE]
> Este artigo foi atualizado para usar o módulo Azure PowerShell AZ ou CLI do Azure. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020.
>
> Para saber mais sobre o módulo AZ e a compatibilidade do AzureRM, consulte [apresentando o módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az). Para obter instruções de instalação, consulte [instalar Azure PowerShell](/powershell/azure/install-az-ps) ou [instalar CLI do Azure](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Alias de DNS na cadeia de conexão

Para conectar um [SQL Server lógico](logical-servers.md), um cliente como o SQL Server Management Studio (SSMS) pode fornecer o nome do alias DNS em vez do nome do servidor verdadeiro. Na cadeia de caracteres de servidor de exemplo a seguir, o alias *any-unique-alias-name* substitui o primeiro nó delimitado por pontos na cadeia de caracteres de servidor de quatro nós:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Pré-requisitos

Se você deseja executar o script de demonstração do PowerShell fornecido neste artigo, os seguintes pré-requisitos se aplicam:

- Uma assinatura e conta do Azure, para avaliação gratuita, consulte [avaliações do Azure](https://azure.microsoft.com/free/)
- Dois servidores

## <a name="example"></a>Exemplo

O exemplo de código a seguir começa atribuindo valores literais a várias variáveis.

Para executar o código, edite os valores de espaço reservado para corresponder aos valores reais em seu sistema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets usados são os seguintes:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): cria um alias DNS no sistema de serviço do banco de dados SQL do Azure. O alias se refere ao servidor 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): obter e listar todos os aliases atribuídos ao servidor 1.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica o nome do servidor ao qual o alias está configurado para se referir, do servidor 1 ao servidor 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove o alias do servidor 2, usando o nome do alias.

Para instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Use `Get-Module -ListAvailable Az` no *\_ise.exedo PowerShell* para localizar a versão.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os comandos usados são os seguintes:

- [AZ SQL Server DNS-Create alias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): cria um alias DNS para um servidor. O alias se refere ao servidor 1.
- [AZ SQL Server DNS-alias show](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): obter e listar todos os aliases atribuídos ao servidor 1.
- [AZ SQL Server DNS-alias Set](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica o nome do servidor ao qual o alias está configurado para se referir, do servidor 1 para o servidor 2.
- [AZ SQL Server DNS-alias Delete](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o alias do servidor 2, usando o nome do alias.

Para instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Próximas etapas

Para obter uma explicação completa do recurso de alias de DNS para o banco de dados SQL, consulte [alias DNS para o banco de dados SQL do Azure](./dns-alias-overview.md).