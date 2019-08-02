---
title: Criar e gerenciar o Azure Cosmos DB usando o PowerShell
description: Use Azure PowerShell para gerenciar suas contas do Azure Cosmos DB, os bancos de dados, os contêineres e a taxa de transferência.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/09/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: b61c7bbc06d8d265e5dd5dddd31aceadce1f623b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797051"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gerenciar recursos da API de SQL do Azure Cosmos DB usando PowerShell

O guia a seguir descreve como usar o PowerShell para criar script e automatizar o gerenciamento de recursos do Azure Cosmos DB, incluindo a conta, banco de dados, contêiner e taxa de transferência. O gerenciamento do Azure Cosmos DB é manipulado por meio do cmdlet AzResource diretamente para o provedor de recursos do Azure Cosmos DB. Para exibir todas as propriedades que podem ser gerenciadas usando o PowerShell para o provedor de recursos do Azure Cosmos DB, confira [Esquema do provedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Para o gerenciamento multiplataforma do Azure Cosmos DB, você pode usar a [CLI do Azure](manage-with-cli.md), a [API REST][rp-rest-api] ou o [portal do Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em [Como instalar e configurar o Azure PowerShell][powershell-install-configure] para instalar e entrar em sua conta do Azure no PowerShell.

* Se você quiser executar os comandos a seguir sem a necessidade de confirmação do usuário, acrescente o sinalizador `-Force` ao comando.
* Todos os comandos a seguir são síncronos.

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

### <a id="create-account"></a> Criar uma conta do Azure Cosmos

Esse comando cria uma conta de banco de dados do Azure Cosmos DB com uma [política de consistência](consistency-levels.md) de desatualização limitada de [várias regiões][distribute-data-globally].

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` O nome da conta do Cosmos do Azure. Deve estar em letras minúsculas, aceitar caracteres alfanuméricos e o caractere '-' e ter entre 3 e 31 caracteres.
* `$location` A localização para o recurso de conta do Azure Cosmos.
* `$locations` As regiões de réplica para a conta de banco de dados. Deve haver uma região de gravação por conta de banco de dados com um valor de prioridade de failover de 0.
* `$consistencyPolicy` O nível de consistência padrão da conta do Azure Cosmos. Para obter mais informações, consulte [Níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Os valores de propriedade passados para o Provedor do Azure Resource Manager do Cosmos DB para provisionar a conta.

As contas do Azure Cosmos podem ser configuradas com o Firewall de IP, bem como pontos de extremidade de serviço de Rede Virtual. Para obter informações sobre como configurar o Firewall de IP para o Azure Cosmos DB, confira [Configurar o Firewall de IP](how-to-configure-firewall.md).  Para obter mais informações sobre como habilitar pontos de extremidade de serviço para o Azure Cosmos DB, confira [Configurar o acesso de redes virtuais](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a> Listar todas as contas do Azure Cosmos em uma assinatura

Esse comando permite listar todas as contas do Azure Cosmos em uma assinatura.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a> Obter as propriedades de uma conta do Azure Cosmos

Esse comando permite que você obtenha as propriedades de uma conta existente do Azure Cosmos DB.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Atualizar uma conta do Azure Cosmos

Esse comando permite que você atualize as propriedades de sua conta de banco de dados do Azure Cosmos DB. As propriedades que podem ser atualizadas incluem as seguintes:

* Adicionar ou remover regiões
* Alterar a política de consistência padrão
* Alterar a política de Failover
* Alterar o filtro de intervalo de IP
* Alterar as configurações de Rede Virtual
* Habilitar vários mestres

> [!NOTE]
> Esse comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover. Para modificar a prioridade de failover, confira [Modificar a prioridade de failover para uma conta do Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Excluir uma conta do Azure Cosmos

Esse comando permite que você exclua uma conta existente do Azure Cosmos.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Atualizar as marcas de uma conta do Azure Cosmos

O exemplo a seguir descreve como definir [Marcas de recurso do Azure][azure-resource-tags] para uma conta do Azure Cosmos.

> [!NOTE]
> Este comando pode ser combinado com os comandos de criação ou atualização anexando o sinalizador `-Tags` com o parâmetro correspondente.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Listar chaves da conta

Quando você cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Azure Cosmos DB é acessada. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite regenerar as chaves sem nenhuma interrupção na conta do Azure Cosmos DB. Também estão disponíveis chaves somente leitura para autenticação de operações somente leitura. Há duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Listar Cadeias de Conexão

Para contas do MongoDB, a cadeia de conexão para conectar seu aplicativo do MongoDB à conta do banco de dados pode ser recuperada usando o comando a seguir.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> Regenerar chaves da conta

Chaves de acesso para uma conta do Azure Cosmos devem ser regeneradas periodicamente para ajudar a manter conexões mais seguras. São atribuídas uma chave de acesso primária e uma chave de acesso secundária à conta. Isso permite que os clientes mantenham o acesso enquanto a outra é regenerada. Há quatro tipos de chaves para uma conta do Azure Cosmos (Primary, Secondary, PrimaryReadonly e SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> Modificar a prioridade de failover

Para contas de banco de dados de várias regiões, você pode alterar a ordem em que uma conta do Cosmos promoverá réplicas de leitura secundárias caso ocorra um failover regional na réplica de gravação primária. Quando a região com `failoverPriority=0` é modificada, esse comando pode também ser usado para iniciar uma simulação de recuperação de desastre para testar o planejamento de recuperação de desastre.

Para o exemplo a seguir, suponha que a conta tenha uma prioridade de failover atual de westus=0 e eastus=1 e inverta as regiões.

> [!CAUTION]
> Alterar `locationName` para `failoverPriority=0` disparará um failover manual para uma conta do Azure Cosmos. Nenhuma outra alteração de prioridade disparará um failover.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Banco de dados do Azure Cosmos

As seções a seguir demonstram como gerenciar o banco de dados do Azure Cosmos, incluindo:

* [Criar um banco de dados do Azure Cosmos DB](#create-db)
* [Criar um banco de dados do Azure Cosmos com taxa de transferência compartilhada](#create-db-ru)
* [Obter a taxa de transferência de um banco de dados do Azure Cosmos](#get-db-ru)
* [Listar todos os bancos de dados do Azure Cosmos em uma conta](#list-db)
* [Obter um único banco de dados do Azure Cosmos](#get-db)
* [Excluir um banco de dados do Azure Cosmos DB](#delete-db)

### <a id="create-db"></a>Criar um banco de dados do Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Criar um banco de dados do Azure Cosmos com taxa de transferência compartilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Obter a taxa de transferência de um banco de dados do Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Obter todos os bancos de dados do Azure Cosmos em uma conta

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obter um único banco de dados do Azure Cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Excluir um banco de dados do Azure Cosmos DB

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Contêiner do Azure Cosmos

As seções a seguir demonstram como gerenciar o contêiner do Azure Cosmos, incluindo:

* [Criar um contêiner do Azure Cosmos](#create-container)
* [Criar um contêiner do Azure Cosmos com uma chave de partição grande](#create-container-big-pk)
* [Obter a taxa de transferência de um contêiner do Azure Cosmos](#get-container-ru)
* [Criar um contêiner do Azure Cosmos com taxa de transferência dedicada](#create-container-ru)
* [Criar um contêiner do Azure Cosmos com indexação personalizada](#create-container-custom-index)
* [Criar um contêiner do Azure Cosmos com indexação desativada](#create-container-no-index)
* [Criar um contêiner do Azure Cosmos com chave exclusiva e TTL](#create-container-unique-key-ttl)
* [Criar um contêiner do Azure Cosmos com resolução de conflitos](#create-container-lww)
* [Listar todos os contêineres do Azure Cosmos em um banco de dados](#list-containers)
* [Obter um único contêiner do Azure Cosmos em um banco de dados](#get-container)
* [Excluir um contêiner do Azure Cosmos](#delete-container)

### <a id="create-container"></a>Criar um contêiner do Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Criar um contêiner do Azure Cosmos com um tamanho de chave de partição grande

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Obter a taxa de transferência de um contêiner do Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Criar um contêiner do Azure Cosmos com taxa de transferência dedicada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Criar um contêiner do Azure Cosmos com uma política de índice personalizada

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Criar um contêiner do Azure Cosmos com indexação desativada

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Criar um contêiner do Azure Cosmos com uma política de chave exclusiva e TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Criar um contêiner do Azure Cosmos com resolução de conflitos

Para criar uma política de resolução de conflitos para usar um procedimento armazenado, defina `"mode"="custom"` e defina o caminho de resolução como o nome do procedimento armazenado, `"conflictResolutionPath"="myResolverStoredProcedure"`. Para gravar todos os conflitos no ConflictsFeed e tratá-los separadamente, defina `"mode"="custom"` e `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Listar todos os contêineres do Azure Cosmos em um banco de dados

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Obter um único contêiner do Azure Cosmos em um banco de dados

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Excluir um contêiner do Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Próximas etapas

* [Todos os exemplos do PowerShell](powershell-samples.md)
* [Como gerenciar uma conta do Azure Cosmos](how-to-manage-database-account.md)
* [Criar um contêiner do Azure Cosmos](how-to-create-container.md)
* [Configurar a vida útil no Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
