---
title: 'Tutorial: Compilar um aplicativo Web Java usando o Azure Cosmos DB e a API do SQL'
description: 'Tutorial: Este tutorial de aplicativo Web Java mostra a você como usar o Azure Cosmos DB e a API de SQL para armazenar e acessar dados de um aplicativo Java hospedado nos Sites do Azure.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 489f4aad78437edad2184e773d97d35af24f3249
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285987"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Tutorial: Compilar um aplicativo Web Java usando o Azure Cosmos DB e a API de SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Este tutorial de aplicativo Web Java mostra a você como usar o serviço [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) para armazenar e acessar dados de um aplicativo Java hospedado nos Aplicativos Web do Serviço de Aplicativo do Azure. Neste artigo, você aprenderá:

* Como compilar um aplicativo básico do JSP (JavaServer Pages) no Eclipse.
* Como trabalhar com o serviço Azure Cosmos DB usando o [SDK de Java do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

Este tutorial de aplicativo Java mostra como criar um aplicativo de gerenciamento de tarefas baseado na web que permite criar, recuperar e marcar tarefas como concluídas, conforme mostrado na imagem a seguir. Cada uma das tarefas na lista de tarefas é armazenada como documentos JSON no Azure Cosmos DB.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Aplicativo Java My ToDo List":::

> [!TIP]
> Este tutorial de desenvolvimento de aplicativo presume que você tenha experiência anterior com o Java. Se você não estiver familiarizado com Java ou com as [ferramentas de pré-requisito](#Prerequisites), recomendamos o download completo do projeto [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) do GitHub e compilação dele usando as [instruções no final deste artigo](#GetProject). Depois de compilá-lo, você poderá consultar o artigo para obter informações sobre o código no contexto do projeto.  

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Pré-requisitos para este tutorial de aplicativo Web Java

Antes de começar este tutorial de desenvolvimento de aplicativo, você deve ter:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7 +](/java/azure/jdk/).
* [Eclipse IDE para desenvolvedores de Java EE.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Um site do Azure com um Java runtime environment (por exemplo, Tomcat ou Jetty) habilitado.](../app-service/quickstart-java.md)

Se você estiver instalando essas ferramentas pela primeira vez, o coreservlets.com fornecerá um passo a passo do processo de instalação na seção de início rápido do artigo [Tutorial: Instalar TomCat7 e usá-lo com o Eclipse](https://www.youtube.com/watch?v=jOdCfW7-ybI&t=2s).

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Criar uma conta do Azure Cosmos DB

Vamos começar criando uma conta do Azure Cosmos DB. Se você já tiver uma conta ou se estiver usando o Emulador do Azure Cosmos DB para este tutorial, pule para a [Etapa 2: Criar o aplicativo JSP Java](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Criar o aplicativo JSP Java

Para criar o aplicativo JSP:

1. Primeiro, começaremos criando um projeto Java. Inicie o Eclipse, clique em **Arquivo**, **Novo** e clique em **Projeto Web dinâmico**. Se não vir o **Projeto Web Dinâmico** listado como um projeto disponível, faça o seguinte: clique em **Arquivo**, **Novo**, **Projeto...** , expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="Desenvolvimento de aplicativo Java JSP":::

1. Digite um nome de projeto na caixa **Nome do projeto** e no menu suspenso **Runtime de Destino**, selecione, opcionalmente, um valor (por exemplo, Apache Tomcat v 7.0) e clique em **Concluir**. Selecione um runtime de destino que permite que você execute seu projeto localmente por meio do Eclipse.

1. No Eclipse, na exibição do Explorador de Projeto, expanda o seu projeto. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.

1. Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo como **index.jsp**. Mantenha a pasta pai como **WebContent**, conforme mostrado na ilustração a seguir e clique em **Avançar**.
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Criar um novo arquivo JSP – tutorial de aplicativo Web Java":::

1. Na caixa de diálogo **Selecionar modelo JSP**, selecione esse tutorial **Novo Arquivo JSP (html)** e clique em **Concluir**.

1. Quando o arquivo *index.jsp* for aberto no Eclipse, adicione o texto para exibir **Olá, Mundo!** dentro do elemento existente `<body>`. A atualização `<body>` do conteúdo deve se parecer com o código a seguir:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Salve o arquivo *index.jsp*.

1. Se definir um runtime de destino na etapa 2, você pode clicar no **Projeto** e em **Executar** para executar o aplicativo JSP localmente:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Olá, Mundo – Tutorial de aplicativo Java":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Instalar o SDK Java do SQL

É a maneira mais fácil de obter o SDK do Java do SQL e suas dependências por meio do [Apache Maven](https://maven.apache.org/). Para fazer isso, você precisa converter o projeto em um projeto do Maven usando as etapas a seguir:

1. Clique em seu projeto no Explorador de projeto, clique em **Configurar** e em **Converter em Projeto Maven**.

1. Na janela **Criar novo POM**, aceite os padrões e clique em **Concluir**.

1. No **Explorador de projeto**, abra o arquivo pom.xml.

1. Na guia **Dependências**, no painel **Dependências**, clique em **Adicionar**.

1. Na janela **Selecionar dependência** , faça o seguinte:
   
   * Na caixa **ID do Grupo**, digite `com.azure`.
   * Na caixa **ID do Artefato**, insira `azure-cosmos`.
   * Na caixa **Versão**, insira `4.11.0`.
  
   Ou adicione o XML de dependência da ID do Grupo e da ID do Artefato diretamente ao arquivo *pom.xml*:

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. Clique em **OK** e o Maven instalará o SDK Java do SQL ou salvará o arquivo pom.xml.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Usar o serviço do Azure Cosmos DB em seu aplicativo Java

Agora, vamos adicionar os modelos, as exibições e os controladores ao seu aplicativo Web.

### <a name="add-a-model"></a>Adicionar um modelo

Primeiro, vamos definir um modelo em um novo arquivo *TodoItem.java*. A classe `TodoItem` define o esquema de um item junto com os métodos getter e setter:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Adicionar as classes de DAO (Objeto de acesso a dados)

Crie um DAO (Objeto de acesso a dados) para abstrair mantendo os itens ToDo no Azure Cosmos DB. Para salvar os itens das tarefas em uma coleção, o cliente precisa saber qual banco de dados e coleção manter (como referenciado por self-links). Em geral, é melhor armazenar o banco de dados e a coleção em cache sempre que possível para evitar viagens adicionais ao banco de dados.

1. Para invocar o serviço do Azure Cosmos DB, você precisa criar uma instância de um novo `cosmosClient`. Em geral, é melhor reutilizar o objeto `cosmosClient` em vez de construir um novo cliente para cada solicitação posterior. Você pode reutilizar o cliente definindo-o dentro da classe `cosmosClientFactory`. Atualize os valores de HOST e MASTER_KEY que você salvou na [Etapa 1](#CreateDB). Substitua a variável HOST por pelo URI e substitua a MASTER_KEY pela sua CHAVE PRIMÁRIA. Use o código a seguir para criar a classe `CosmosClientFactory` dentro do arquivo *CosmosClientFactory.java*:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. Crie um arquivo *TodoDao.java* e adicione a classe `TodoDao` para criar, atualizar, ler e excluir os itens de tarefas pendentes:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. Crie um arquivo *MockDao.java* e adicione a classe `MockDao`, essa classe implementa a classe `TodoDao` para executar operações CRUD nos itens:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. Crie um arquivo *DocDbDao.java* e adicione a classe `DocDbDao`. Essa classe define o código para persistir os TodoItems no contêiner, recupera o banco de dados e a coleção, se existir, ou cria um se ele não existe. Este exemplo usa [Gson](https://code.google.com/p/google-gson/) para serializar e desserializar POJOs (TodoItem Plain Old Java Objects) para documentos JSON. Para salvar os itens das tarefas em uma coleção, o cliente precisa saber qual banco de dados e coleção manter (como referenciado por self-links). Essa classe também define a função auxiliar para recuperar os documentos por outro atributo (por exemplo, "ID") em vez de se autovincular. Você pode usar o método auxiliar para recuperar um documento TodoItem JSON pela ID e, em seguida, desserializá-lo para um POJO.

   Você também pode usar o objeto cliente `cosmosClient` para obter uma coleção ou uma lista de TodoItems usando uma consulta SQL. Por fim, você define o método de exclusão para excluir um TodoItem da sua lista. O código a seguir mostra o conteúdo da classe `DocDbDao`:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. Em seguida, crie um arquivo *TodoDaoFactory.java* e adicione a classe `TodoDaoFactory` que cria um objeto DocDbDao:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Adicionar um controlador

Adicione o controlador *TodoItemController* ao seu aplicativo. Neste projeto, você está usando [Project Lombok](https://projectlombok.org/) para gerar o construtor, os getters, os setters e um builder. Como alternativa, você pode escrever esse código manualmente ou o IDE pode gerá-lo:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Criar um servlet

Em seguida, crie um servlet para rotear solicitações HTTP para o controlador. Crie o arquivo *ApiServlet.java* e defina o seguinte código abaixo dele:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Conectar o restante do aplicativo Java

Agora que concluímos a parte divertida, tudo que restou é criar uma interface do usuário rápida e conectá-la ao seu DAO.

1. Você precisará de uma interface do usuário da Web para a exibição ao usuário. Vamos reescrever o *index.jsp* criado anteriormente com o seguinte código:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Finalmente, escreva algum JavaScript do lado do cliente para unir o servlet e a interface do usuário da Web:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Agora tudo o que resta é testar o aplicativo. Executar o aplicativo localmente e adicionar alguns itens de tarefas, preenchendo o nome do item e a categoria e clicando em **Adicionar tarefa**. Quando o item aparecer, você poderá atualizar se ele está concluído alternando a caixa de seleção e clicando em **Atualizar Tarefas**.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Implantar seu aplicativo Java em sites do Azure

Sites do Azure tornam a implantação de aplicativos Java tão simples quanto a exportação de seu aplicativo como um arquivo WAR e por carregamento ou por meio do controle de origem (por exemplo, Git) ou FTP.

1. Para exportar seu aplicativo como um arquivo WAR, clique com o botão direito em seu projeto no **Explorador de Projeto**, clique em **Exportar** e clique em **Arquivo WAR**.

1. Na janela **Exportar WAR** , faça o seguinte:
   
   * Na caixa do projeto Web, insira azure-cosmos-java-sample.
   * Na caixa Destino, escolha um destino para salvar o arquivo WAR.
   * Clique em **Concluir**.

1. Agora que tem um arquivo WAR em mãos, você pode simplesmente carregá-lo no seu diretório **webapps** do site do Azure. Para obter instruções sobre como carregar o arquivo, confira [Adicionar um aplicativo Java aos Aplicativos Web do Serviço de Aplicativo do Azure](../app-service/quickstart-java.md). Uma vez carregado o arquivo WAR na pasta webapps, o ambiente de runtime detectará que você o adicionou e o carregará automaticamente.

1. Para exibir seu produto final, navegue até `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` e comece a adicionar tarefas!

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Obtenha o projeto do GitHub

Todos os exemplos neste tutorial foram incluídos no projeto [tarefas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) no GitHub. Para importar o projeto de tarefas no Eclipse, certifique-se de ter o software e os recursos listados na seção [pré-requisitos](#Prerequisites) e, em seguida, faça o seguinte:

1. Instalar [Project Lombok](https://projectlombok.org/). Lombok é usado para gerar construtores, getters e setters no projeto. Depois que você baixou o arquivo lombok.jar, clique duas vezes nele para instalá-lo ou instalá-lo a partir da linha de comando.

1. Se o Eclipse estiver aberto, feche-o e reinicie-o para carregar o Lombok.

1. No Eclipse, no menu **Arquivo**, clique em **Importar**.

1. Na janela **Importar**, clique em **Git**, **Projetos do Git** e clique em **Avançar**.

1. Na tela **Selecionar origem de repositório**, clique em **Clonar URI**.

1. Na tela **Repositório Git de Origem**, na caixa **URI**, digite https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app e, em seguida, clique em **Avançar**.

1. Na tela **Seleção de Branch**, verifique se **main** está selecionado e clique em **Avançar**.

1. Na tela **Destino Local**, clique em **Procurar** para selecionar uma pasta onde o repositório possa ser copiado e clique em **Avançar**.

1. Na tela **Selecionar um assistente a ser usado para importar projetos**, verifique se **Importar projetos existentes** está selecionado e clique em **Avançar**.

1. Na tela **Importar projetos**, desmarque o projeto do **DocumentDB** e clique em **Concluir**. O projeto DocumentDB contém o SDK Java do Azure Cosmos DB, que adicionaremos como uma dependência em seu lugar.

1. No **Explorador de Projeto**, navegue para azure-cosmos-java-sample\src\com.microsoft.azure.cosmos.sample.dao\DocumentClientFactory.java e substitua os valores HOST e MASTER_KEY pela URI e a CHAVE PRIMÁRIA da sua conta do Azure Cosmos DB e salve o arquivo. Para obter mais informações, consulte a [Etapa 1. Criar uma conta de banco de dados do Azure Cosmos](#CreateDB).

1. Em **Explorador de Projeto**, clique com o botão direito do mouse em **azure-cosmos-java-sample**, clique em **Caminho de Build** e clique em **Configurar Caminho de Build**.

1. Na tela **Caminho de Build Java**, no painel direito, selecione a guia **Bibliotecas** e clique em **Adicionar JARs Externos**. Navegue até o local do arquivo lombok.jar e clique em **Abrir** e clique em **OK**.

1. Use a Etapa 12 para abrir a janela **Propriedades** novamente e, no painel esquerdo, clique em **Runtimes Direcionados**.

1. Na tela **Runtimes Direcionados**, clique em **Novo**, selecione **Apache Tomcat v7.0** e clique em **OK**.

1. Use a Etapa 12 para abrir a janela **Propriedades** novamente e, no painel esquerdo, clique em **Facetas do Projeto**.

1. Na tela **Facetas do Projeto**, selecione **Módulo da Web Dinâmico** e **Java** e clique em **OK**.

1. Na guia **Servidores** na parte inferior da tela, clique com o botão direito do mouse em **Servidor Tomcat v7.0 no localhost** e clique em **Adicionar e Remover**.

1. Na janela **Adicionar e Remover**, mova **azure-cosmos-java-sample** para a caixa **Configurado** e clique em **Concluir**.

1. Na guia **Servidores**, clique com o botão direito do mouse em **Servidor Tomcat v7.0 no localhost** e clique em **Reiniciar**.

1. Em um navegador, navegue até `http://localhost:8080/azure-cosmos-java-sample/` e comece a adicionar à sua lista de tarefas. Observe que, se você alterou os valores da porta padrão, altere a 8080 para o valor selecionado.

1. Para implantar o projeto em um site do Azure, consulte a [Etapa 6. Implante o aplicativo nos Sites do Azure](#Deploy).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Compilar um aplicativo node.js com o Azure Cosmos DB](sql-api-nodejs-application.md)
