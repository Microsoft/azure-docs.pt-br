---
title: 'Tutorial do ASP.NET Core MVC para o Azure Cosmos DB: Desenvolvimento de aplicativo Web'
description: Tutorial do ASP.NET Core MVC para criar um aplicativo Web MVC usando o Azure Cosmos DB. Você armazenará o JSON e acessará dados de um aplicativo de tarefas pendentes hospedado no Serviço de Aplicativo do Azure ‒ tutorial passo a passo do ASP.NET Core MVC.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67985846"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: Desenvolver um aplicativo Web ASP.NET Core MVC com o Azure Cosmos DB usando o SDK do .NET 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Este tutorial mostra a você como usar o Azure Cosmos DB para armazenar e acessar dados de um aplicativo Web ASP.NET MVC hospedado no Azure. Neste tutorial, você usará o SDK do .NET V3. A imagem abaixo mostra a página da Web que você criará usando o exemplo neste artigo:
 
![Captura de tela do aplicativo Web MVC de tarefas pendentes criado por este tutorial – tutorial passo a passo do ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Se você não tiver tempo para concluir o tutorial, baixe o projeto de exemplo completo no [GitHub][GitHub].

Este tutorial abrange:

> [!div class="checklist"]
> * Como criar uma conta do Azure Cosmos
> * Como criar um aplicativo ASP.NET Core MVC
> * Como conectar o aplicativo ao Azure Cosmos DB 
> * Executar operações CRUD nos dados

> [!TIP]
> Este tutorial pressupõe que você tenha experiência anterior com o uso do ASP.NET Core MVC e o Serviço de Aplicativo do Azure. Caso esteja conhecendo agora o ASP.NET Core ou as [ferramentas de pré-requisito](#prerequisites), recomendaremos que você baixe o projeto de exemplo completo no [GitHub][GitHub], adicione os pacotes NuGet necessários e execute-o. Depois de compilar o projeto, você poderá consultar esse artigo para obter insights sobre o código no contexto do projeto.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes recursos:

* **Uma conta ativa do Azure:** Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Todas as capturas de tela deste artigo foram feitas usando o Microsoft Visual Studio Community 2019. Se o seu sistema estiver configurado com uma versão diferente, suas telas e opções poderão não corresponder totalmente, mas se você cumprir os pré-requisitos acima, esta solução deverá funcionar.

## <a name="create-an-azure-cosmos-account"></a>Etapa 1: Criar uma conta do Azure Cosmos

Vamos começar criando uma conta do Azure Cosmos. Se você já tiver uma conta da API de SQL do Azure Cosmos DB, ou se estiver usando o emulador do Azure Cosmos DB para este tutorial, poderá avançar para a seção [Criar um novo aplicativo ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Na próxima seção, você criará um aplicativo ASP.NET Core MVC. 

## <a name="create-a-new-mvc-application"></a>Etapa 2: Criar um aplicativo ASP.NET Core MVC

1. No Visual Studio, no menu **Arquivo**, selecione **Novo** e **Projeto**. A caixa de diálogo **Novo Projeto** aparecerá.

2. Na janela **Novo Projeto**, use a caixa de entrada **Pesquisar modelos** para pesquisar "Web" e, em seguida, selecione **Aplicativo Web ASP.NET Core**. 

   ![Criar um projeto de aplicativo Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Na caixa **Nome** , digite o nome do projeto. Este tutorial usará o nome "todo". Se você optar por usar outro nome, sempre que este tutorial mencionar o namespace de tarefas pendentes, ajuste os exemplos de código fornecidos para usar o nome do aplicativo. 

4. Selecione **Procurar** para navegar até a pasta na qual deseja criar o projeto. Selecione **Criar**. 

5. A caixa de diálogo **Criar um Aplicativo Web ASP.NET Core** será exibida. Na lista de modelos, selecione **Aplicativo Web (Model-View-Controller)** .

6. Selecione **Criar** e deixe o Visual Studio fazer o scaffolding do modelo ASP.NET Core MVC vazio. 

7. Depois que o Visual Studio concluir a criação do aplicativo MVC de texto clichê, você terá um aplicativo ASP.NET vazio que poderá ser executado localmente.

## <a name="add-nuget-packages"></a>Etapa 3: Adicionar o pacote NuGet do Azure Cosmos DB ao projeto

Agora que temos a maior parte do código da estrutura ASP.NET Core MVC de que precisamos para esta solução, vamos adicionar os pacotes NuGet necessários para a conexão com o Azure Cosmos DB.

1. O SDK .NET do Azure Cosmos DB é empacotado e distribuído como um pacote do NuGet. Para obter o pacote NuGet no Visual Studio, use o gerenciador de pacotes NuGet no Visual Studio clicando com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**.
   
   ![Captura de tela das opções acessadas ao clicar com o botão direito do mouse para o projeto de aplicativo Web no Gerenciador de Soluções, com Gerenciar Pacotes NuGet realçado.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. A caixa de diálogo **Gerenciar Pacotes NuGet** será exibida. Na caixa **Procurar** do NuGet, digite **Microsoft.Azure.Cosmos**. Com base nos resultados, instale o pacote **Microsoft.Azure.Cosmos**. Ele baixará e instalará o pacote do Azure Cosmos DB e suas dependências. Selecione **Aceito** na janela **Aceitação da Licença** para concluir a instalação.
   
   Como alternativa, você pode usar o Console do Gerenciador de Pacotes para instalar o pacote NuGet. Para isso, no menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes**. No prompt, digite o seguinte comando:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Depois que o pacote for instalado, o projeto do Visual Studio deverá conter a referência de biblioteca para Microsoft.Azure.Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Etapa 4: Configurar o aplicativo ASP.NET Core MVC

Agora vamos adicionar os modelos, as exibições e os controladores ao aplicativo MVC:

* [Adicionar um modelo](#add-a-model).
* [Adicionar um controlador](#add-a-controller).
* [Adicionar exibições](#add-views).

### <a name="add-a-model"></a> Adicionar um modelo

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modelos**, selecione **Adicionar** e **Classe**. A caixa de diálogo **Adicionar Novo Item** aparecerá.

1. Nomeie a nova classe **Item.cs** e selecione **Adicionar**. 

1. Em seguida, substitua o código na classe "Item.cs" pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Os dados armazenados no Azure Cosmos DB são transferidos e armazenados como JSON. Para controlar a forma como os objetos são serializados/desserializados pelo JSON.NET, use o atributo **JsonProperty**, como demonstrado na classe **Item** criada. Além de poder controlar o formato do nome da propriedade inserido no JSON, você também pode renomear as propriedades do .NET, como fez com a propriedade **Concluído**. 

### <a name="add-a-controller"></a>Adicionar um controlador

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Controladores**, selecione **Adicionar** e **Controlador**. A caixa de diálogo **Adicionar Scaffold** aparecerá.

1. Selecione **Controlador MVC – Vazio** e **Adicionar**.

   ![Captura de tela da caixa de diálogo Adicionar Scaffold com a opção Controlador MVC – Vazio realçada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nomeie o novo controlador, **ItemController**, e substitua o código nesse arquivo pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   O atributo **ValidateAntiForgeryToken** é usado aqui para ajudar a proteger esse aplicativo contra ataques de solicitação intersite forjada. Isso envolve mais do que apenas adicionar esse atributo, pois as exibições também precisam trabalhar com esse token antifalsificação. Para saber mais sobre o assunto e ver exemplos de como implementar isso corretamente, confira [Impedir solicitação intersite forjada][Preventing Cross-Site Request Forgery]. O código-fonte fornecido no [GitHub][GitHub] tem a implementação completa estabelecida.

   Também usamos o atributo **Bind** no parâmetro de método para ajudar na proteção contra ataques overposting. Para obter mais detalhes, confira [Operações CRUD básicas no ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

### <a name="add-views"></a>Adicionar exibições

Em seguida, vamos criar as três exibições abaixo: 

* [Adicionar uma exibição de item de lista](#AddItemIndexView).
* [Adicionar uma exibição Novo item](#AddNewIndexView).
* [Adicionar uma exibição Editar item](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Adicionar uma exibição de item de lista

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse na pasta vazia **Item** que o Visual Studio criou quando você adicionou **ItemController** anteriormente, clique em **Adicionar** e em **Exibição**.
   
   ![Captura de tela do Gerenciador de Soluções mostrando a pasta Item que o Visual Studio criou com os comandos Adicionar Exibição realçados](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. Na caixa de diálogo **Adicionar Exibição**, atualize os seguintes valores:
   
   * Na caixa **Nome da exibição**, digite ***Índice***.
   * Na caixa **Modelo**, selecione ***Lista***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
     
   ![Captura de tela mostrando a caixa de diálogo Adicionar Exibição](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Depois de definir todos esses valores, selecione **Adicionar** e deixe o Visual Studio criar uma nova exibição de modelo. Quando terminar, ele abrirá o arquivo cshtml criado. Você pode fechar esse arquivo no Visual Studio, pois voltará a ele mais tarde.

#### <a name="AddNewIndexView"></a>Adicionar uma nova exibição de item

Da mesma forma que você criou uma exibição para itens de lista, crie uma nova exibição para criar itens usando as seguintes etapas:

1. No **Gerenciador de Soluções**, clique com botão direito do mouse na pasta **Item** novamente, selecione **Adicionar**e selecione **Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, atualize os seguintes valores:
   
   * Na caixa **Nome da exibição**, digite ***Criar***.
   * Na caixa **Modelo**, selecione ***Criar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.
   
#### <a name="AddEditIndexView"></a>Adicionar uma exibição Editar item

E, finalmente, adicione um modo de exibição para editar um item com as seguintes etapas:

1. No **Gerenciador de Soluções**, clique com botão direito do mouse na pasta **Item** novamente, selecione **Adicionar**e selecione **Exibição**.

1. Na caixa de diálogo **Adicionar Exibição** , faça o seguinte:
   
   * Na caixa **Nome da exibição**, digite ***Editar***.
   * Na caixa **Modelo**, selecione ***Editar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.

Feito isso, feche todos os documentos cshtml no Visual Studio, já que você voltará a essas exibições mais tarde.

## <a name="connect-to-cosmosdb"></a>Etapa 5: Conectar-se ao Azure Cosmos DB 

Agora que os detalhes padrão do MVC foram resolvidos, vamos adicionar o código para conexão com o Azure Cosmos DB e executar operações CRUD. 

### <a name="perform-crud-operations"></a> Executar operações CRUD nos dados

A primeira coisa a fazer aqui é adicionar uma classe que contenha a lógica para conectar e usar o Azure Cosmos DB. Para este tutorial, encapsularemos essa lógica em uma classe chamada `CosmosDBService` e uma interface chamada `ICosmosDBService`. Esse serviço executa as operações CRUD e de feed de leitura, como listar itens incompletos, criar, editar e excluir os itens. 

1. No **Gerenciador de Soluções**, crie uma nova pasta em seu projeto chamada **Serviços**.

1. Clique com o botão direito do mouse na pasta **Serviços**, selecione **Adicionar** e selecione **Classe**. Nomeie a nova classe **CosmosDBService** e selecione **Adicionar**.

1. Adicione o seguinte código à classe **CosmosDBService** e substitua o código no arquivo pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Repita as etapas 2 e 3, mas, desta vez, para uma classe chamada **ICosmosDBService**, e adicione o seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. O código anterior recebe um `CosmosClient` como parte do construtor. Seguindo o pipeline do ASP.NET Core, precisamos acessar o **Startup.cs** do projeto e inicializar o cliente com base na configuração como uma instância singleton a ser injetada por meio da [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). No manipulador **ConfigureServices**, definimos:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. No mesmo arquivo, definimos nosso método auxiliar **InitializeCosmosClientInstanceAsync**, que lerá a configuração e inicializará o cliente.

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. A configuração é definida no arquivo **appsettings.json** do projeto. Abra-o e adicione uma seção chamada **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Agora se você executar o aplicativo, o pipeline do ASP.NET Core criará uma instância de **CosmosDbService** e manterá uma única instância como singleton; quando o **ItemController** for usado para processar as solicitações do lado do cliente, ele receberá essa única instância e poderá usá-la para executar operações CRUD.

Se você criar e executar esse projeto agora, deverá ver algo parecido com isto:

![Captura de tela do aplicativo Web de lista de tarefas pendentes criado por este tutorial de banco de dados](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Etapa 6: Executar o aplicativo localmente

Use as etapas abaixo para testar o aplicativo em seu computador local:

1. Pressione F5 no Visual Studio para compilar o aplicativo no modo de depuração. Ele deve compilar o aplicativo e iniciar um navegador com a página de grade vazia que vimos anteriormente:
   
   ![Captura de tela do aplicativo Web de lista de tarefas pendentes criado por este tutorial](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Clique no link **Criar Novo** e adicione valores ao campos **Nome** e **Descrição**. Deixe a caixa de seleção **Concluído** desmarcada. Caso contrário, o novo item será adicionado em um estado concluído e não aparecerá na lista inicial.
   
3. Clique em **Criar** para ser redirecionado à exibição **Índice** e ver seu item na lista. Você pode adicionar mais alguns itens à lista de tarefas pendentes.

    ![Captura de tela da exibição Índice](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Clique em **Editar** perto de um **Item** na lista e você será levado para a exibição **Editar**, na qual poderá atualizar qualquer propriedade do objeto, incluindo o sinalizador **Concluído**. Se você marcar o sinalizador **Concluído** e clicar em **Salvar**, o **Item** será exibido como concluído na lista.
   
   ![Captura de tela da exibição Índice com a caixa Concluído marcada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. Verifique a qualquer momento o estado dos dados no serviço Azure Cosmos DB usando o [Cosmos Explorer](https://cosmos.azure.com) ou o Data Explorer do Emulador do Azure Cosmos DB.

6. Depois de testar o aplicativo, pressione Ctrl + F5 para parar a depuração do aplicativo. Você está pronto para implantar!

## <a name="deploy-the-application-to-azure"></a>Etapa 7: Implantar o aplicativo 
Agora que você tem o aplicativo completo funcionando corretamente no Azure Cosmos DB, vamos implantar esse aplicativo Web no Serviço de Aplicativo do Azure.  

1. Para publicar o aplicativo, clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.
   
2. Na caixa de diálogo **Publicar**, selecione **Serviço de Aplicativo** e, em seguida, selecione **Criar** para criar um perfil do Serviço de Aplicativo ou escolha **Selecionar Existente** para usar um perfil existente.

3. Se você tiver um perfil existente do Serviço de Aplicativo do Azure, selecione uma **Assinatura** na lista suspensa. Use o filtro **Exibição** para classificar por tipo de recurso ou grupo de recursos. Em seguida, pesquise o Serviço de Aplicativo do Azure necessário e selecione **OK**.
   
   ![Caixa de diálogo Serviço de Aplicativo no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Para criar um novo perfil do Serviço de Aplicativo do Azure, clique em **Criar Novo** na caixa de diálogo **Publicar**. Na caixa de diálogo **Criar Serviço de Aplicativo**, digite seu nome do aplicativo Web e a assinatura apropriada, o grupo de recursos e o plano de Serviço de Aplicativo e selecione **Criar**.

   ![Caixa de diálogo Criar Serviço de Aplicativo no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Em poucos segundos, o Visual Studio publicará seu aplicativo Web e iniciará um navegador no qual você poderá ver o projeto sendo executado no Azure.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar um aplicativo Web ASP.NET Core MVC que pode acessar os dados armazenados no Azure Cosmos DB. Prossiga agora para o próximo artigo:

* [Saiba mais sobre como particionar seus dados no Azure Cosmos DB](./partitioning-overview.md)
* [Saiba mais sobre como fazer consultas mais avançadas no Azure Cosmos DB](./how-to-sql-query.md)
* [Saiba mais sobre como modelar seus dados em um cenário mais avançado](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
