---
title: Tutorial de aplicativo Web MVC do ASP.NET Core usando Azure Cosmos DB
description: Tutorial do ASP.NET Core MVC para criar um aplicativo Web MVC usando o Azure Cosmos DB. Você armazenará o JSON e acessará dados de um aplicativo de tarefas pendentes hospedado no Serviço de Aplicativo do Azure ‒ tutorial passo a passo do ASP.NET Core MVC.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 528cab915a1ac3918146e428e9ae6b3c401324c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010326"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: Desenvolver um aplicativo Web ASP.NET Core MVC com o Azure Cosmos DB usando o SDK do .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Este tutorial mostra a você como usar o Azure Cosmos DB para armazenar e acessar dados de um aplicativo Web ASP.NET MVC hospedado no Azure. Neste tutorial, você usará o SDK do .NET V3. A imagem abaixo mostra a página da Web que você criará usando o exemplo neste artigo:

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="Captura de tela do aplicativo Web MVC de tarefas pendentes criado por este tutorial – tutorial passo a passo do ASP.NET Core MVC":::

Se você não tiver tempo para concluir o tutorial, baixe o projeto de exemplo completo no [GitHub][GitHub].

Este tutorial abrange:

> [!div class="checklist"]
>
> * Como criar uma conta do Azure Cosmos
> * Como criar um aplicativo ASP.NET Core MVC
> * Como conectar o aplicativo ao Azure Cosmos DB
> * Executando operações CRUD (criar, ler, atualizar e excluir) nos dados

> [!TIP]
> Este tutorial pressupõe que você tenha experiência anterior com o uso do ASP.NET Core MVC e o Serviço de Aplicativo do Azure. Caso esteja conhecendo agora o ASP.NET Core ou as [ferramentas de pré-requisito](#prerequisites), recomendamos que você baixe o projeto de exemplo completo no [GitHub][GitHub], adicione os pacotes NuGet necessários e execute-o. Depois de compilar o projeto, você poderá consultar esse artigo para obter insights sobre o código no contexto do projeto.

## <a name="prerequisites"></a><a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes recursos:

* Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Todas as capturas de tela deste artigo são do Microsoft Visual Studio Community 2019. Se você usar uma versão diferente, suas telas e opções poderão não coincidir totalmente. A solução deverá funcionar se você atender aos pré-requisitos.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Etapa 1: Criar uma conta do Azure Cosmos

Vamos começar criando uma conta do Azure Cosmos. Caso já tenha uma conta da API do SQL do Azure Cosmos DB ou esteja usando o Emulador do Azure Cosmos DB, passe para a [Etapa 2: Criar um novo aplicativo MCV do ASP.NET](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Na próxima seção, você criará um aplicativo ASP.NET Core MVC.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Etapa 2: Criar um aplicativo ASP.NET Core MVC

1. Abra o Visual Studio e selecione **Criar um projeto**.

1. Em **Criar novo projeto**, localize e selecione **Aplicativo Web ASP.NET Core** para C#. Selecione **Avançar** para continuar.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="Criar um projeto de aplicativo Web ASP.NET Core":::

1. Em **Configurar seu novo projeto**, nomeie o projeto *todo* e selecione **Criar**.

1. Em **Criar um novo Aplicativo Web ASP.NET Core**, escolha **Aplicativo Web (Model-View-Controller)** . Selecione **Criar** para continuar.

   O Visual Studio cria um aplicativo MVC vazio.

1. Selecione **Depurar** > **Iniciar Depuração** ou pressione F5 para executar o aplicativo ASP.NET localmente.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Etapa 3: Adicionar o pacote NuGet do Azure Cosmos DB ao projeto

Agora que temos a maior parte do código da estrutura ASP.NET Core MVC de que precisamos para esta solução, vamos adicionar os pacotes NuGet necessários para a conexão com o Azure Cosmos DB.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**.

1. No **Gerenciador de Pacotes NuGet**, pesquise e selecione **Microsoft.Azure.Cosmos**. Selecione **Instalar**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="Instalar pacote NuGet":::

   O Visual Studio baixa e instala o pacote do Azure Cosmos DB e suas dependências.

   Também é possível usar o **Console do Gerenciador de Pacotes** para instalar o pacote NuGet. Para fazer isso, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**. No prompt, digite o seguinte comando:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Etapa 4: Configurar o aplicativo ASP.NET Core MVC

Agora, vamos adicionar os modelos, as exibições e os controladores ao aplicativo MVC.

### <a name="add-a-model"></a><a name="add-a-model"></a> Adicionar um modelo

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modelos**, selecione **Adicionar** > **Classe**.

1. Em **Adicionar Novo Item**, nomeie a nova classe *Item.cs* e selecione **Adicionar**.

1. Substitua o conteúdo da classe *Item.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

O Azure Cosmos DB usa JSON para mover e armazenar dados. Você pode usar o atributo `JsonProperty` para controlar como o JSON serializa e desserializa objetos. A classe `Item` demonstra o atributo `JsonProperty`. Esse código controla o formato do nome da propriedade recebida pelo JSON. Ele também renomeia a propriedade do .NET `Completed`.

### <a name="add-views"></a><a name="add-views"></a>Adicionar exibições

Em seguida, vamos adicionar as exibições a seguir.

* Uma exibição de criação de item
* Uma exibição de exclusão de item
* Uma exibição para obter detalhes de um item
* Uma exibição de edição de item
* Uma exibição para listar todos os itens

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>Exibição de criação de item

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Exibições** e selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta *Item*.

1. Clique com o botão direito do mouse na pasta vazia **Item** e, em seguida, selecione **Adicionar** > **Exibição**.

1. Em **Adicionar Exibição do MVC**, faça as seguintes alterações:

   * Em **Nome da exibição**, insira *Criar*.
   * Em **Modelo**, selecione **Criar**.
   * Em **Classe do modelo**, selecione **Item (todo.Models)** .
   * Selecione **Usar uma página de layout** e insira *~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="Captura de tela mostrando a caixa de diálogo Adicionar Exibição do MVC":::

1. Em seguida, selecione **Adicionar** e deixe o Visual Studio criar uma exibição de modelo. Substitua o código no arquivo gerado pelo seguinte conteúdo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>Exibição de exclusão de item

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Item** novamente, selecione **Adicionar** > **Exibição**.

1. Em **Adicionar Exibição do MVC**, faça as seguintes alterações:

   * Na caixa **Nome da exibição**, digite *Excluir*.
   * Na caixa **Modelo**, selecione **Excluir**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)** .
   * Selecione **Usar uma página de layout** e insira *~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

1. Em seguida, selecione **Adicionar** e deixe o Visual Studio criar uma exibição de modelo. Substitua o código no arquivo gerado pelo seguinte conteúdo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>Adicionar uma exibição para obter detalhes de um item

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Item** novamente, selecione **Adicionar** > **Exibição**.

1. Em **Adicionar Exibição do MVC**, forneça os seguintes valores:

   * Em **Nome da exibição**, insira *Detalhes*.
   * Em **Modelo**, selecione **Detalhes**.
   * Em **Classe do modelo**, selecione **Item (todo.Models)** .
   * Selecione **Usar uma página de layout** e insira *~/Views/Shared/_Layout.cshtml*.

1. Em seguida, selecione **Adicionar** e deixe o Visual Studio criar uma exibição de modelo. Substitua o código no arquivo gerado pelo seguinte conteúdo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Adicionar uma exibição Editar item

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Item** novamente, selecione **Adicionar** > **Exibição**.

1. Em **Adicionar Exibição do MVC**, faça as seguintes alterações:

   * Na caixa **Nome da exibição**, digite *Editar*.
   * Na caixa **Modelo**, selecione **Editar**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)** .
   * Selecione **Usar uma página de layout** e insira *~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

1. Em seguida, selecione **Adicionar** e deixe o Visual Studio criar uma exibição de modelo. Substitua o código no arquivo gerado pelo seguinte conteúdo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>Adicionar uma exibição para listar todos os itens

E, finalmente, adicione uma exibição para obter todos os itens com as seguintes etapas:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Item** novamente, selecione **Adicionar** > **Exibição**.

1. Em **Adicionar Exibição do MVC**, faça as seguintes alterações:

   * Na caixa **Nome da exibição**, digite *Índice*.
   * Na caixa **Modelo**, selecione **Lista**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)** .
   * Selecione **Usar uma página de layout** e insira *~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

1. Em seguida, selecione **Adicionar** e deixe o Visual Studio criar uma exibição de modelo. Substitua o código no arquivo gerado pelo seguinte conteúdo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

Após concluir essas etapas, feche todos os documentos *cshtml* no Visual Studio.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Declarar e inicializar serviços

Primeiro, adicionaremos uma classe que contenha a lógica para conectar e usar o Azure Cosmos DB. Para este tutorial, encapsularemos essa lógica em uma classe chamada `CosmosDbService` e uma interface chamada `ICosmosDbService`. Esse serviço executa as operações CRUD. Ele também executa operações de leitura de feed, como listar itens incompletos, criar, editar e excluir os itens.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta *Serviços*.

1. Clique com o botão direito do mouse na pasta **Serviços**, selecione **Adicionar** > **Classe**. Nomeie a nova classe *CosmosDbService* e selecione **Adicionar**.

1. Substitua o conteúdo de *CosmosDbService.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Clique com o botão direito do mouse na pasta **Serviços**, selecione **Adicionar** > **Classe**. Nomeie a nova classe *ICosmosDbService* e selecione **Adicionar**.

1. Adicione o seguinte código à classe *ICosmosDbService*:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Abra o arquivo *Startup.cs* em sua solução e adicione o método a seguir **InitializeCosmosClientInstanceAsync**, que lê a configuração e inicializa o cliente.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. No mesmo arquivo, substitua o método `ConfigureServices` por:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   O código nessa etapa inicializa o cliente com base na configuração como uma instância singleton a ser injetada por meio da [Injeção de dependência no ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).

   Altere o controlador MVC padrão para `Item` editando as rotas no método `Configure` do mesmo arquivo:

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Defina a configuração no arquivo *appsettings.json* do projeto, conforme mostrado no seguinte snippet:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Adicionar um controlador

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Controladores**, selecione **Adicionar** > **Controlador**.

1. Em **Adicionar Scaffold**, selecione **Controlador MVC – Vazio** e selecione **Adicionar**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="Selecione Controlador MVC – Vazio em Adicionar Scaffold":::

1. Nomeie o novo controlador *ItemController*.

1. Substitua o conteúdo de *ItemController.cs* pelo código a seguir:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

O atributo **ValidateAntiForgeryToken** é usado aqui para ajudar a proteger esse aplicativo contra ataques de solicitação intersite forjada. Suas exibições também devem funcionar com esse token antifalsificação. Para obter mais informações e exemplos, confira [Evitando ataques de CSRF (solicitação intersite forjada) em aplicativos MVC do ASP.NET][Preventing Cross-Site Request Forgery]. O código-fonte fornecido no [GitHub][GitHub] tem a implementação completa estabelecida.

Também usamos o atributo **Bind** no parâmetro de método para ajudar na proteção contra ataques overposting. Para saber mais, confira [Tutorial: Implementar a funcionalidade CRUD com o Entity Framework no MVC do ASP.NET][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Etapa 5: Executar o aplicativo localmente

Use as etapas abaixo para testar o aplicativo em seu computador local:

1. Pressione F5 no Visual Studio para compilar o aplicativo no modo de depuração. Ele deve compilar o aplicativo e iniciar um navegador com a página de grade vazia que vimos anteriormente:

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="Captura de tela do aplicativo Web de lista de tarefas pendentes criado por este tutorial":::
   
   Se o aplicativo for aberto na Página Inicial, acrescente `/Item` à URL.

1. Selecione o link **Criar Novo** e adicione valores ao campos **Nome** e **Descrição**. Deixe a caixa de seleção **Concluído** desmarcada. Se você selecioná-la, o aplicativo adicionará o novo item no estado concluído. O item não aparece mais na lista inicial.

1. Selecione **Criar**. O aplicativo envia você de volta para a exibição **Índice** e seu item aparece na lista. Você pode adicionar mais alguns itens à lista de **Tarefas Pendentes**.

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="Captura de tela da exibição Índice":::
  
1. Selecione **Editar** ao lado de um **Item** na lista. O aplicativo abre a exibição **Editar**, na qual pode atualizar qualquer propriedade do objeto, incluindo o sinalizador **Concluído**. Se você selecionar **Concluído** e selecionar **Salvar**, o aplicativo exibirá o **Item** como concluído na lista.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="Captura de tela da exibição Índice com a caixa Concluído marcada":::

1. Verifique o estado dos dados no serviço Azure Cosmos DB usando o [Cosmos Explorer](https://cosmos.azure.com) ou o Data Explorer do Emulador do Azure Cosmos DB.

1. Depois de testar o aplicativo, pressione Ctrl + F5 para parar a depuração do aplicativo. Você está pronto para implantar!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Etapa 6: Implantar o aplicativo

Agora que você tem o aplicativo completo funcionando corretamente no Azure Cosmos DB, vamos implantar esse aplicativo Web no Serviço de Aplicativo do Azure.  

1. Para publicar o aplicativo, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.

1. Em **Escolher um destino de publicação**, selecione o **Serviço de Aplicativo**.

1. Para usar um perfil existente do Serviço de Aplicativo, escolha **Selecionar existente** e, em seguida, selecione **Publicar**.

1. No **Serviço de Aplicativo**, selecione uma **Assinatura**. Use o filtro **Exibição** para classificar por tipo de recurso ou grupo de recursos.

1. Localize o perfil e, em seguida, selecione **OK**. Em seguida, pesquise o Serviço de Aplicativo do Azure necessário e selecione **OK**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="Caixa de diálogo Serviço de Aplicativo no Visual Studio":::

Outra opção é criar um novo perfil:

1. Assim como no procedimento anterior, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.
  
1. Em **Escolher um destino de publicação**, selecione o **Serviço de Aplicativo**.

1. Em **Escolher um destino de publicação**, selecione **Criar Novo** e selecione **Publicar**.

1. No **Serviço de Aplicativo**, digite o nome do aplicativo Web e a assinatura apropriada, o grupo de recursos, o plano de hospedagem e, em seguida, selecione **Criar**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="Caixa de diálogo Criar Serviço de Aplicativo no Visual Studio":::

Em poucos segundos, o Visual Studio publicará seu aplicativo Web e iniciará um navegador no qual você poderá ver o projeto sendo executado no Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um aplicativo Web MVC do ASP.NET Core. Seu aplicativo pode acessar dados armazenados no Azure Cosmos DB. Agora, você pode continuar com estes recursos:

* [Particionamento no Azure Cosmos DB](./partitioning-overview.md)
* [Guia de Introdução a consultas SQL](./sql-query-getting-started.md)
* [Como modelar e particionar dados no Azure Cosmos DB usando um exemplo do mundo real](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: /aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: /aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
