---
title: Configurar e usar o Link do Azure Synapse para Azure Cosmos DB
description: Saiba como habilitar o link do Synapse para contas de Azure Cosmos DB, criar um contêiner com o repositório analítico habilitado, conectar o banco de dados Cosmos do Azure ao Synapse espaço de trabalho e executar consultas.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: dde6af75b751037c10d7786fa5b0b03ae31d969e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222608"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Configurar e usar o Link do Azure Synapse para Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

O [Azure Synapse link para Azure Cosmos DB](synapse-link.md) é um recurso de HTAP (transacional e de processamento analítico) híbrido nativo de nuvem que permite executar análises quase em tempo real sobre dados operacionais em Azure Cosmos DB. O Link do Synapse cria uma integração perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics.

O link Synapse do Azure está disponível para os contêineres da API do SQL Azure Cosmos DB ou para a API Azure Cosmos DB para coleções do Mongo DB. Use as etapas a seguir para executar consultas analíticas com o link Synapse do Azure para Azure Cosmos DB:

* [Habilitar o link do Synapse para suas contas de Azure Cosmos DB](#enable-synapse-link)
* [Criar um repositório analítico habilitado Azure Cosmos DB contêiner](#create-analytical-ttl)
* [Conectar o banco de dados do Azure Cosmos DB a um espaço de trabalho do Synapse](#connect-to-cosmos-database)
* [Consultar o repositório analítico usando o Synapse Spark](#query-analytical-store-spark)
* [Consultar o repositório analítico usando o pool SQL sem servidor](#query-analytical-store-sql-on-demand)
* [Use o pool SQL sem servidor para analisar e Visualizar dados no Power BI](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Habilitar o link Synapse do Azure para contas de Azure Cosmos DB

### <a name="azure-portal"></a>Portal do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure](create-sql-api-dotnet.md#create-account)ou selecione uma conta de Azure Cosmos DB existente.

1. Navegue até sua conta do Azure Cosmos DB e abra o painel **recursos** .

1. Selecione o **Link do Synapse** na lista de recursos.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Localizar recurso de link do Synapse":::

1. Em seguida, ele solicita que você habilite o Link do Synapse em sua conta. Selecione **Habilitar**. Esse processo pode levar de 1 a 5 minutos para ser concluído.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Habilitar o recurso Link do Synapse":::

1. Sua conta agora está habilitada para usar o Link do Synapse. Em seguida, consulte como criar contêineres habilitados para repositório analítico para iniciar automaticamente a replicação dos dados operacionais do repositório transacional no repositório analítico.

> [!NOTE]
> Ativar o link Synapse não ativa o repositório analítico automaticamente. Depois de habilitar o link do Synapse na conta de Cosmos DB, habilite o repositório analítico em contêineres ao criá-los, para iniciar a replicação dos dados de operação para o repositório analítico. 

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a>Criar um contêiner do Azure Cosmos com repositório analítico

Você pode ativar o repositório analítico em um contêiner Azure Cosmos ao criar o contêiner. Você pode usar o portal do Azure ou configurar a propriedade `analyticalTTL` durante a criação do contêiner usando os SDKs do Azure Cosmos DB.

> [!NOTE]
> No momento, você pode habilitar o repositório analítico para **novos** contêineres (em contas novas e existentes). Você pode migrar dados de seus contêineres do existente para novos contêineres usando [Azure Cosmos DB ferramentas de migração.](cosmosdb-migrationchoices.md)

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/) ou no [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Vá até sua conta do Azure Cosmos DB e abra a guia **Data Explorer**.

1. Selecione **Novo Contêiner** e insira um nome para o banco de dados, o contêiner, a chave de partição e os detalhes da taxa de transferência. Ative a opção **Repositório analítico**. Depois de habilitar o repositório analítico, ele cria um contêiner com `AnalyicalTTL` propriedade definida como o valor padrão de -1 (retenção infinita). Esse repositório analítico retém todas as versões históricas dos registros.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Ativar repositório analítico para o contêiner do Azure Cosmos":::

1. Se você não tiver habilitado o Link do Synapse anteriormente nessa conta, ele solicitará que você faça isso porque é um pré-requisito para criar um contêiner habilitado para repositório analítico. Se solicitado, selecione **Habilitar Link do Synapse**. Esse processo pode levar de 1 a 5 minutos para ser concluído.

1. Selecione **OK** para criar um contêiner do Azure Cosmos habilitado para repositório analítico.

1. Depois que o contêiner for criado, verifique se o repositório analítico foi habilitado clicando em **configurações**, logo abaixo dos documentos em data Explorer e verifique se a opção **vida útil do repositório analítico** está ativada.

### <a name="net-sdk"></a>SDK .NET

O código a seguir cria um contêiner com o repositório analítico usando o SDK do .NET. Defina a propriedade do TTL analítico com o valor necessário. Para obter a lista de valores permitidos, consulte o artigo sobre [valores aceitos no TTL analítico](analytical-store-introduction.md#analytical-ttl):

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### <a name="java-v4-sdk"></a>SDK do Java v4

O código a seguir cria um contêiner com o repositório analítico usando o SDK do Java V4. Defina a propriedade `AnalyticalStoreTimeToLiveInSeconds` com o valor necessário:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>SDK do Python v4

Python 2,7 e Azure Cosmos DB SDK 4.1.0 são as versões mínimas necessárias, e o SDK é compatível apenas com a API do SQL.

A primeira etapa é garantir que você esteja usando pelo menos a versão 4.1.0 do SDK do [Azure Cosmos DB Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos):

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
A próxima etapa cria um contêiner com o repositório analítico usando o SDK Azure Cosmos DB Python:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Atualizar a vida útil do repositório analítico

Depois que o repositório analítico é habilitado com um determinado valor de TTL, é possível atualizá-lo posteriormente com um valor válido diferente. Você pode atualizar o valor usando o portal do Azure ou SDKs. Para obter informações sobre as várias opções de configuração do TTL analítico, consulte o artigo sobre [valores aceitos no TTL analítico](analytical-store-introduction.md#analytical-ttl).

#### <a name="azure-portal"></a>Portal do Azure

Se você criou um contêiner de repositório analítico habilitado por meio do portal do Azure, ele contém um TTL analítico padrão de -1. Use as etapas a seguir para atualizar este valor:

1. Entre no [portal do Azure](https://portal.azure.com/) ou no [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Vá até sua conta do Azure Cosmos DB e abra a guia **Data Explorer**.

1. Selecione um contêiner existente que tenha o repositório analítico habilitado. Expanda-o e modifique os seguintes valores:

  * Abra a janela **Escala e Configurações**.
  * Em **Configuração** localize **Vida útil do armazenamento analítico**.
  * Selecione **Ativado (não há padrão)** ou selecione **Ativado** e defina um valor de TTL
  * Clique em **Salvar** para salvar as alterações.

#### <a name="net-sdk"></a>SDK .NET

O código a seguir mostra como atualizar o TTL para o repositório analítico usando o SDK do .NET:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>SDK do Java v4

O código a seguir mostra como atualizar o TTL para o repositório analítico usando o SDK do Java V4:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Conectar-se a um espaço de trabalho do Synapse

Use as instruções em [Conectar-se ao Link do Azure Synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sobre como acessar um banco de dados Azure Cosmos DB do Azure Synapse Analytics Studio com o Link do Azure Synapse.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Consultar o repositório analítico usando Apache Spark para o Azure Synapse Analytics

Use as instruções no artigo [Consultar o repositório analítico do Azure Cosmos DB](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) sobre como consultar com o Synapse Spark. Esse artigo fornece alguns exemplos sobre como você pode interagir com o armazenamento analítico de gestos do Synapse. Esses gestos ficam visíveis quando você clica com o botão direito do mouse em um contêiner. Com gestos, você pode gerar código rapidamente e ajustá-lo às suas necessidades. Eles também são perfeitos para descobrir dados com um único clique.

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Consultar o repositório analítico usando o pool SQL sem servidor no Azure Synapse Analytics

O pool SQL sem servidor permite consultar e analisar dados em seus contêineres de Azure Cosmos DB que estão habilitados com o link Synapse do Azure. Você pode analisar dados quase em tempo real sem afetar o desempenho de suas cargas de trabalho transacionais. Ele oferece uma sintaxe de T-SQL familiar para consultar dados do armazenamento analítico e conectividade integrada a uma ampla gama de ferramentas de consulta de BI e ad hoc por meio da interface T-SQL. Para saber mais, consulte o artigo [repositório analítico de consulta usando o pool de SQL sem servidor](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) .

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Use o pool SQL sem servidor para analisar e Visualizar dados no Power BI

Você pode criar um banco de dados do pool SQL sem servidor e exibições sobre o link do Synapse para Azure Cosmos DB. Posteriormente, você pode consultar os contêineres Cosmos do Azure e, em seguida, criar um modelo com Power BI sobre essas exibições para refletir essa consulta. Para saber mais, Confira como usar o [pool de SQL sem servidor para analisar Azure Cosmos DB dados com o artigo de link do Synapse](synapse-link-power-bi.md) .

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

O [modelo de Azure Resource Manager](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) cria uma conta de Azure Cosmos DB do link habilitado para Synapse para a API do SQL. Este modelo cria uma conta de API do Core (SQL) em uma região com um contêiner configurado com o TTL analítico habilitado e uma opção de usar taxa de transferência de dimensionamento manual ou automático. Para implantar esse modelo, clique em **Implantar no Azure** na página do arquivo Leia-me.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Introdução ao Link do Azure Synapse - exemplos

Você pode encontrar exemplos para começar a usar o link Synapse do Azure no [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Essas soluções de demonstração de ponta a ponta com cenários de varejo e IoT. Você também pode encontrar os exemplos correspondentes à API Azure Cosmos DB para MongoDB no mesmo repositório na pasta do [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) . 

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte a seguinte documentação:

* [Link do Azure Synapse para Azure Cosmos DB.](synapse-link.md)

* [Visão geral do repositório analítico do Azure Cosmos DB.](analytical-store-introduction.md)

* [Perguntas frequentes sobre o Link do Synapse para Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark no Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Suporte a tempo de execução do pool SQL sem servidor no Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
