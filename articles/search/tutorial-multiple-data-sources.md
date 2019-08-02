---
title: 'Tutorial: Indexar várias fontes de dados – Azure Search'
description: Saiba como importar dados de várias fontes de dados para um único índice do Azure Search.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: v-rodixo
ms.custom: seodec2018
ms.openlocfilehash: 8ce3c66432f3d2d0cb973886498aa46e7820698c
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485255"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>Tutorial do C#: Combinar dados de várias fontes de dados em um índice do Azure Search

O Azure Search pode importar, analisar e indexar dados de várias fontes de dados em um único índice de pesquisa combinado. Isso dá suporte a situações em que os dados estruturados são agregados com os dados menos estruturados ou, até mesmo, de texto sem formatação de outras fontes, como texto, HTML ou documentos JSON.

Este tutorial descreve como indexar dados de hotéis de uma fonte de dados do Azure Cosmos DB e mesclá-los com os detalhes de quartos de hotel extraídos de documentos do Armazenamento de Blobs do Azure. O resultado será um índice combinado de pesquisa de hotéis contendo tipos de dados complexos.

Este tutorial usa o C#, o SDK do .NET para Azure Search e o portal do Azure para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Fazer upload de dados de exemplo e criar fontes de dados
> * Identificar a chave de documento
> * Definir e criar o índice
> * Indexar dados de hotéis do Azure Cosmos DB
> * Mesclar dados de quartos de hotel do Armazenamento de Blobs

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste início rápido. 

- [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

- [Crie uma conta do Azure Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de hotéis de exemplo.

- [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de blob JSON de exemplo.

- [Instalar o Visual Studio](https://visualstudio.microsoft.com/) para usar como o IDE.

### <a name="install-the-project-from-github"></a>Instalar o projeto por meio do GitHub

1. Localize o repositório de exemplo no GitHub: [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecione **Clonar ou baixar** e faça sua cópia local particular do repositório.
1. Abra o Visual Studio e instale o pacote NuGet do Microsoft Azure Search, caso ele ainda não esteja instalado. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Gerenciar Pacotes NuGet para a Solução...** . Selecione a guia **Procurar** e digite "Azure Search" na caixa de pesquisa. Instale **Microsoft.Azure.Search** quando ele for exibido na lista (versão 9.0.1 ou posterior). Você precisará clicar nas caixas de diálogo adicionais para concluir a instalação.

    ![Como usar o NuGet para adicionar bibliotecas do Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Usando o Visual Studio, navegue até o repositório local e abra o arquivo de solução **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Para interagir com o serviço Azure Search, você precisará da URL do serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Preparar dados de exemplo do Azure Cosmos DB

Esta amostra usa dois conjuntos de dados pequenos que descrevem sete hotéis fictícios. Um conjunto descreve os hotéis em si e será carregado em um banco de dados do Azure Cosmos DB. O outro conjunto contém detalhes de quartos de hotel e é fornecido como sete arquivos JSON separados a serem carregados no Armazenamento de Blobs do Azure.

1. [Entre no portal do Azure](https://portal.azure.com) e, em seguida, navegue até a página Visão Geral de sua conta do Azure Cosmos DB.

1. Na barra de menus, clique em Adicionar Contêiner. Especifique "Criar banco de dados" e use o nome **hotel-rooms-db**. Insira **hotel-rooms** para o nome da coleção e **/HotelId** para a chave de partição. Clique em **OK** para criar o banco de dados e o contêiner.

   ![Adicionar um contêiner do Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-add-container.png "Add an Azure Cosmos DB container")

1. Acesse o Cosmos DB Data Explorer e selecione o elemento **items** sob o contêiner **hotels** no banco de dados **hotel-rooms-db**. Em seguida, clique em **Carregar Item** na barra de comandos.

   ![Upload na coleção do Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Upload to Cosmos DB collection")

1. No painel de upload, clique no botão de pasta e, em seguida, navegue até o arquivo **cosmosdb/HotelsDataSubset_CosmosDb.json** na pasta do projeto. Clique em **OK** para iniciar o upload.

   ![Selecionar arquivo para upload](media/tutorial-multiple-data-sources/cosmos-upload2.png "Select file to upload")

1. Use o botão Atualizar para atualizar a exibição dos itens na coleção de hotéis. Você deverá ver sete novos documentos de banco de dados listados.

## <a name="prepare-sample-blob-data"></a>Preparar dados de blob de exemplo

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de Armazenamento do Azure, clique em **Blobs** e, em seguida, clique em **+ Contêiner**.

1. [Crie um contêiner de blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) chamado **hotel-rooms** para armazenar os arquivos JSON de quartos de hotel de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

   ![Criar um contêiner de blobs](media/tutorial-multiple-data-sources/blob-add-container.png "Create a blob container")

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "Upload on command bar")

1. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e, em seguida, clique em **Carregar**.

   ![Carregar arquivos](media/tutorial-multiple-data-sources/blob-upload.png "Upload files")

Após a conclusão do upload, os arquivos deverão ser exibidos na lista do contêiner de dados.

## <a name="set-up-connections"></a>Configurar as conexões

As informações de conexão para o serviço de pesquisa e as fontes de dados são especificadas no arquivo **appsettings.json** na solução. 

1. No Visual Studio, abra o arquivo **AzureSearchMultipleDataSources.sln**.

1. No Gerenciador de Soluções, edite o arquivo **appsettings.json**.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "BlobStorageAccountName": "Put your Azure Storage account name here",
  "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
  "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
  "CosmosDBDatabaseName": "hotel-rooms-db"
}
```

As duas primeiras entradas usam a URL e as chaves de administração do serviço Azure Search. Considerando o ponto de extremidade `https://mydemo.search.windows.net`, o nome do serviço a ser fornecido é `mydemo`.

As próximas entradas especificam os nomes de conta e as informações de cadeia de conexão do Armazenamento de Blobs do Azure, bem como as fontes de dados do Azure Cosmos DB.

### <a name="identify-the-document-key"></a>Identificar a chave de documento

No Azure Search, o campo de chave identifica exclusivamente cada documento no índice. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. Esse campo de chave precisa estar presente para cada documento em uma fonte de dados que é adicionada ao índice. (Na verdade, ele é o único campo obrigatório.)

Ao indexar dados de várias fontes de dados, cada valor de chave de fonte de dados precisa ser mapeado para o mesmo campo de chave no índice combinado. Isso geralmente exige algum planejamento inicial para identificar uma chave de documento significativa para o índice e verificar se ela existe em cada fonte de dados.

Os indexadores do Azure Search podem usar mapeamentos de campo para renomear e, até mesmo, reformatar os campos de dados durante o processo de indexação, de modo que os dados de origem possam ser direcionados para o campo de índice correto.

Por exemplo, em nossos dados de exemplo do Azure Cosmos DB, o identificador de hotel é chamado **HotelId**. Porém, nos arquivos de blob JSON dos quartos de hotel, o identificador de hotel é chamado **Id**. O programa lida com isso mapeando o campo **Id** dos blobs para o campo de chave **HotelId** no índice.

> [!NOTE]
> Na maioria dos casos, as chaves de documento geradas automaticamente, como aquelas criadas por padrão por alguns indexadores, não servem como chaves de documento úteis para índices combinados. Em geral, o recomendável é usar um valor de chave significativo e exclusivo que já exista nas fontes de dados ou que possa ser adicionado a elas com facilidade.

## <a name="understand-the-code"></a>Compreender o código

Depois que os dados e as definições configuração estiverem em vigor, o programa de exemplo em **AzureSearchMultipleDataSources.sln** deverá estar pronto para ser compilado e executado.

Este aplicativo de console C#/.NET simples realiza as seguintes tarefas:
* Cria um índice do Azure Search com base na estrutura de dados da classe Hotel C# (que também referencia as classes Address e Room).
* Cria uma fonte de dados do Azure Cosmos DB e um indexador que mapeia os dados do Azure Cosmos DB para os campos do índice.
* Executa o indexador do Azure Cosmos DB para carregar os dados de Hotel.
* Cria uma fonte de dados do Armazenamento de Blobs do Azure e um indexador que mapeia dados de blob JSON para os campos do índice.
* Executa o indexador do Armazenamento de Blobs do Azure para carregar os dados de Rooms.

 Antes de executar o programa, reserve um minuto para estudar o código e as definições de índice e indexador desta amostra. O código relevante encontra-se em dois arquivos:

  + **Hotel.cs** contém o esquema que define o índice
  + **Program.cs** contém funções que criam o índice, as fontes de dados e os indexadores do Azure Search e carrega os resultados combinados no índice.

### <a name="define-the-index"></a>Definir o índice

Este programa de exemplo usa o SDK do .NET para definir e criar um índice do Azure Search. Ele aproveita a classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) para gerar uma estrutura de índice de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências às classes Address e Room. O FieldBuilder faz uma busca detalhada em várias definições de classe para gerar uma estrutura de dados complexa para o índice. As marcas de metadados são usadas para definir os atributos de cada campo, como se ele é pesquisável ou classificável.

Os snippets a seguir do arquivo **Hotel.cs** mostram como um único campo e uma referência a outra classe de modelo de dados podem ser especificados.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

No arquivo **Program.cs**, o índice é definido com um nome e uma coleção de campos gerada pelo método `FieldBuilder.BuildForType<Hotel>()` e, em seguida, é criado da seguinte maneira:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Criar uma fonte de dados e um indexador do Azure Cosmos DB

Em seguida, o programa principal inclui a lógica para criar a fonte de dados do Azure Cosmos DB para os dados de hotéis.

Primeiro, ele concatena o nome do banco de dados do Azure Cosmos DB com a cadeia de conexão. Em seguida, ele define o objeto de fonte de dados, incluindo configurações específicas de fontes do Azure Cosmos DB, como a propriedade [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Depois que a fonte de dados é criada, o programa configura um indexador do Azure Cosmos DB chamado **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
O programa excluirá os indexadores existentes com o mesmo nome antes de criar outro, caso você deseje executar este exemplo mais de uma vez.

Este exemplo define um agendamento para o indexador, de modo que ele seja executado uma vez por dia. Você poderá remover a propriedade de agendamento desta chamada se não desejar que o indexador seja executado de forma automática novamente no futuro.

### <a name="index-azure-cosmos-db-data"></a>Indexar dados do Azure Cosmos DB

Depois que a fonte de dados e o indexador forem criados, o código que executa o indexador será breve:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Este exemplo inclui um bloco simples try-catch para relatar os erros que podem ocorrer durante a execução.

Depois que o indexador do Azure Cosmos DB for executado, o índice de pesquisa conterá um conjunto completo de documentos de hotéis de exemplo. No entanto, o campo de quartos de cada hotel será uma matriz vazia, pois a fonte de dados do Azure Cosmos DB não continha nenhum detalhe de quarto. Em seguida, o programa efetuará pull do Armazenamento de Blobs para carregar e mesclar os dados de quartos.

### <a name="create-blob-storage-data-source-and-indexer"></a>Criar uma fonte de dados e um indexador do Armazenamento de Blobs

Para obter os detalhes de quartos, o programa primeiro configura uma fonte de dados do Armazenamento de Blobs para referenciar um conjunto de arquivos de blob JSON individuais.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Depois que a fonte de dados é criada, o programa configura um indexador de blob chamado **hotel-rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Os blobs JSON contêm um campo de chave chamado **Id** em vez de **HotelId**. O código usa a classe `FieldMapping` para instruir o indexador a direcionar o valor do campo **Id** para a chave de documento **HotelId** no índice.

Os indexadores do Armazenamento de Blobs podem usar parâmetros que identificam o modo de análise a ser usado. O modo de análise varia para blobs que representam um único documento ou vários documentos no mesmo blob. Neste exemplo, cada blob representa um único documento de índice e, portanto, o código usa o parâmetro `IndexingParameters.ParseJson()`.

Para obter mais informações sobre parâmetros de análise de indexador para blobs JSON, confira [Indexar blobs JSON](search-howto-index-json-blobs.md). Para obter mais informações sobre como especificar esses parâmetros usando o SDK do .NET, confira a classe [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions).

O programa excluirá os indexadores existentes com o mesmo nome antes de criar outro, caso você deseje executar este exemplo mais de uma vez.

Este exemplo define um agendamento para o indexador, de modo que ele seja executado uma vez por dia. Você poderá remover a propriedade de agendamento desta chamada se não desejar que o indexador seja executado de forma automática novamente no futuro.

### <a name="index-blob-data"></a>Indexar dados de blob

Depois que a fonte de dados e o indexador do Armazenamento de Blobs forem criados, o código que executa o indexador será simples:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Como o índice já foi populado com os dados de hotéis do banco de dados do Azure Cosmos DB, o indexador de blob atualiza os documentos existentes no índice e adiciona os detalhes de quartos.

> [!NOTE]
> Se você tiver os mesmos campos não chave em ambas as fontes de dados e os dados contidos nesses campos não forem correspondentes, o índice conterá os valores de qualquer indexador executado mais recentemente. Em nosso exemplo, ambas as fontes de dados contêm um campo **HotelName**. Se, por alguma razão, os dados desse campo forem diferentes, para documentos com o mesmo valor de chave, os dados de **HotelName** da fonte de dados que foi indexada mais recentemente serão o valor armazenado no índice.

## <a name="search-your-json-files"></a>Pesquisar os arquivos JSON

Explore o índice de pesquisa populado após a execução do programa usando o [**Search Explorer**](search-explorer.md) no portal.

No portal do Azure, abra a página **Visão Geral** do serviço de pesquisa e localize o índice **hotel-rooms-sample** na lista **Índices**.

  ![Lista de índices do Azure Search](media/tutorial-multiple-data-sources/index-list.png "List of Azure Search indexes")

Clique no índice hotel-rooms-sample na lista. Você verá uma interface do Search Explorer para o índice. Insira uma consulta para um termo como "Luxo". Você deverá ver, pelo menos, um documento nos resultados, o qual deverá mostrar uma lista de objetos de quarto em sua matriz de quartos.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida de fazer a limpeza depois de um tutorial é excluindo o grupo de recursos que contém o serviço Azure Search. Você pode excluir o grupo de recursos agora para excluir permanentemente todo o conteúdo. No portal, o nome do grupo de recursos está na página Visão Geral do serviço Azure Search.

## <a name="next-steps"></a>Próximas etapas

Há várias abordagens e várias opções para indexar blobs JSON. Se os dados de origem incluírem algum conteúdo JSON, examine essas opções para ver o que funciona melhor para seu cenário.

> [!div class="nextstepaction"]
> [Como indexar blobs JSON usando o indexador de Blobs do Azure Search](search-howto-index-json-blobs.md)

O ideal é ampliar os dados de índice estruturados de uma fonte de dados com os dados enriquecidos cognitivamente de blobs não estruturados ou conteúdo de texto completo. O tutorial a seguir mostra como usar os Serviços Cognitivos junto com o Azure Search, usando o SDK do .NET.

> [!div class="nextstepaction"]
> [Chamar APIs de Serviços Cognitivos em um pipeline de indexação do Azure Search](cognitive-search-tutorial-blob-dotnet.md)
