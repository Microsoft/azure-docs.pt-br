---
title: Tutorial de desenvolvimento de aplicativo Java usando o Azure Cosmos DB | Microsoft Docs
description: "Este tutorial de aplicativo Web Java mostra a você como usar o Azure Cosmos DB e a API de DocumentDB para armazenar e acessar dados de um aplicativo Java hospedado nos Sites do Azure."
keywords: Desenvolvimento de aplicativos, tutorial de banco de dados, aplicativo java, tutorial do aplicativo web java, banco de dados de documentos, azure, Microsoft azure
services: cosmos-db
documentationcenter: java
author: dennyglee
manager: jhubbard
editor: mimig
ms.assetid: 0867a4a2-4bf5-4898-a1f4-44e3868f8725
ms.service: cosmos-db
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/22/2017
ms.author: denlee
ms.openlocfilehash: 7b1053a7ec879294cb2240c9d6b4cd32f923ef9c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-java-web-application-using-azure-cosmos-db-and-the-documentdb-api"></a>Compilar um aplicativo Web Java usando o Azure Cosmos DB e a API de DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Este tutorial de aplicativo Web Java mostra a você como usar o serviço [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) para armazenar e acessar dados de um aplicativo Java hospedado nos Aplicativos Web do Serviço de Aplicativo do Azure. Neste tópico, você aprenderá:

* Como compilar um aplicativo básico do JSP (JavaServer Pages) no Eclipse.
* Como trabalhar com o serviço Azure Cosmos DB usando o [SDK de Java do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

Este tutorial de aplicativo Java mostra como criar um aplicativo de gerenciamento de tarefas baseado na web que permite criar, recuperar e marcar tarefas como concluídas, conforme mostrado na imagem a seguir. Cada uma das tarefas na lista de tarefas é armazenada como documentos JSON no Azure Cosmos DB.

![Aplicativo Java Minha lista de tarefas pendentes](./media/documentdb-java-application/image1.png)

> [!TIP]
> Este tutorial de desenvolvimento de aplicativo presume que você tenha experiência anterior com o Java. Se você não estiver familiarizado com Java ou com as [ferramentas de pré-requisito](#Prerequisites), recomendamos o download completo do projeto [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) do GitHub e compilação dele usando as [instruções no final deste artigo](#GetProject). Depois de compilá-lo, você poderá consultar o artigo para obter informações sobre o código no contexto do projeto.  
> 
> 

## <a id="Prerequisites"></a>Pré-requisitos para este tutorial de aplicativo Web Java
Antes de começar este tutorial de desenvolvimento de aplicativo, você deve ter:

*  Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Eclipse IDE para desenvolvedores de Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
* [Um site do Azure com um Java runtime environment (por exemplo, Tomcat ou Jetty) habilitado.](../app-service/app-service-web-get-started-java.md)

Se você estiver instalando essas ferramentas pela primeira vez, o coreservlets.com fornecerá um passo a passo do processo de instalação na seção de Início rápido do artigo [Tutorial: Instalar TomCat7 e usá-lo com o Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) .

## <a id="CreateDB"></a>Etapa 1: Criar uma conta de banco de dados do Azure Cosmos DB
Vamos começar criando uma conta do Azure Cosmos DB. Se você já tiver uma conta ou se estiver usando o Emulador do Azure Cosmos DB para este tutorial, pule para a [Etapa 2: Criar um novo aplicativo do Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a id="CreateJSP"></a>Etapa 2: criar o aplicativo JSP Java
Para criar o aplicativo JSP:

1. Primeiro, começaremos criando um projeto Java. Inicie o Eclipse, clique em **Arquivo**, **Novo** e clique em **Projeto Web dinâmico**. Se você não vir o **Projeto Web Dinâmico** listado como um projeto disponível, faça o seguinte: clique em **Arquivo**, **Novo**, **Projeto...**, expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**.
   
    ![Desenvolvimento de aplicativo Java JSP](./media/documentdb-java-application/image10.png)
2. Digite um nome de projeto na caixa **Nome do projeto** e no menu suspenso **Tempo de Execução de Destino**, selecione, opcionalmente, um valor (por exemplo, Apache Tomcat v 7.0) e clique em **Concluir**. Selecione um tempo de execução de destino que permite que você execute seu projeto localmente por meio do Eclipse.
3. No Eclipse, na exibição do Explorador de Projeto, expanda o seu projeto. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.
4. Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo como **index.jsp**. Mantenha a pasta pai como **WebContent**, conforme mostrado na ilustração a seguir e clique em **Avançar**.
   
    ![Criar um novo arquivo JSP — tutorial de aplicativo Web Java](./media/documentdb-java-application/image11.png)
5. Na caixa de diálogo **Selecionar modelo JSP**, selecione esse tutorial **Novo Arquivo JSP (html)** e clique em **Concluir**.
6. Quando o arquivo index.jsp for aberto no Eclipse, adicione o texto para exibir **Hello World!** dentro do elemento existente <body>. A atualização <body> do conteúdo deve se parecer com o código a seguir:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. Salve o arquivo index.jsp.
8. Se definir um tempo de execução de destino na etapa 2, você pode clicar no **Projeto** e em **Executar** para executar o aplicativo JSP localmente:
   
    ![Hello World — tutorial de aplicativo Java](./media/documentdb-java-application/image12.png)

## <a id="InstallSDK"></a>Etapa 3: Instalar o SDK do Java do DocumentDB
É a maneira mais fácil de obter o SDK do Java do DocumentDB e suas dependências por meio do [Apache Maven](http://maven.apache.org/).

Para fazer isso, você precisará converter o projeto para um projeto Maven concluindo as etapas a seguir:

1. Clique em seu projeto no Explorador de projeto, clique em **Configurar** e em **Converter em Projeto Maven**.
2. Na janela **Criar novo POM**, aceite os padrões e clique em **Concluir**.
3. No **Explorador de projeto**, abra o arquivo pom.xml.
4. Na guia **Dependências**, no painel **Dependências**, clique em **Adicionar**.
5. Na janela **Selecionar dependência** , faça o seguinte:
   
   * Na caixa **Id do Grupo**, insira com.microsoft.azure.
   * Na caixa **Id do Artefato**, insira azure-documentdb.
   * Na caixa **Versão**, insira 1.5.1.
     
   ![Instalar o SDK do aplicativo Java para DocumentDB](./media/documentdb-java-application/image13.png)
     
   * Ou adicione a dependência de XML para a ID do Grupo e ID do Artefato diretamente no pom.xml por meio de um editor de texto:
     
        <dependency> <groupId>com.microsoft.azure</groupId> <artifactId>azure-documentdb</artifactId> <version>1.9.1</version> </dependency>
6. Clique em **OK** e o Maven instalará o SDK do Java do DocumentDB.
7. Salve o arquivo pom.xml.

## <a id="UseService"></a>Etapa 4: Usar o serviço do Azure Cosmos DB em um aplicativo Java
1. Primeiro, vamos definir o objeto TodoItem no TodoItem.java:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    Neste projeto, estamos usando [Project Lombok](http://projectlombok.org/) para gerar o construtor, os getters, os setters e um builder. Como alternativa, você pode escrever esse código manualmente ou o IDE pode gerá-lo.
2. Para invocar o serviço Azure Cosmos DB, você deve criar um novo **DocumentClient**. Em geral, é melhor reutilizar o **DocumentClient** - em vez de construir um novo cliente para cada solicitação subsequente. O cliente pode ser reutilizado envolvendo o cliente em uma **DocumentClientFactory**. No DocumentClientFactory.java, você precisa colar o valor da URI e a CHAVE PRIMÁRIA salva na área de transferência na [etapa 1](#CreateDB). Substitua [SEU\_PONTODEEXTREMIDADE\_AQUI] pelo seu URI e substitua [SUA\_CHAVE\_AQUI] pela sua chave primária.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Agora, vamos criar um objeto de acesso de dados (DAO) para abstrair mantendo os itens ToDo no Azure Cosmos DB.
   
    Para salvar os itens das tarefas em uma coleção, o cliente precisa saber qual banco de dados e coleção manter (como referenciado por self-links). Em geral, é melhor armazenar o banco de dados e a coleção em cache sempre que possível para evitar viagens adicionais ao banco de dados.
   
    O código a seguir ilustra como recuperar nosso Banco de dados e Coleção, se existir, ou criar um novo se ela não existir:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The Azure Cosmos DB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. A próxima etapa é escrever algum código para manter as TodoItems na coleção. Neste exemplo, usaremos [Gson](https://code.google.com/p/google-gson/) para serializar e desserializar TodoItem Plain Old Java Objects (POJOs) para documentos JSON.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Assim como os bancos de dados e coleções do Azure Cosmos DB, os documentos também são referenciados por self-links. A função de auxiliar a seguir nos permite recuperar documentos por outro atributo (por exemplo, "id") em vez de self-links:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Podemos usar o método auxiliar na etapa 5 para recuperar um documento TodoItem JSON pela ID e, em seguida, desserializá-lo para um POJO:
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. Também podemos usar o DocumentClient para obter uma coleção ou uma lista de TodoItems usando o SQL do DocumentDB:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. Há muitas maneiras de atualizar um documento com o DocumentClient. Em nosso aplicativo de lista Todo, queremos poder ativar ou desativar um TodoItem ele for concluído. Isso pode ser feito atualizando o atributo "concluído" dentro do documento:
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Por fim, queremos ter a capacidade de excluir um TodoItem de nossa lista. Para fazer isso, podemos usar o método auxiliar que escrevemos antes para recuperar self links e depois dizer ao cliente para excluí-lo:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // Azure Cosmos DB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>Etapa 5: Conectando por fio o restante do projeto de desenvolvimento de aplicativo Java
Agora que concluímos a parte divertida - tudo que restou é criar uma interface de usuário rápida e conectá-la ao nosso DAO.

1. Primeiro, vamos começar com a criação de um Controlador para chamar nosso DAO:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    Em um aplicativo mais complexo, o Controlador pode ter uma lógica comercial complicada na parte superior do DAO.
2. Em seguida, criaremos um Servlet para rotear solicitações HTTP para o Controlador:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Precisaremos de uma interface do usuário da Web para a exibição ao usuário. Vamos reescrever o ndex.jsp criado anteriormente:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure Cosmos DB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. E, finalmente, escrever um JavaScript do lado do cliente para unir o servlet e a interface do usuário da web:
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. Incrível! Agora tudo o que resta é testar o aplicativo. Executar o aplicativo localmente e adicionar alguns itens de tarefas, preenchendo o nome do item e a categoria e clicando em **Adicionar tarefa**.
6. Quando o item aparecer, você poderá atualizar se ele está concluído alternando a caixa de seleção e clicando em **Atualizar Tarefas**.

## <a id="Deploy"></a>Etapa 6: implantar seu aplicativo Java em sites do Azure
Sites do Azure tornam a implantação de aplicativos Java tão simples quanto a exportação de seu aplicativo como um arquivo WAR e por carregamento ou por meio do controle de origem (por exemplo, Git) ou FTP.

1. Para exportar seu aplicativo como um arquivo WAR, clique com o botão direito em seu projeto no **Explorador de Projeto**, clique em **Exportar** e clique em **Arquivo WAR**.
2. Na janela **Exportar WAR** , faça o seguinte:
   
   * Na caixa de projeto da Web, insira azure-documentdb-java-sample.
   * Na caixa Destino, escolha um destino para salvar o arquivo WAR.
   * Clique em **Concluir**.
3. Agora que tem um arquivo WAR em mãos, você pode simplesmente carregá-lo no seu diretório **webapps** do site do Azure. Para obter instruções sobre como carregar o arquivo, confira [Adicionar um aplicativo Java aos Aplicativos Web do Serviço de Aplicativo do Azure](../app-service/web-sites-java-add-app.md).
   
    Uma vez carregado o arquivo WAR na pasta webapps, o ambiente de tempo de execução irá detectar que você o adicionou e o carregará automaticamente.
4. Para exibir seu produto acabado, navegue para http://SEU\_NOME\_SITE.azurewebsites.net/azure-java-sample/ e comece a adicionar suas tarefas!

## <a id="GetProject"></a>Obtenha o projeto do GitHub
Todos os exemplos neste tutorial foram incluídos no projeto [tarefas](https://github.com/Azure-Samples/documentdb-java-todo-app) no GitHub. Para importar o projeto de tarefas no Eclipse, certifique-se de ter o software e os recursos listados na seção [pré-requisitos](#Prerequisites) e, em seguida, faça o seguinte:

1. Instalar [Project Lombok](http://projectlombok.org/). Lombok é usado para gerar construtores, getters e setters no projeto. Depois que você baixou o arquivo lombok.jar, clique duas vezes nele para instalá-lo ou instalá-lo a partir da linha de comando.
2. Se o Eclipse estiver aberto, feche-o e reinicie-o para carregar o Lombok.
3. No Eclipse, no menu **Arquivo**, clique em **Importar**.
4. Na janela **Importar**, clique em **Git**, **Projetos do Git** e clique em **Avançar**.
5. Na tela **Selecionar origem de repositório**, clique em **Clonar URI**.
6. Na tela **Repositório de Origem do Git**, na caixa **URI**, insira https://github.com/Azure-Samples/java-todo-app.git e clique em **Avançar**.
7. Na tela **Seleção de Ramificação**, verifique se **master** está selecionado e clique em **Avançar**.
8. Na tela **Destino Local**, clique em **Procurar** para selecionar uma pasta onde o repositório possa ser copiado e clique em **Avançar**.
9. Na tela **Selecionar um assistente a ser usado para importar projetos**, verifique se **Importar projetos existentes** está selecionado e clique em **Avançar**.
10. Na tela **Importar projetos**, desmarque o projeto do **DocumentDB** e clique em **Concluir**. O projeto DocumentDB contém o SDK Java do Azure Cosmos DB, que adicionaremos como uma dependência em seu lugar.
11. No **Gerenciador de Projetos**, navegue para azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java e substitua os valores HOST e MASTER_KEY pela URI e a CHAVE PRIMÁRIA de sua conta BD Cosmos do Azure, então, salve o arquivo. Para obter mais informações, consulte a [Etapa 1. Crie uma conta do banco de dados BD Cosmos do Azure](#CreateDB).
12. Em **Explorador de Projeto**, clique com o botão direito do mouse em **azure-documentdb-java-sample**, clique em **Caminho de Build** e clique em **Configurar Caminho de Build**.
13. Na tela **Caminho de Build Java**, no painel direito, selecione a guia **Bibliotecas** e clique em **Adicionar JARs Externos**. Navegue até o local do arquivo lombok.jar e clique em **Abrir** e clique em **OK**.
14. Use a Etapa 12 para abrir a janela **Propriedades** novamente e, no painel esquerdo, clique em **Tempos de Execução Direcionados**.
15. Na tela **Tempos de Execução Direcionados**, clique em **Novo**, selecione **Apache Tomcat v7.0** e clique em **OK**.
16. Use a Etapa 12 para abrir a janela **Propriedades** novamente e, no painel esquerdo, clique em **Facetas do Projeto**.
17. Na tela **Facetas do Projeto**, selecione **Módulo da Web Dinâmico** e **Java** e clique em **OK**.
18. Na guia **Servidores** na parte inferior da tela, clique com o botão direito do mouse em **Servidor Tomcat v7.0 no localhost** e clique em **Adicionar e Remover**.
19. Na janela **Adicionar e Remover**, mova **azure-documentdb-java-sample** para a caixa **Configurado** e clique em **Concluir**.
20. Na guia **Servidores**, clique com o botão direito do mouse em **Servidor Tomcat v7.0 no localhost** e clique em **Reiniciar**.
21. Em um navegador, navegue para http://localhost:8080/azure-documentdb-java-sample/ e comece a adicionar sua lista de tarefas. Observe que, se você alterou os valores da porta padrão, altere a 8080 para o valor selecionado.
22. Para implantar o projeto em um site do Azure, consulte a [Etapa 6. Implante o aplicativo nos Sites do Azure](#Deploy).

[1]: media/documentdb-java-application/keys.png
