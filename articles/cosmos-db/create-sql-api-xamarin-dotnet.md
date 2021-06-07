---
title: 'O Azure Cosmos DB: Compilar um aplicativo de tarefas pendentes com o Xamarin'
description: Apresenta um exemplo de código do Xamarin que pode ser usado para se conectar ao Azure Cosmos DB e consultá-lo
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: f02a0673a3fae33f935ae9a9cbf94f101b25c39f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449759"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Início Rápido: Criar um aplicativo de tarefas pendentes com o Xamarin usando a conta de API de SQL do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK do Java v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB.

> [!NOTE]
> Encontre um exemplo de código de um aplicativo Xamarin de exemplo totalmente canônico mostrando várias ofertas do Azure, incluindo o CosmosDB, no GitHub clicando [aqui](https://github.com/xamarinhq/app-geocontacts). Esse aplicativo demonstra a exibição de contatos geograficamente disperso, e permite que os contatos atualizem sua localização.

Este início rápido demonstra como criar uma conta de API do SQL, um banco de dados de documento e um contêiner do Azure Cosmos DB usando o portal do Azure. Em seguida, você compilará e implantará um aplicativo móvel de lista de tarefas pendentes na [API do SQL .NET](sql-api-sdk-dotnet.md) e no [Xamarin](/xamarin/) usando o [Xamarin.Forms](/xamarin/) e o [padrão de arquitetura MVVM](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm).

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="Aplicativo de tarefas pendentes do Xamarin em execução no iOS":::

## <a name="prerequisites"></a>Pré-requisitos

Se você estiver desenvolvendo no Windows e ainda não tiver o Visual Studio 2019 instalado, poderá baixar e usar o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou as cargas de trabalho do **Desenvolvimento do Azure** e do **Desenvolvimento móvel com .NET** durante a instalação do Visual Studio.

Se estiver usando um Mac, você poderá baixar o **Visual Studio para Mac** [grátis](https://www.visualstudio.com/vs/mac/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contêiner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos clonar o aplicativo de API do SQL Xamarin no GitHub, revisar o código, obter as chaves de API e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática.

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. No Visual Studio, abra **C:\git-samples\azure-cosmos-db-sql-xamarin-getting-started\src\ToDoItems.sln** 

## <a name="obtain-your-api-keys"></a>Obter as chaves de API

Volte ao Portal do Azure para obter as informações da sua chave de API e copie-as para o aplicativo.

1. No [Portal do Azure](https://portal.azure.com/), na sua conta de API do SQL do Azure Cosmos DB, no painel de navegação esquerdo, clique em **Chaves** e, em seguida, clique em **Chaves de leitura/gravação**. Você usará os botões de cópia do lado direito da tela para copiar o URI e a Chave Primária para o arquivo APIKeys.cs na próxima etapa.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Exibir e copiar uma chave de acesso no portal do Azure, folha Chaves":::

2. No Visual Studio, abra **ToDoItems.Core/Helpers/APIKeys.cs**.

3. No portal do Azure, usando o botão Copiar, copie o valor do **URI** e o torne-o o valor da variável `CosmosEndpointUrl` em APIKeys.cs.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure Portal]";
    ```

4. No portal do Azure, usando o botão Copiar, copie o valor da **CHAVE PRIMÁRIA** e torne-o o valor da `Cosmos Auth Key` em APIKeys.cs.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure Portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Examine o código

Esta solução demonstra como criar um aplicativo de tarefas pendentes usando a API do SQL do Azure Cosmos DB e o Xamarin.Forms. O aplicativo tem duas guias. A primeira contém uma exibição de lista mostrando itens de tarefas pendentes que ainda não foram concluídas. A segunda guia exibe os itens de tarefas pendentes que foram concluídos. Além de exibir os itens de tarefas pendentes não concluídos na primeira guia, você também pode adicionar novos itens de tarefas pendentes, editar os existentes e marcar itens como concluído.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Copiar dados JSON e clicar em Salvar no Data Explorer no portal do Azure":::

O código na solução ToDoItems contém:

* **ToDoItems.Core**
   * trata-se de um projeto .NET Standard contendo um projeto Xamarin.Forms e o código da lógica de aplicativo compartilhado que mantém os itens de tarefas pendentes no Azure Cosmos DB.
* **ToDoItems.Android**
  * este projeto contém o aplicativo Android.
* **ToDoItems.iOS**
  * este projeto contém o aplicativo iOS.

Agora vamos fazer uma rápida revisão de como o aplicativo se comunica com o Azure Cosmos DB.

* O Pacote NuGet do [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) deve ser adicionado em todos os projetos.
* A classe `ToDoItem` na pasta **ToDoItems.Core/Models** modela os documentos no contêiner **Itens** criados acima. Observe que a propriedade de nomeação diferencia maiúsculas de minúsculas.
* A classe `CosmosDBService` na pasta **ToDoItems.Core/Services** encapsula a comunicação com o Azure Cosmos DB.
* Na classe `CosmosDBService` há uma variável do tipo `DocumentClient`. O `DocumentClient` é usado para configurar e executar solicitações em relação à conta do Azure Cosmos DB e é instanciado:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Ao consultar um contêiner de documentos, é usado o método `DocumentClient.CreateDocumentQuery<T>`, como visto aqui na função `CosmosDBService.GetToDoItems`:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    O `CreateDocumentQuery<T>` usa um URI que aponta para o contêiner criado na seção anterior. E também é possível especificar operadores LINQ, como uma cláusula `Where`. Nesse caso, somente os itens de tarefas pendentes que não foram concluídos são retornados.

    A função `CreateDocumentQuery<T>` é executada de forma síncrona e retorna um `IQueryable<T>`. No entanto, o método `AsDocumentQuery` converte o `IQueryable<T>` para um objeto `IDocumentQuery<T>` que pode ser executado de forma assíncrona, não bloqueando, portanto, o thread de interface do usuário para aplicativos móveis.

    A função `IDocumentQuery<T>.ExecuteNextAsync<T>` recupera a página de resultados do Azure Cosmos DB, na qual uma verificação `HasMoreResults` vê se ainda há resultados adicionais a serem retornados.

> [!TIP]
> Várias funções que operam em contêineres e documentos do Azure Cosmos usam um URI como parâmetro, que especifica o endereço do contêiner ou do documento. Esse URI é construído usando a classe `URIFactory`. Todos os URIs para bancos de dados, contêineres e documentos podem ser criados com essa classe.

* A função `ComsmosDBService.InsertToDoItem` demonstra como inserir um novo documento:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    O URI do item é especificado, bem como o item a ser inserido.

* A função `CosmosDBService.UpdateToDoItem` demonstra como substituir um documento existente por um novo:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Aqui, é necessário um novo URI para identificar exclusivamente o documento a ser substituído, e ele é obtido usando o `UriFactory.CreateDocumentUri` e passando a ele os nomes de banco de dados e de contêiner e a ID do documento.

    O `DocumentClient.ReplaceDocumentAsync` substitui o documento identificado pelo URI por aquele especificado como um parâmetro.

* A exclusão de um item é demonstrada com a função `CosmosDBService.DeleteToDoItem`:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Anote novamente o URI exclusivo de documento que está sendo criado e passado para a função `DocumentClient.DeleteDocumentAsync`.

## <a name="run-the-app"></a>Executar o aplicativo

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB.

As etapas a seguir demonstram como executar o aplicativo usando o depurador do Visual Studio para Mac.

> [!NOTE]
> O uso do aplicativo de versão Android é exatamente o mesmo, e quaisquer diferenças serão indicadas nas etapas abaixo. Se você deseja depurar com o Visual Studio no Windows, documente as tarefas pendentes para que elas possam ser encontradas pelo [iOS aqui](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) e pelo [Android aqui](/xamarin/android/deploy-test/debugging/).

1. Primeiro, selecione a plataforma de destino desejada clicando no menu suspenso em destaque e selecionando a opção ToDoItems.iOS para iOS ou ToDoItems.Android para Android.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Selecionando uma plataforma para depurar no Visual Studio para Mac":::

2. Para iniciar a depuração do aplicativo, pressione cmd+Enter ou clique no botão de execução.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Iniciando a depuração no Visual Studio para Mac":::

3. Quando o simulador do iOS ou o emulador do Android concluir a inicialização, o aplicativo exibirá duas guias: para iOS, na parte inferior da tela; para Android, na parte superior da tela. A primeira mostra os itens de tarefas pendentes não concluídos; a segunda mostra os itens de tarefas pendentes concluídos.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="Iniciar tela do aplicativo de tarefas pendentes":::

4. Para concluir um item de tarefas pendentes em iOS, deslize-o para a esquerda > toque no botão **Concluir**. Para concluir um item de tarefas pendentes em Android, pressione e segure o item > toque no botão Concluir.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Concluir um item de tarefa pendente":::

5. Para editar um item de tarefas pendentes > toque no item > uma nova tela é exibida, permitindo que você insira novos valores. Ao tocar no botão Salvar, serão mantidas as alterações no Azure Cosmos DB.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Editar item de tarefa pendente":::

6. Para adicionar um item de tarefas pendentes > toque no botão **Adicionar** no canto superior direito da tela inicial > uma nova página de edição em branco será exibida.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Adicionar item de tarefa pendente":::

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos, criar um contêiner usando o Data Explorer e criar e implantar um aplicativo Xamarin. Agora, você pode importar dados adicionais para sua conta do Azure Cosmos.

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)
