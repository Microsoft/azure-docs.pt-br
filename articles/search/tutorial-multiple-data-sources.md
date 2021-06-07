---
title: Tutorial de C# sobre a indexação de várias fontes de dados do Azure
titleSuffix: Azure Cognitive Search
description: Saiba como importar dados de várias fontes de dados para um só índice do Azure Cognitive Search usando indexadores. Este tutorial e este código de exemplo estão em C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: df7dcf9859b6942662ae447af836f59985e2d11a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509496"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Tutorial: Indexar de várias fontes de dados usando o SDK do .NET

O Azure Cognitive Search pode importar, analisar e indexar dados de várias fontes de dados em um índice de pesquisa consolidado. 

Este tutorial usará C# e a biblioteca de clientes [Azure.Search.Documents](/dotnet/api/overview/azure/search) no SDK do Azure para .NET com o objetivo de indexar dados de hotel de exemplo do Azure Cosmos DB e mesclá-los com os detalhes de quartos de hotel extraídos de documentos do Armazenamento de Blobs do Azure. O resultado será um índice combinado de pesquisa de hotéis contendo documentos de hotéis, com os quartos sendo um tipo de dados complexos.

Neste tutorial, você executará as seguintes tarefas:

> [!div class="checklist"]
> * Fazer upload de dados de exemplo e criar fontes de dados
> * Identificar a chave de documento
> * Definir e criar o índice
> * Indexar dados de hotéis do Azure Cosmos DB
> * Mesclar dados de quartos de hotel do Armazenamento de Blobs

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="overview"></a>Visão geral

Este tutorial usará a nova biblioteca de clientes, [Azure.Search.Documents](/dotnet/api/overview/azure/search), versão 11.x para criar e executar vários indexadores. Neste tutorial, você configurará duas fontes de dados do Azure para que seja possível configurar um indexador que efetuará pull de ambas para preencher um índice de pesquisa. Os dois conjuntos de dados deverão ter um valor em comum para serem compatíveis com a mesclagem. Neste exemplo, esse campo será uma ID. Contanto que haja um campo em comum para ser compatível com o mapeamento, um indexador poderá mesclar dados de recursos diferentes: dados estruturados do SQL do Azure, dados não estruturados do Armazenamento de Blobs ou qualquer combinação de [fontes de dados compatíveis](search-indexer-overview.md#supported-data-sources) com o Azure.

Uma versão concluída do código neste tutorial pode ser encontrada no seguinte projeto:

* [multiple-data-sources/v11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Este tutorial foi atualizado para usar o pacote Azure.Search.Documents (versão 11). Para obter uma versão anterior do SDK do .NET, confira o [exemplo de código do Microsoft.Azure.Search (versão 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Armazenamento do Azure](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Pacote NuGet do Azure Cognitive Search (versão 11.x)](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Use o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, reserve um espaço no seu serviço para aceitar os novos recursos.

## <a name="1---create-services"></a>1 – Criar serviços

Este tutorial use o Azure Cognitive Search para indexação e consultas, o Azure Cosmos DB para um conjunto de dados e o Armazenamento de Blobs do Azure para o segundo conjunto de dados. 

Se possível, crie todos os serviços na mesma região e no mesmo grupo de recursos para facilitar a proximidade e a capacidade de gerenciamento. Na prática, os serviços podem estar em qualquer região.

Esta amostra usa dois conjuntos de dados pequenos que descrevem sete hotéis fictícios. Um conjunto descreve os hotéis em si e será carregado em um banco de dados do Azure Cosmos DB. O outro conjunto contém detalhes de quartos de hotel e é fornecido como sete arquivos JSON separados a serem carregados no Armazenamento de Blobs do Azure.

### <a name="start-with-cosmos-db"></a>Iniciar com o Cosmos DB

1. Entre no [portal do Azure](https://portal.azure.com) e, em seguida, navegue até a página Visão Geral da sua conta do Azure Cosmos DB.

1. Selecione **Data Explorer** e, em seguida, selecione **Novo Banco de Dados**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Criar um banco de dados" border="false":::

1. Insira o nome **hotel-rooms-db**. Aceite os valores padrão para as configurações restantes.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Configurar o banco de dados" border="false":::

1. Criar um novo contêiner. Use o banco de dados existente recém-criado. Insira **hotels** como o nome do contêiner e use **/HotelId** como a chave de partição.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Adicionar contêiner" border="false":::

1. Selecione **Itens** em **hotels** e, em seguida, clique em **Carregar Item** na barra de comandos. Navegue até o arquivo **cosmosdb/HotelsDataSubset_CosmosDb.json** e selecione-o na pasta do projeto.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Carregar na coleção do Azure Cosmos DB" border="false":::

1. Use o botão Atualizar para atualizar a exibição dos itens na coleção de hotéis. Você deverá ver sete novos documentos de banco de dados listados.

1. Copie uma cadeia de conexão da página **Chaves** no Bloco de notas. Você precisará dela para usar o **appsettings.json** em uma etapa posterior. Caso não use o nome do banco de dados sugerido, "hotel-rooms-db", copie o nome do banco de dados também.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

1. Entre no [portal do Azure](https://portal.azure.com), navegue até a sua conta de armazenamento do Azure, clique em **Blobs** e, em seguida, em **+ Contêiner**.

1. [Crie um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md) chamado **hotel-rooms** para armazenar os arquivos JSON de quartos de hotel de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Crie um contêiner de blob" border="false":::

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e, em seguida, clique em **Carregar**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Carregar arquivos" border="false":::

1. Copie o nome da conta de armazenamento e uma cadeia de conexão da página **Chaves de Acesso** no Bloco de notas. Você precisará dos dois valores para usar o **appsettings.json** em uma etapa posterior.

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O terceiro componente é a Pesquisa Cognitiva do Azure, que pode ser [criada no portal](search-create-service-portal.md). 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Copiar uma URL e uma chave de API de administração para o Azure Cognitive Search

Para se autenticar no serviço de pesquisa, você precisará obter uma URL do serviço e uma chave de acesso.

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Obter o nome do serviço e as chaves de consulta e de administrador" border="false":::

Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="2---set-up-your-environment"></a>2 – Configurar o ambiente

1. Inicie o Visual Studio e, no menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e clique em **Gerenciar Pacotes NuGet para a Solução...** . 

1. Na guia **Procurar**, localize e instale o **Azure.Search.Documents** (versão 11.0 ou posterior). Você precisará clicar nas caixas de diálogo adicionais para concluir a instalação.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Como usar o NuGet para adicionar bibliotecas do Azure" border="false":::

1. Pesquise os pacotes NuGet **Microsoft.Extensions.Configuration** e **Microsoft.Extensions.Configuration.Json** e instale-os também.

1. Abra o arquivo de solução **/v11/AzureSearchMultipleDataSources.sln**.

1. No Gerenciador de Soluções, edite o arquivo **appsettings.json** para adicionar informações de conexão.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

As duas primeiras entradas serão a URL e as chaves de administrador de um serviço de pesquisa. Use um ponto de extremidade completo, por exemplo: `https://mydemo.search.windows.net`.

As próximas entradas especificam os nomes de conta e as informações de cadeia de conexão do Armazenamento de Blobs do Azure, bem como as fontes de dados do Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 – Mapear campos de chave

A mesclagem de conteúdo exige que ambos os fluxos de dados sejam direcionados para os mesmos documentos no índice de pesquisa. 

No Azure Cognitive Search, o campo de chave identifica exclusivamente cada documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. Esse campo de chave precisa estar presente para cada documento em uma fonte de dados que é adicionada ao índice. (Na verdade, ele é o único campo obrigatório.)

Ao indexar dados de várias fontes de dados, verifique se cada linha ou documento de entrada contém uma chave de documento comum para mesclar dados de dois documentos de origem fisicamente distintos em um novo documento de pesquisa no índice combinado. 

Isso geralmente exige algum planejamento inicial para identificar uma chave de documento significativa para o índice e verificar se ela existe nas duas fontes de dados. Nesta demonstração, a chave `HotelId` de cada hotel no Cosmos DB também está presente nos blobs JSON de quartos no Armazenamento de Blobs.

Os indexadores da Pesquisa Cognitiva do Azure podem usar mapeamentos de campo para renomear e, até mesmo, reformatar os campos de dados durante o processo de indexação, de modo que os dados de origem possam ser direcionados para o campo de índice correto. Por exemplo, no Cosmos DB, o identificador de hotel é chamado **`HotelId`** . Porém, nos arquivos de blob JSON dos quartos de hotel, o identificador de hotel é chamado **`Id`** . O programa lidará com isso mapeando o campo **`Id`** dos blobs para o campo-chave **`HotelId`** do indexador.

> [!NOTE]
> Na maioria dos casos, as chaves de documento geradas automaticamente, como aquelas criadas por padrão por alguns indexadores, não servem como chaves de documento úteis para índices combinados. Em geral, o recomendável é usar um valor de chave significativo e exclusivo que já exista nas fontes de dados ou que possa ser adicionado a elas com facilidade.

## <a name="4---explore-the-code"></a>4 – Explorar o código

Depois que os dados e os parâmetros de configuração estiverem em vigor, o programa de exemplo deverá estar pronto para ser criado e executado em **/v11/AzureSearchMultipleDataSources.sln**.

Este aplicativo de console C#/.NET simples realiza as seguintes tarefas:

* Cria um índice baseado na estrutura de dados da classe Hotel C# (que também referencia as classes Address e Room).
* Cria uma fonte de dados e um indexador que mapeia os dados do Azure Cosmos DB para os campos do índice. Esses são os dois objetos no Azure Cognitive Search.
* Executa o indexador para carregar os dados de Hotel do Azure Cosmos DB.
* Cria uma segunda fonte de dados e um indexador que mapeia dados de blob JSON para os campos do índice.
* Executa o segundo indexador para carregar os dados de Rooms do Armazenamento de Blobs.

 Antes de executar o programa, reserve um minuto para estudar o código e as definições de índice e indexador desta amostra. O código relevante encontra-se em dois arquivos:

  + **Hotel.cs** contém o esquema que define o índice
  + **Program.cs** contém funções que criam o índice, as fontes de dados e os indexadores da Pesquisa Cognitiva do Azure e carrega os resultados combinados no índice.

### <a name="create-an-index"></a>Crie um índice

Este programa de exemplo usa o [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) para definir e criar um índice do Azure Cognitive Search. Ele aproveita a classe [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) para gerar uma estrutura de índice de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências às classes Address e Room. O FieldBuilder faz uma busca detalhada em várias definições de classe para gerar uma estrutura de dados complexa para o índice. As marcas de metadados são usadas para definir os atributos de cada campo, como se ele é pesquisável ou classificável.

O programa excluirá os indexadores existentes com o mesmo nome antes de criar outro, caso você queira executar esse exemplo mais de uma vez.

Os trechos de código a seguir do arquivo **Hotel.cs** mostrarão campos únicos, seguidos por uma referência a outra classe de modelo de dados, Room[], que por sua vez será definida no arquivo **Room.cs** (não mostrado).

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

No arquivo **Program.cs**, o [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) será definido com um nome e uma coleção de campos gerados pelo método `FieldBuilder.Build` e criado da seguinte maneira:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Criar uma fonte de dados e um indexador do Azure Cosmos DB

Em seguida, o programa principal inclui a lógica para criar a fonte de dados do Azure Cosmos DB para os dados de hotéis.

Primeiro, ele concatena o nome do banco de dados do Azure Cosmos DB com a cadeia de conexão. Depois ele definirá um objeto [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection).

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

Depois que a fonte de dados é criada, o programa configura um indexador do Azure Cosmos DB chamado **hotel-rooms-cosmos-indexer**.

O programa atualizará todos os indexadores existentes com o mesmo nome, além de substituir o indexador existente pelo conteúdo do código acima. Ele também incluirá ações de redefinição e execução, caso você queira executar esse exemplo mais de uma vez.

O exemplo a seguir definirá uma agenda para o indexador com o objetivo de que ele seja executado uma vez por dia. Você poderá remover a propriedade de agendamento desta chamada se não desejar que o indexador seja executado de forma automática novamente no futuro.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Este exemplo inclui um bloco simples try-catch para relatar os erros que podem ocorrer durante a execução.

Depois que o indexador do Azure Cosmos DB for executado, o índice de pesquisa conterá um conjunto completo de documentos de hotéis de exemplo. No entanto, o campo Quartos será uma matriz vazia para cada hotel, pois a fonte de dados do Azure Cosmos DB omitirá os detalhes de quartos. Em seguida, o programa efetuará pull do Armazenamento de Blobs para carregar e mesclar os dados de quartos.

### <a name="create-blob-storage-data-source-and-indexer"></a>Criar uma fonte de dados e um indexador do Armazenamento de Blobs

Para obter detalhes de quartos, o programa primeiro configurará uma fonte de dados do Armazenamento de Blobs para fazer referência a um conjunto de arquivos individuais de blobs JSON.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

Depois que a fonte de dados for criada, o programa configurará um indexador de blobs chamado **hotel-rooms-blob-indexer**, conforme mostrado abaixo.

Os blobs JSON contêm um campo de chave chamado **`Id`** em vez de **`HotelId`** . O código usa a classe `FieldMapping` para instruir o indexador a direcionar o valor do campo **`Id`** para a chave de documento **`HotelId`** no índice.

Os indexadores do Armazenamento de Blobs poderão usar o [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) para especificar um modo de análise. Será necessário definir diferentes modos de análise dependendo das hipóteses em que os blobs representam um documento ou vários documentos dentro do mesmo blob. Neste exemplo, cada blob representará um documento JSON, portanto, o código usará o modo de análise `json`. Para obter mais informações sobre parâmetros de análise de indexador para blobs JSON, confira [Indexar blobs JSON](search-howto-index-json-blobs.md).

Este exemplo define um agendamento para o indexador, de modo que ele seja executado uma vez por dia. Você poderá remover a propriedade de agendamento desta chamada se não desejar que o indexador seja executado de forma automática novamente no futuro.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

Como o índice já foi populado com os dados de hotéis do banco de dados do Azure Cosmos DB, o indexador de blob atualiza os documentos existentes no índice e adiciona os detalhes de quartos.

> [!NOTE]
> Se você tiver os mesmos campos não chave em ambas as fontes de dados e os dados contidos nesses campos não forem correspondentes, o índice conterá os valores de qualquer indexador executado mais recentemente. Em nosso exemplo, ambas as fontes de dados contêm um campo **HotelName**. Se, por alguma razão, os dados desse campo forem diferentes, para documentos com o mesmo valor de chave, os dados de **HotelName** da fonte de dados que foi indexada mais recentemente serão o valor armazenado no índice.

## <a name="5---search"></a>5 – Pesquisar

Explore o índice de pesquisa populado após a execução do programa usando o [**Search Explorer**](search-explorer.md) no portal.

No portal do Azure, abra a página **Visão Geral** do serviço de pesquisa e localize o índice **hotel-rooms-sample** na lista **Índices**.

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Lista de índices da Pesquisa Cognitiva do Azure" border="false":::

Clique no índice hotel-rooms-sample na lista. Você verá uma interface do Search Explorer para o índice. Insira uma consulta para um termo como "Luxo". Você deverá ver, pelo menos, um documento nos resultados, o qual deverá mostrar uma lista de objetos de quarto em sua matriz de quartos.

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

O código de exemplo verificará os objetos existentes. Além disso, ele excluirá ou atualizará os objetos para que seja possível executar o programa novamente.

Use também o portal para excluir índices, indexadores e fontes de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o conceito de ingestão de dados de várias fontes, vamos examinar mais de perto a configuração do indexador, começando com o Cosmos DB.

> [!div class="nextstepaction"]
> [Configurar um indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md)
