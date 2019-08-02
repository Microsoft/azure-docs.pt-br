---
title: Tutorial de distribuição global do Azure Cosmos DB para a API do SQL
description: Saiba como configurar a distribuição global do Azure Cosmos DB usando a API do SQL.
author: rimman
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a566094f88ba9ffd25eadd046ae7254e26b9c2cf
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234589"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Configurar a distribuição global do Azure Cosmos DB usando a API do SQL

Neste artigo, mostraremos como usar o Portal do Azure para configurar a distribuição global do Azure Cosmos DB e, depois, conectar-se usando a API do SQL.

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando as [APIs do SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Conectar-se a uma região preferencial usando a API do SQL

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente podem especificar a lista de preferências ordenadas de regiões a serem usadas para executar operações de documento. Isso pode ser feito definindo a política de conexão. Com base na configuração da conta do Azure Cosmos DB, na disponibilidade regional atual e na lista de preferências especificada, o ponto de extremidade mais adequado será escolhido pelo SDK do SQL para executar operações de gravação e leitura.

Essa lista de preferências é especificada ao inicializar uma conexão usando os SDKs do SQL. Os SDKs aceitam um parâmetro opcional "PreferredLocations", que é uma lista ordenada de regiões do Azure.

O SDK enviará automaticamente todas as gravações para a região de gravação atual.

Todas as leituras serão enviadas para a primeira região disponível na lista PreferredLocations. Se a solicitação falhar, o cliente não fará o envio para a próxima região da lista, e assim por diante.

Os SDKs tentarão ler apenas das regiões especificadas em PreferredLocations. Desse modo, se a Conta do Banco de Dados estiver disponível em quatro regiões, por exemplo, mas o cliente especificar apenas duas regiões de leitura (não gravação) em PreferredLocations, nenhuma leitura será atendida fora da região de leitura que não esteja especificada em PreferredLocations. Se as regiões de leitura especificadas em PreferredLocations não estiverem disponíveis, as leituras serão atendidas fora da região de gravação.

O aplicativo pode verificar o ponto de extremidade de gravação e o ponto de extremidade de leitura atuais escolhidos pelo SDK marcando duas propriedades, WriteEndpoint e ReadEndpoint, disponíveis no SDK versão 1.8 e superiores.

Se a propriedade PreferredLocations não estiver definida, todas as solicitações serão atendidas na região de gravação atual.

## <a name="net-sdk"></a>SDK .NET
O SDK pode ser usado sem qualquer mudança de código. Nesse caso, o SDK direciona automaticamente as leituras e gravações para a região de gravação atual.

Na versão 1.8 e posteriores do SDK para .NET, o parâmetro ConnectionPolicy do construtor DocumentClient tem uma propriedade chamada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Essa propriedade é do tipo `<string>` de Coleção e deve conter uma lista de nomes de região. Os valores de cadeia de caracteres são formatados de acordo com a coluna Nome da Região na página [Regiões do Azure][regions], sem espaços antes nem depois do primeiro e do último caractere, respectivamente.

Os pontos de extremidade atuais de gravação e leitura estão disponíveis em DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint, respectivamente.

> [!NOTE]
> As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizá-las a qualquer momento. O SDK lida com essa alteração automaticamente.
>
>

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

## <a name="nodejsjavascript"></a>Node.js/JavaScript

O SDK pode ser usado sem qualquer mudança de código. Nesse caso, o SDK direcionará automaticamente as leituras e gravações para a região de gravação atual.

Na versão 1.8 e posteriores de cada SDK, o parâmetro ConnectionPolicy do construtor DocumentClient tem uma nova propriedade chamada DocumentClient.ConnectionPolicy.PreferredLocations. Esse parâmetro é uma matriz de cadeias de caracteres que usa uma lista de nomes de região. Os nomes são formatados de acordo com a coluna Nome da Região na página [Regiões do Azure][regions]. Você também pode usar as constantes predefinidas no objeto de conveniência AzureDocuments.Regions

Os pontos de extremidade atuais de gravação e leitura estão disponíveis em DocumentClient.getWriteEndpoint e DocumentClient.getReadEndpoint, respectivamente.

> [!NOTE]
> As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizá-las a qualquer momento. O SDK tratará dessa mudança automaticamente.
>
>

Veja abaixo um exemplo de código para Node.js/JavaScript.

```JavaScript
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="python-sdk"></a>SDK do Python

O código a seguir mostra como definir locais preferenciais usando o SDK do Python:

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>SDK do Java V2

O código a seguir mostra como definir locais preferenciais usando o SDK do Java:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
Assim que uma conta de banco de dados tiver sido disponibilizada em várias regiões, os clientes poderão consultar sua disponibilidade executando uma solicitação GET no URI a seguir.

    https://{databaseaccount}.documents.azure.com/

O serviço retornará uma lista de regiões e seus URIs de pontos de extremidade do Azure Cosmos DB correspondentes para as réplicas. A região de gravação atual será indicada na resposta. O cliente pode selecionar o ponto de extremidade apropriado para todas as solicitações adicionais de API REST como se segue.

Exemplo de resposta

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

