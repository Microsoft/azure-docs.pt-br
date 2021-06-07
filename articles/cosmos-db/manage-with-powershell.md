---
title: Gerenciar recursos de API do Azure Cosmos DB Core (SQL) usando o PowerShell
description: Gerenciar recursos de API do Azure Cosmos DB Core (SQL) usando o PowerShell.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333419"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Gerenciar recursos de API do Azure Cosmos DB Core (SQL) usando o PowerShell
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O guia a seguir descreve como usar o PowerShell para gerar scripts e automatizar o gerenciamento de recursos de API do Azure Cosmos DB Core (SQL), incluindo a conta do cosmos, o banco de dados, o contêiner e a taxa de transferência. Para cmdlets do PowerShell para outras APIs, confira [Exemplos do PowerShell para Cassandra](powershell-samples-cassandra.md), [Exemplos do PowerShell para a API do MongoDB](powershell-samples-mongodb.md), [Exemplos do PowerShell para Gremlin](powershell-samples-gremlin.md), [Exemplos do PowerShell para a Tabela](powershell-samples-table.md)

> [!NOTE]
> Os exemplos neste artigo usam cmdlets de gerenciamento [Az.CosmosDB](/powershell/module/az.cosmosdb). Confira na página de referência da API [Az.CosmosDB](/powershell/module/az.cosmosdb) as alterações mais recentes.

Para o gerenciamento multiplataforma do Azure Cosmos DB, é possível usar os cmdlets `Az` e `Az.CosmosDB` com o [PowerShell multiplataforma](/powershell/scripting/install/installing-powershell), bem como a [CLI do Azure](manage-with-cli.md), a [API REST][rp-rest-api] ou o [portal do Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em [Como instalar e configurar o Azure PowerShell][powershell-install-configure] para instalar e entrar em sua conta do Azure no PowerShell.

> [!IMPORTANT]
> Azure Cosmos DB recursos não podem ser renomeados, pois isso viola como o Azure Resource Manager funciona com URIs de recursos.

## <a name="azure-cosmos-accounts"></a>Contas do Azure Cosmos

As seções a seguir demonstram como gerenciar a conta do Azure Cosmos, incluindo:

* [Criar uma conta do Azure Cosmos](#create-account)
* [Atualizar uma conta do Azure Cosmos](#update-account)
* [Listar todas as contas do Azure Cosmos em uma assinatura](#list-accounts)
* [Obter uma conta do Azure Cosmos](#get-account)
* [Excluir uma conta do Azure Cosmos](#delete-account)
* [Atualizar as marcas para uma conta do Azure Cosmos](#update-tags)
* [Listar as chaves para uma conta do Azure Cosmos](#list-keys)
* [Regenerar chaves para uma conta do Azure Cosmos](#regenerate-keys)
* [Listar as cadeias de conexão para uma conta do Azure Cosmos](#list-connection-strings)
* [Modificar a prioridade de failover para uma conta do Azure Cosmos](#modify-failover-priority)
* [Disparar um failover manual para uma conta do Azure Cosmos](#trigger-manual-failover)
* [Listar bloqueios de recursos em uma conta do Azure Cosmos DB](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Criar uma conta do Azure Cosmos

Esse comando cria uma conta de banco de dados do Azure Cosmos DB com [múltiplas regiões][distribute-data-globally], [failover automático](how-to-manage-database-account.md#automatic-failover) e uma [política de consistência](consistency-levels.md) de desatualização limitada.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` O grupo de recursos do Azure no qual implantar a conta do Cosmos. Ele já deve existir.
* `$locations` As regiões para a conta de banco de dados, a região com `FailoverPriority 0` é a região de gravação.
* `$accountName` O nome da conta do Cosmos do Azure. Deve ser exclusivo, em letras minúsculas, incluir somente caracteres alfanuméricos e '-' e ter entre 3 e 31 caracteres.
* `$apiKind` O tipo de conta do Cosmos a ser criada. Para obter mais informações, confira [APIs no Cosmos DB](introduction.md#simplified-application-development).
* `$consistencyPolicy`, `$maxStalenessInterval` e `$maxStalenessPrefix` O nível de consistência e as configurações padrão da conta do Azure Cosmos. Para obter mais informações, consulte [Níveis de consistência no Azure Cosmos DB](consistency-levels.md).

As contas do Azure Cosmos podem ser configuradas com o Firewall de IP, pontos de extremidade de serviço de Rede Virtual e pontos de extremidade privados. Para obter informações sobre como configurar o Firewall de IP para o Azure Cosmos DB, confira [Configurar o Firewall de IP](how-to-configure-firewall.md). Para obter informações sobre como habilitar pontos de extremidade de serviço para o Azure Cosmos DB, confira [Configurar o acesso de redes virtuais](how-to-configure-vnet-service-endpoint.md). Para obter informações sobre como habilitar pontos de extremidade privados para o Azure Cosmos DB, confira [Configurar o acesso de pontos de extremidade privados](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Listar todas as contas do Azure Cosmos em um Grupo de Recursos

Esse comando lista todas as contas do Azure Cosmos em um Grupo de Recursos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Obter as propriedades de uma conta do Azure Cosmos

Esse comando permite que você obtenha as propriedades de uma conta existente do Azure Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Atualizar uma conta do Azure Cosmos

Esse comando permite que você atualize as propriedades de sua conta de banco de dados do Azure Cosmos DB. As propriedades que podem ser atualizadas incluem as seguintes:

* Adicionar ou remover regiões
* Alterar a política de consistência padrão
* Alterar o filtro de intervalo de IP
* Alterar as configurações de Rede Virtual
* Habilitando gravações de várias regiões

> [!NOTE]
> Não é possível adicionar ou remover regiões (`locations`) simultaneamente nem alterar outras propriedades de uma conta do Azure Cosmos. A modificação de regiões precisa ser executada como uma operação separada de qualquer outra alteração na conta.
> [!NOTE]
> Este comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover nem disparar um failover manual. Confira [Modificar a prioridade de failover](#modify-failover-priority) e [Disparar um failover manual](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Habilitar várias regiões de gravação para uma conta do Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Excluir uma conta do Azure Cosmos

Este comando exclui uma conta existente do Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Atualizar as marcas de uma conta do Azure Cosmos

Esse comando define as [marcas de recurso do Azure][azure-resource-tags] para uma conta do Azure Cosmos. As marcas podem ser definidas na criação da conta usando `New-AzCosmosDBAccount`, bem como na atualização da conta usando `Update-AzCosmosDBAccount`.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Listar chaves da conta

Quando você cria uma conta do Azure Cosmos, o serviço gera duas chaves de acesso primárias que podem ser usadas para autenticação quando a conta do Azure cosmos é acessada. Também são geradas chaves somente leitura para autenticação de operações somente leitura.
Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite regenerar e girar uma chave por vez sem nenhuma interrupção na conta do Azure Cosmos.
As contas do Cosmos DB têm duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Listar Cadeias de Conexão

O comando a seguir recupera cadeias de conexão para conectar aplicativos à conta do Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Regenerar chaves da conta

Chaves de acesso para uma conta do Azure Cosmos devem ser regeneradas periodicamente para ajudar a manter conexões seguras. São atribuídas uma chave de acesso primária e uma chave de acesso secundária à conta. Isso permite que os clientes mantenham o acesso enquanto uma chave por vez é regenerada.
Há quatro tipos de chaves para uma conta do Azure Cosmos (Primary, Secondary, PrimaryReadonly e SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Habilitar failover automático

O comando a seguir define uma conta do Cosmos DB para failover automático para sua região secundária se a região primária ficar não disponível.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Modificar a prioridade de failover

Para as contas configuradas com failover automático, é possível alterar a ordem na qual o Cosmos promoverá as réplicas secundárias a primária caso a primária não esteja disponível.

Para o exemplo abaixo, suponha a prioridade de failover atual é `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2`. O comando mudará isso para `West US 2 = 0`, `South Central US = 1`, `East US 2 = 2`.

> [!CAUTION]
> Mudar o local para `failoverPriority=0` disparará um failover manual para uma conta do Azure Cosmos. Nenhuma outra alteração de prioridade disparará um failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Disparar um failover manual

Para as contas configuradas com failover manual, é possível fazer failover e promover qualquer réplica secundária para primária modificando o valor para `failoverPriority=0`. Essa operação pode ser usada para iniciar uma análise de recuperação de desastre para testar o planejamento de recuperação de desastre.

Para o exemplo abaixo, suponha que a conta tem uma prioridade de failover atual de `West US 2 = 0` e `East US 2 = 1` e inverta as regiões.

> [!CAUTION]
> Alterar `locationName` para `failoverPriority=0` disparará um failover manual para uma conta do Azure Cosmos. Nenhuma outra alteração de prioridade disparará um failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Listar bloqueios de recursos em uma conta do Azure Cosmos DB

Os bloqueios de recursos podem ser colocados em recursos do Azure Cosmos DB, incluindo bancos de dados e coleções. O exemplo abaixo mostra como listar todos os bloqueios de recursos do Azure em uma conta do Azure Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Banco de dados do Azure Cosmos DB

As seguintes seções demonstram como gerenciar o banco de dados do Azure Cosmos DB, incluindo:

* [Criar um banco de dados do Azure Cosmos DB](#create-db)
* [Criar um banco de dados do Azure Cosmos DB com taxa de transferência compartilhada](#create-db-ru)
* [Obter a taxa de transferência de um banco de dados do Azure Cosmos DB](#get-db-ru)
* [Migrar produtividade do banco de dados para dimensionamento automático](#migrate-db-ru)
* [Listar todos os bancos de dados do Azure Cosmos DB em uma conta](#list-db)
* [Obter um único banco de dados do Azure Cosmos DB](#get-db)
* [Excluir um banco de dados do Azure Cosmos DB](#delete-db)
* [Criar um bloqueio de recurso em um banco de dados do Azure Cosmos DB para impedir a exclusão](#create-db-lock)
* [Remover um bloqueio de recurso em um banco de dados do Azure Cosmos DB](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db">Criar um banco de dados do Azure Cosmos DB</a>

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Criar um banco de dados do Azure Cosmos DB com taxa de transferência compartilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Obter a taxa de transferência de um banco de dados do Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Migrar produtividade do banco de dados para dimensionamento automático

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Obter todos os bancos de dados do Azure Cosmos DB em uma conta

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Obter um único banco de dados do Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Excluir um banco de dados do Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Criar um bloqueio de recurso em um banco de dados do Azure Cosmos DB para impedir a exclusão

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Remover um bloqueio de recurso em um banco de dados do Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Contêiner do Azure Cosmos DB

As seguintes seções demonstram como gerenciar o contêiner do Azure Cosmos DB, incluindo:

* [Criar um contêiner do Azure Cosmos DB](#create-container)
* [Criar um contêiner do Azure Cosmos DB com dimensionamento automático](#create-container-autoscale)
* [Criar um contêiner do Azure Cosmos DB com uma chave de partição grande](#create-container-big-pk)
* [Obter a taxa de transferência de um contêiner do Azure Cosmos DB](#get-container-ru)
* [Migrar taxa de transferência de contêiner para dimensionamento automático](#migrate-container-ru)
* [Criar um contêiner do Azure Cosmos DB com indexação personalizada](#create-container-custom-index)
* [Criar um contêiner do Azure Cosmos DB com indexação desativada](#create-container-no-index)
* [Criar um contêiner do Azure Cosmos DB com chave exclusiva e TTL](#create-container-unique-key-ttl)
* [Criar um contêiner do Azure Cosmos DB com resolução de conflitos](#create-container-lww)
* [Listar todos os contêineres do Azure Cosmos DB em um banco de dados](#list-containers)
* [Obter um único contêiner do Azure Cosmos DB em um banco de dados](#get-container)
* [Excluir um contêiner do Azure Cosmos DB](#delete-container)
* [Criar um bloqueio de recurso em um contêiner do Azure Cosmos DB para impedir a exclusão](#create-container-lock)
* [Remover um bloqueio de recurso em um contêiner do Azure Cosmos DB](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Criar um contêiner do Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Criar um contêiner do Azure Cosmos DB com dimensionamento automático

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Criar um contêiner do Azure Cosmos DB com uma chave de partição grande

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Obter a taxa de transferência de um contêiner do Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Migrar taxa de transferência de contêiner para dimensionamento automático

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Criar um contêiner do Azure Cosmos DB com uma política de índice personalizada

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Criar um contêiner do Azure Cosmos DB com indexação desativada

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Criar um contêiner do Azure Cosmos DB com uma política de chave exclusiva e TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Criar um contêiner do Azure Cosmos DB com resolução de conflitos

Para gravar todos os conflitos no ConflictsFeed e tratá-los separadamente, passe `-Type "Custom" -Path ""`.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Para criar uma política de resolução de conflitos para usar um procedimento armazenado, chame `New-AzCosmosDBSqlConflictResolutionPolicy` e passe parâmetros `-Type` e `-ConflictResolutionProcedure`.

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Listar todos os contêineres do Azure Cosmos DB em um banco de dados

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Obter um único contêiner do Azure Cosmos DB em um banco de dados

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Excluir um contêiner do Azure Cosmos DB

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Criar um bloqueio de recurso em um contêiner do Azure Cosmos DB para impedir a exclusão

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Remover um bloqueio de recurso em um contêiner do Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Próximas etapas

* [Todos os exemplos do PowerShell](powershell-samples.md)
* [Como gerenciar uma conta do Azure Cosmos](how-to-manage-database-account.md)
* [Criar um contêiner do Azure Cosmos DB](how-to-create-container.md)
* [Configurar a vida útil no Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/