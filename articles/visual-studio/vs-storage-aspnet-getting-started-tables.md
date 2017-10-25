---
title: "Introdução ao Armazenamento de Tabelas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET) | Microsoft Docs"
description: "Como começar a usar o Armazenamento de Tabelas do Azure em um projeto do ASP.NET no Visual Studio após a conexão a uma conta de armazenamento usando os Serviços Conectados do Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraigb
ms.openlocfilehash: 32a57e77bf6fe3cff88b9d6772ede9e6669ec75f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao Armazenamento de Tabelas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

O serviço de armazenamento de Tabela do Azure permite que você armazene grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Este tutorial mostra como gravar código ASP.NET para alguns cenários comuns usando entidades do Armazenamento de Tabelas do Azure. Os cenários abordados incluem a criação de tabela e a adição, consulta e exclusão de entidades de tabela. 

##<a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores** e, no menu de contexto, selecione **Adicionar-> Controlador**.

    ![Adicionar um controlador a um aplicativo ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Na caixa de diálogo **Adicionar Scaffold**, clique em **Controlador MVC 5 – Vazio** e selecione **Adicionar**.

    ![Especificar o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Na caixa de diálogo **Adicionar Controlador**, nomeie o controlador *TablesController* e selecione **Adicionar**.

    ![Dê um nome ao controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adicione as seguintes diretivas *using* ao arquivo `TablesController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Criar uma classe de modelo

Muitos dos exemplos neste artigo usam uma classe derivada de **TableEntity** chamada **CustomerEntity**. As etapas a seguir o orientarão pela declaração desta classe como uma classe de modelo:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Modelos** e, no menu de contexto, selecione **Adicionar->Classe**.

1. Na caixa de diálogo **Adicionar Novo Item**, nomeie a classe como **CustomerEntity**.

1. Abra o arquivo `CustomerEntity.cs` e adicione a seguinte diretiva **using**:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Modifique a classe de modo que, quando terminar, ela será declarada como no código a seguir. A classe é declarada em uma classe de entidade chamada **CustomerEntity** que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Criar uma tabela

As etapas a seguir ilustram como criar uma tabela:

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `TablesController.cs` .

1. Adicione um método chamado **CreateTable** que retorna um **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **CreateTable**, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que representa uma referência ao nome da tabela desejada. O método **CloudTableClient.GetTableReference** não faz uma solicitação para o Armazenamento de Tabelas. A referência é retornada mesmo se a tabela não existir. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Chame o método **CloudTable.CreateIfNotExists** para criar a tabela se ela ainda não existir. O método **CloudTable.CreateIfNotExists** retorna **true** se a tabela não existir e for criada com êxito. Caso contrário, **false** será retornado.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Atualize o **ViewBag** com o nome da tabela.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Tabelas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **CreateTable** para o nome de exibição e selecione **Adicionar**.

1. Abra `CreateTable.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **Criar tabela** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Criar Tabela](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Conforme mencionado anteriormente, o método **CloudTable.CreateIfNotExists** retornará **true** apenas quando a tabela não existir e for criada. Portanto, se você executar o aplicativo quando a tabela existir, o método retornará **false**. Para executar o aplicativo várias vezes, você deverá excluir a tabela antes de executar o aplicativo novamente. É possível excluir a tabela por meio do método **CloudTable.Delete**. Também é possível excluir a tabela usando o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

As *entidades* são mapeadas para objetos C\# usando uma classe personalizada derivada de **TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da sua entidade. Nesta seção, você verá como definir uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes, mas usar chaves de partição diferentes permite uma escalabilidade maior de operações paralelas. Para qualquer propriedade que deva ser armazenada no serviço Tabela, a propriedade deve ser uma propriedade pública de um tipo com suporte que exponha os valores de configuração e recuperação.
A classe da entidade *deve* declarar um construtor público sem parâmetros.

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o arquivo `TablesController.cs` .

1. Adicione a seguinte diretiva para que o código no arquivo `TablesController.cs` possa acessar a classe **CustomerEntity**:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adicione um método chamado **AddEntity** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **AddEntity**, obtenha um objeto **CloudStorageAccount** que representa as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que representa uma referência à tabela na qual a nova entidade será adicionada. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instancie e inicialize a classe **CustomerEntity**.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Crie um objeto **TableOperation** que insere a entidade do cliente.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Execute a operação de inserção chamando o método **Cloudtable.Execute**. Você pode verificar o resultado da operação inspecionando a propriedade **TableResult.HttpStatusCode**. Um código de status de 2xx indica que a ação solicitada pelo cliente foi processada com êxito. Por exemplo, inserções bem-sucedidas de novas entidades resultam em um código de status HTTP 204, indicando que a operação foi processada com êxito, e o servidor não retornou qualquer conteúdo.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Atualize o **ViewBag** com o nome da tabela e os resultados da operação de inserção.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Tabelas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, digite **AddEntity** para o nome da exibição e selecione **Adicionar**.

1. Abra `AddEntity.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **Adicionar entidade** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Adicionar entidade](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Você pode verificar se a entidade foi adicionada seguindo as etapas na seção [Obter uma única entidade](#get-a-single-entity). Você também pode usar o [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para exibir todas as entidades para suas tabelas.

## <a name="add-a-batch-of-entities-to-a-table"></a>Adicionar um lote de entidades a uma tabela

Além de poder [adicionar uma entidade por vez a uma tabela](#add-an-entity-to-a-table), também é possível adicionar entidades em lote. Adicionar entidades em lote reduz o número de viagens de ida e volta entre o código e o serviço Tabela do Azure. As etapas a seguir ilustram como adicionar várias entidades a uma tabela com uma única operação de inserção:

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o arquivo `TablesController.cs` .

1. Adicione um método chamado **AddEntities** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **AddEntities**, obtenha um objeto **CloudStorageAccount** que representa as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que representa uma referência à tabela na qual as novas entidades serão adicionadas. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instancie alguns objetos de cliente com base na classe modelo **CustomerEntity** apresentada na seção [Adicionar uma entidade a uma tabela](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Obtenha um objeto **TableBatchOperation**.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Adicione entidades ao objeto da operação de inserção em lote.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Execute a operação de inserção em lote chamando o método **CloudTable.ExecuteBatch**.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. O método **CloudTable.ExecuteBatch** retorna uma lista de objetos **TableResult** em que cada objeto **TableResult** pode ser examinado para determinar o sucesso ou falha de cada operação individual. Para este exemplo, passe a lista para uma exibição e permita que os resultado de cada operação sejam mostrados pela exibição. 
 
    ```csharp
    return View(results);
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Tabelas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, digite **AddEntities** para o nome da exibição e selecione **Adicionar**.

1. Abra `AddEntities.cshtml` e modifique-o para que se pareça com o seguinte.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **Adicionar entidades** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Adicionar entidades](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Você pode verificar se a entidade foi adicionada seguindo as etapas na seção [Obter uma única entidade](#get-a-single-entity). Você também pode usar o [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para exibir todas as entidades para suas tabelas.

## <a name="get-a-single-entity"></a>Obter uma única entidade

Esta seção mostra como obter uma única entidade de uma tabela usando a chave de linha e de partição da entidade. 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment) e usa dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o arquivo `TablesController.cs` .

1. Adicione um método chamado **GetSingle** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **GetSingle**, obtenha um objeto **CloudStorageAccount** que representa as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que representa uma referência à tabela da qual a entidade será recuperada. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie um objeto de operação de recuperação que usa um objeto de entidade derivado de **TableEntity**. O primeiro parâmetro é *partitionKey*, e o segundo parâmetro é *rowKey*. Usando a classe **CustomerEntity** e os dados apresentados na seção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o trecho de código a seguir consulta a tabela em busca de uma entidade **CustomerEntity** com um valor de *partitionKey* de “Mateus” e um valor de *rowKey* de “Rodrigues”:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Execute a operação de recuperação.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Passe o resultado para ser mostrado na exibição.

    ```csharp
    return View(result);
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Tabelas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **GetSingle** para o nome da exibição e selecione **Adicionar**.

1. Abra `GetSingle.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **Obter Único** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Obter um único](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Obter todas as entidades em uma partição

Conforme mencionado na seção [Adicionar uma entidade a uma tabela](#add-an-entity-to-a-table), a combinação de uma partição e uma chave de linha identifica com exclusividade uma entidade em uma tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes. Esta seção mostra como consultar uma tabela de todas as entidades de uma partição específica.  

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment) e usa dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o arquivo `TablesController.cs` .

1. Adicione um método chamado **GetPartition** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **GetPartition**, obtenha um objeto **CloudStorageAccount** que representa as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que representa uma referência à tabela da qual as entidades serão recuperadas. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie uma instância de um objeto **TableQuery** que especifica a consulta na cláusula **Where**. Usando a classe **CustomerEntity** e os dados apresentados na seção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o trecho de código a seguir consulta a tabela em busca de uma entidade na qual **PartitionKey** (sobrenome do cliente) tem um valor de “Rodrigues”:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Dentro de um loop, chame o método **CloudTable.ExecuteQuerySegmented** passando o objeto de consulta para o qual você criou uma instância na etapa anterior.  O método **CloudTable.ExecuteQuerySegmented** retorna um objeto **TableContinuationToken** que - quando é **nulo** -indica que não há mais entidades para recuperação. Dentro do loop, use outro loop para iterar sobre as entidades retornadas. No código de exemplo a seguir, cada entidade retornada é adicionada a uma lista. Depois que o loop é encerrado, a lista é passada para ser mostrada em uma exibição: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Tabelas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **GetPartition** para o nome da exibição e selecione **Adicionar**.

1. Abra `GetPartition.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **Obter Partição** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Obter Partição](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Excluir uma entidade

Esta seção ilustra como excluir uma entidade de uma tabela.

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment) e usa dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o arquivo `TablesController.cs` .

1. Adicione um método chamado **DeleteEntity** que retorna um **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **DeleteEntity**, obtenha um objeto **CloudStorageAccount** que representa as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que representa uma referência à tabela da qual a entidade será excluída. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie um objeto de operação de exclusão que usa um objeto de entidade derivado de **TableEntity**. Neste caso, usamos a classe **CustomerEntity** e os dados apresentados na seção, [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). A **Etag** da entidade deve ser definida como um valor válido.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Execute a operação de exclusão.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Passe o resultado para ser mostrado na exibição.

    ```csharp
    return View(result);
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Tabelas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, digite **DeleteEntity** para o nome da exibição e selecione **Adicionar**.

1. Abra `DeleteEntity.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **Excluir entidade** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Obter um único](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Próximas etapas
Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.

  * [Introdução ao Armazenamento de Blobs do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao Armazenamento de Filas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
