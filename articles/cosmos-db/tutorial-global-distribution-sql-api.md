---
title: 'Tutorial: Tutorial de distribuição global do Azure Cosmos DB para a API do SQL'
description: 'Tutorial: Saiba como configurar a distribuição global do Azure Cosmos DB usando a API do SQL com .NET, Java, Python e vários outros SDKs'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: aacb8d4ffb98d553b17aa52e4c4b11a4837dc1c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337856"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Tutorial: Configurar a distribuição global do Azure Cosmos DB usando a API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Neste artigo, mostraremos como usar o portal do Azure para configurar a distribuição global do Azure Cosmos DB e, depois, conectar-se usando a API do SQL.

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando as [APIs do SQL](./introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> Conectar-se a uma região preferencial usando a API do SQL

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente podem especificar a lista de preferências ordenadas de regiões a serem usadas para executar operações de documento. Com base na configuração da conta do Azure Cosmos DB, na disponibilidade regional atual e na lista de preferências especificada, o ponto de extremidade mais adequado será escolhido pelo SDK do SQL para executar operações de gravação e leitura.

Essa lista de preferências é especificada ao inicializar uma conexão usando os SDKs do SQL. Os SDKs aceitam um parâmetro opcional `PreferredLocations`, que é uma lista ordenada de regiões do Azure.

O SDK enviará automaticamente todas as gravações para a região de gravação atual. Todas as leituras serão enviadas para a primeira região disponível na lista de locais preferenciais. Se a solicitação falhar, o cliente não fará o envio para a próxima região da lista.

O SDK tentará fazer a leitura apenas das regiões especificadas nos locais preferenciais. Desse modo, se a conta do Azure Cosmos estiver disponível em quatro regiões, por exemplo, mas o cliente especificar apenas duas regiões de leitura (não gravação) em `PreferredLocations`, nenhuma leitura será feita nas regiões não especificadas em `PreferredLocations`. Se as regiões de leitura especificadas na lista `PreferredLocations` não estiverem disponíveis, as leituras serão feitas na região de gravação.

O aplicativo pode verificar o ponto de extremidade de gravação e o ponto de extremidade de leitura atuais escolhidos pelo SDK marcando duas propriedades, `WriteEndpoint` e `ReadEndpoint`, disponíveis no SDK versão 1.8 e posterior. Se a propriedade `PreferredLocations` não estiver definida, todas as solicitações serão atendidas na região de gravação atual.

Se você não especificar os locais preferenciais, mas tiver usado o método `setCurrentLocation`, o SDK preencherá automaticamente os locais preferenciais com base na região atual em que o cliente está sendo executado. O SDK ordena as regiões com base na proximidade de uma região à região atual.

## <a name="net-sdk"></a>SDK .NET

O SDK pode ser usado sem qualquer mudança de código. Nesse caso, o SDK direciona automaticamente as leituras e gravações para a região de gravação atual.

Na versão 1.8 e posteriores do SDK para .NET, o parâmetro ConnectionPolicy do construtor DocumentClient tem uma propriedade chamada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Essa propriedade é do tipo `<string>` de Coleção e deve conter uma lista de nomes de região. Os valores de cadeia de caracteres são formatados de acordo com a coluna de nome da região na página [Regiões do Azure][regions], sem espaços antes do primeiro caractere nem depois do último.

Os pontos de extremidade atuais de gravação e leitura estão disponíveis em DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint, respectivamente.

> [!NOTE]
> As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizá-las a qualquer momento. O SDK lida com essa alteração automaticamente.
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Se você estiver usando o SDK do .NET V2, use a propriedade `PreferredLocations` para definir a região preferencial.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

Como alternativa, você pode usar a propriedade `SetCurrentLocation` e permitir que o SDK escolha o local preferencial com base na proximidade.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Se você estiver usando o SDK do .NET V3, use a propriedade `ApplicationPreferredRegions` para definir a região preferencial.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

Como alternativa, você pode usar a propriedade `ApplicationRegion` e permitir que o SDK escolha o local preferencial com base na proximidade.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizá-las a qualquer momento. O SDK tratará dessa mudança automaticamente.
>
>

Veja abaixo um exemplo de código para Node.js/JavaScript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>SDK do Python

O código a seguir mostra como definir locais preferenciais usando o SDK do Python:

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> SDK do Java v4

O código a seguir mostra como definir locais preferenciais usando o SDK do Java:

# <a name="async"></a>[Async](#tab/api-async)

   API assíncrona do [SDK do Java V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   API síncrona do [SDK do Java V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

Assim que uma conta de banco de dados tiver sido disponibilizada em várias regiões, os clientes poderão consultar a disponibilidade dela executando uma solicitação GET neste URI: `https://{databaseaccount}.documents.azure.com/`

O serviço retornará uma lista de regiões e seus URIs de pontos de extremidade do Azure Cosmos DB correspondentes para as réplicas. A região de gravação atual será indicada na resposta. O cliente pode selecionar o ponto de extremidade apropriado para todas as solicitações adicionais de API REST como se segue.

Exemplo de resposta

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* Todas as solicitações All PUT, POST e DELETE devem ir para o URI de gravação indicado
* Todas as solicitações GET e outras somente leitura (por exemplo: consultas) podem ir para qualquer ponto de extremidade de escolha do cliente

As solicitações de gravação para regiões somente leitura falharão com o código de erro 403 de HTTP ("Proibido").

Se a região de gravação mudar depois da fase de descoberta inicial do cliente, as gravações subsequentes na região de gravação anterior falharão com o código de erro HTTP 403 ("Proibido"). O cliente deve obter (GET) a lista de regiões novamente para que a região de gravação seja atualizada.

Assim, concluímos este tutorial. Aprenda a gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). E para saber mais sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando as APIs do SQL

Agora você pode prosseguir para o próximo tutorial e aprender a desenvolver localmente usando o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/