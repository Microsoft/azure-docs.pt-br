---
title: Provisionar taxa de transferência de banco de dados no Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência no nível de banco de dados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 2744422e2e082c5bc6f63975b1100f336d32d5fa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250072"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Provisionar taxa de transferência em um banco de dados no Azure Cosmos DB

Este artigo explica como provisionar a taxa de transferência em um banco de dados no Azure Cosmos DB. Você pode provisionar a taxa de transferência para um único [contêiner](how-to-provision-container-throughput.md) ou para um banco de dados e compartilhá-la entre os contêineres dentro dele. Para saber quando usar taxa de transferência no nível de banco de dados e no nível de contêiner, consulte o artigo [Casos de uso para provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md). É possível provisionar a taxa de transferência no nível do banco de dados usando o portal do Azure ou os SDKs do Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Provisionar a taxa de transferência usando o portal do Azure

### <a id="portal-sql"></a>API de SQL (Core)

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Novo Banco de Dados**. Forneça os seguintes detalhes:

   * Insira uma ID do banco de dados. 
   * Selecione **Provisionar taxa de transferência**.
   * Insira uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de tela da caixa de diálogo Novo Banco de Dados](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)


## <a name="provision-throughput-using-azure-cli"></a>Provisionar a taxa de transferência usando a CLI do Azure

```azcli-interactive
az cosmosdb database create --db-name
                            [--key]
                            [--name]
                            [--resource-group-name]
                            [--subscription]
                            [--throughput]
                            [--url-connection]
```




## <a name="provision-throughput-using-powershell"></a>Provisionar a taxa de transferência usando o PowerShell

```azurepowershell-interactive
# Create a database and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName

$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties
```

## <a name="provision-throughput-using-net-sdk"></a>Provisionar a taxa de transferência usando o SDK .NET

> [!Note]
> Você pode usar os SDKs do Cosmos para a API de SQL para provisionar a taxa de transferência de todas as APIs. Opcionalmente, você pode usar o exemplo a seguir para a API do Cassandra também.

### <a id="dotnet-all"></a>Todas as APIs
### <a name="net-v2-sdk"></a>SDK do .NET V2

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a name="net-v3-sdk"></a>SDK do .NET V3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>API do Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre taxa de transferência provisionada no Azure Cosmos DB:

* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como provisionar a taxa de transferência para um contêiner](how-to-provision-container-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
