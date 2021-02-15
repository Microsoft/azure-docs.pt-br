---
title: Gerenciar recursos de API do Azure Cosmos DB Core (SQL) usando CLI do Azure
description: Gerenciar recursos da API do Azure Cosmos DB Core (SQL) usando CLI do Azure.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: b13f5bfffced9afd80663d606e30e028e52643ac
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563827"
---
# <a name="manage-azure-cosmos-core-sql-api-resources-using-azure-cli"></a>Gerenciar recursos da API do Azure Cosmos Core (SQL) usando o CLI do Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O guia a seguir descreve os comandos comuns para automatizar o gerenciamento de contas, os bancos de dados e os contêineres do Azure Cosmos DB usando a CLI do Azure. As páginas de referência de todos os comandos do Azure Cosmos DB CLI estão disponíveis na [Referência de CLI do Azure](/cli/azure/cosmosdb). Você também pode encontrar mais exemplos em [amostras de CLI do Azure para o Azure Cosmos DB](cli-samples.md), incluindo como criar e gerenciar contas, bancos de dados e contêineres do Cosmos DB para MongoDB, Gremlin, Cassandra e API de Tabela.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.12.1 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

Para obter exemplos da CLI do Azure para outras APIs, confira [Exemplos de CLI para o Cassandra](cli-samples-cassandra.md), [Exemplos de CLI para a API do MongoDB](cli-samples-mongodb.md), [Exemplos de CLI para o Gremlin](cli-samples-gremlin.md), [Exemplos de CLI para a Tabela](cli-samples-table.md)

> [!IMPORTANT]
> Azure Cosmos DB recursos não podem ser renomeados, pois isso viola como o Azure Resource Manager funciona com URIs de recursos.

## <a name="azure-cosmos-accounts"></a>Contas do Azure Cosmos

As seções a seguir demonstram como gerenciar a conta do Azure Cosmos, incluindo:

* [Criar uma conta do Azure Cosmos](#create-an-azure-cosmos-db-account)
* [Adicionar ou remover regiões](#add-or-remove-regions)
* [Habilitar gravações de várias regiões](#enable-multiple-write-regions)
* [Definir prioridade de failover regional](#set-failover-priority)
* [Habilitar failover automático](#enable-automatic-failover)
* [Disparar failover manual](#trigger-manual-failover)
* [Listar chaves de conta](#list-account-keys)
* [Listar chaves de conta somente leitura](#list-read-only-account-keys)
* [Cadeias de caracteres de conexão de lista](#list-connection-strings)
* [Regenerar a chave de conta](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Criar uma conta de Azure Cosmos DB com a API do SQL, consistência de sessão nas regiões oeste dos EUA 2 e leste dos EUA 2:

> [!IMPORTANT]
> O nome da conta do Azure Cosmos deve ser minúsculo e menor que 44 caracteres.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>Adicionar ou remover regiões

Crie uma conta do Azure cosmos com duas regiões, adicione uma região e remova uma região.

> [!NOTE]
> Não é possível adicionar ou remover `locations` de regiões simultaneamente nem alterar outras propriedades de uma conta do Azure Cosmos. A modificação de regiões precisa ser executada como uma operação separada de qualquer outra alteração no recurso da conta.
> [!NOTE]
> Este comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover nem disparar um failover manual. Consulte [definir prioridade de failover](#set-failover-priority) e [disparar failover manual](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>Habilitar várias regiões de gravação

Habilitar gravações de várias regiões para uma conta do cosmos

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>Definir prioridade de failover

Definir a prioridade de failover para uma conta do Azure Cosmos configurada para failover automático

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a> Habilitar failover automático

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>Disparar failover manual

> [!CAUTION]
> A alteração da região com prioridade = 0 disparará um failover manual para uma conta do Azure Cosmos. Nenhuma outra alteração de prioridade disparará um failover.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> Listar todas as chaves de conta

Obter todas as chaves para uma conta do cosmos.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>Listar chaves de conta somente leitura

Obter chaves somente leitura para uma conta do cosmos.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>Cadeias de caracteres de conexão de lista

Obtenha as cadeias de conexão para uma conta do cosmos.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>Regenerar a chave de conta

Regenerar uma nova chave para uma conta do cosmos.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Banco de dados Azure Cosmos DB

As seguintes seções demonstram como gerenciar o banco de dados do Azure Cosmos DB, incluindo:

* [Criar um banco de dados](#create-a-database)
* [Criar um banco de dados com produtividade compartilhada](#create-a-database-with-shared-throughput)
* [Migrar um banco de dados para produtividade de dimensionamento automático](#migrate-a-database-to-autoscale-throughput)
* [Alterar taxa de transferência do banco de dados](#change-database-throughput)
* [Impedir que um banco de dados seja excluído](#prevent-a-database-from-being-deleted)

### <a name="create-a-database"></a>Criar um banco de dados

Crie um banco de dados Cosmos.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>Criar um banco de dados com produtividade compartilhada

Crie um banco de dados cosmos com taxa de transferência compartilhada.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="migrate-a-database-to-autoscale-throughput"></a>Migrar um banco de dados para produtividade de dimensionamento automático

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

# Migrate to autoscale throughput
az cosmosdb sql database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="change-database-throughput"></a>Alterar taxa de transferência do banco de dados

Aumente a taxa de transferência de um banco de dados Cosmos de 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="prevent-a-database-from-being-deleted"></a>Impedir que um banco de dados seja excluído

Coloque um bloqueio de exclusão de recurso do Azure em um banco de dados para impedir que ele seja excluído. Esse recurso requer que o bloqueio da conta Cosmos seja alterado pelos SDKs do plano de dados. Para saber mais, consulte [impedindo alterações de SDKs](role-based-access-control.md#prevent-sdk-changes). Os bloqueios de recursos do Azure também podem impedir que um recurso seja alterado, especificando um `ReadOnly` tipo de bloqueio. Para um banco de dados Cosmos, ele pode ser usado para impedir que a taxa de transferência seja alterada.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Contêiner de Azure Cosmos DB

As seguintes seções demonstram como gerenciar o contêiner do Azure Cosmos DB, incluindo:

* [Criar um contêiner](#create-a-container)
* [Criar um contêiner com dimensionamento automático](#create-a-container-with-autoscale)
* [Criar um contêiner com TTL habilitado](#create-a-container-with-ttl)
* [Criar um contêiner com a política de índice personalizada](#create-a-container-with-a-custom-index-policy)
* [Alterar a taxa de transferência do contêiner](#change-container-throughput)
* [Migrar um contêiner para taxa de transferência de dimensionamento automático](#migrate-a-container-to-autoscale-throughput)
* [Impedir que um contêiner seja excluído](#prevent-a-container-from-being-deleted)

### <a name="create-a-container"></a>Criar um contêiner

Crie um contêiner cosmos com política de índice padrão, chave de partição e RU/s de 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>Criar um contêiner com dimensionamento automático

Crie um contêiner cosmos com a política de índice padrão, a chave de partição e a autoescala RU/s de 4000.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>Criar um contêiner com TTL

Crie um contêiner cosmos com TTL habilitado.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>Criar um contêiner com uma política de índice personalizada

Crie um contêiner cosmos com uma política de índice personalizada, um índice espacial, um índice composto, uma chave de partição e RU/s de 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>Alterar a taxa de transferência do contêiner

Aumente a taxa de transferência de um contêiner Cosmos de 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="migrate-a-container-to-autoscale-throughput"></a>Migrar um contêiner para taxa de transferência de dimensionamento automático

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

# Migrate to autoscale throughput
az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="prevent-a-container-from-being-deleted"></a>Impedir que um contêiner seja excluído

Coloque um bloqueio de exclusão de recurso do Azure em um contêiner para impedir que ele seja excluído. Esse recurso requer que o bloqueio da conta Cosmos seja alterado pelos SDKs do plano de dados. Para saber mais, consulte [impedindo alterações de SDKs](role-based-access-control.md#prevent-sdk-changes). Os bloqueios de recursos do Azure também podem impedir que um recurso seja alterado, especificando um `ReadOnly` tipo de bloqueio. Para um contêiner Cosmos, isso pode ser usado para impedir que a taxa de transferência ou qualquer outra propriedade seja alterada.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure, consulte:

* [Instalar a CLI do Azure.](/cli/azure/install-azure-cli)
* [Referência de CLI do Azure](/cli/azure/cosmosdb)
* [Amostras adicionais do CLI do Azure para o Azure Cosmos DB](cli-samples.md)