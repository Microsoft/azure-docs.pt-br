---
title: Compilar um aplicativo .NET do BD Cosmos do Azure usando a API de Tabela | Microsoft Docs
description: "Introdução à API de Tabela do BD Cosmos do Azure usando .NET"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.openlocfilehash: 9d347f37ed6e0ddcde9d2f9a7a8d1d8ff65ab4f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>BD Cosmos do Azure: compilar um aplicativo .NET usando a API de Tabela

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do BD Cosmos do Azure. 

Este início rápido demonstra como criar uma conta do BD Cosmos do Azure e uma tabela dentro desta conta usando o portal do Azure. Você vai escrever código para inserir, atualizar e excluir entidades e execute algumas consultas usando o novo pacote [tabela Premium do Microsoft Azure Storage](https://aka.ms/premiumtablenuget) (versão prévia) do NuGet. Esta biblioteca tem as mesmas classes e assinaturas de método do [SDK do Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage) público, mas também tem a capacidade de se conectar às contas do Azure Cosmos DB usando a [API de Tabela](table-introduction.md) (versão prévia). 

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

Agora é possível adicionar dados à sua nova tabela usando o Data Explorer (versão prévia).

1. No Data Explorer, expanda **sample-table**, clique em **Entidades** e clique em **Adicionar Entidade**.

   ![Criar novas entidades no Data Explorer no portal do Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Agora, adicione dados às caixas de valor PartitionKey e RowKey e clique em **Adicionar Entidade**.

   ![Definir a chave de partição e a chave de linha para uma nova entidade](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Agora, é possível adicionar mais entidades à tabela, editar as entidades ou consultar os dados no Data Explorer. Por meio do Data Explorer, também é possível dimensionar a taxa de transferência e adicionar procedimentos armazenados, funções definidas pelo usuário e gatilhos à tabela.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de Tabela do github, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal do Git, como git bash, e `cd` para um diretório de trabalho.  

2. Execute o comando a seguir para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Em seguida, abra o arquivo da solução no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida análise do que está acontecendo no aplicativo. Abra o arquivo Program.cs e você verá que essas linhas de código criam os recursos do BD Cosmos do Azure. 

* O CloudTableClient é inicializado.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* A nova tabela é criada se ainda não existir.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Uma série de etapas são executadas na tabela usando a classe `TableOperation`.

   ```csharp
   TableOperation insertOperation = TableOperation.Insert(item);
   table.Execute(insertOperation);
   ```
   
   ```csharp
   TableOperation retrieveOperation = TableOperation.Retrieve<T>(items[i].PartitionKey, items[i].RowKey);
   table.Execute(retrieveOperation);
   ```
   
   
   ```csharp
   TableOperation deleteOperation = TableOperation.Delete(items[i]);
   table.Execute(deleteOperation);
   ```


## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora vamos atualizar as informações de cadeia de conexão para que o seu aplicativo possa se comunicar com o BD Cosmos do Azure. 

1. No Visual Studio, abra o arquivo app.config. 

2. No [portal do Azure](http://portal.azure.com/), no menu de navegação à esquerda do BD Cosmos do Azure, clique em **Cadeia de Conexão**. Em seguida, no novo painel, clique no botão Copiar da cadeia de conexão. 

    ![Exibir e copiar o ponto de extremidade e a chave de conta no painel Cadeia de Conexão](./media/create-table-dotnet/keys.png)

3. Cole o valor no arquivo PP.config como o valor de PremiumStorageConnectionString. 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    Você pode deixar o StandardStorageConnectionString como está.

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. No Visual Studio, clique com o botão direito do mouse no projeto **PremiumTableGetStarted** no **Gerenciador de Soluções** e clique em **Gerenciar Pacotes NuGet**. 

2. Na caixa **procurar** do NuGet, digite *WindowsAzure.Storage-PremiumTable*.

3. Marque a caixa de seleção **Incluir pré-lançamento**. 

4. Nos resultados, instale a biblioteca **WindowsAzure.Storage-PremiumTable**. Isso instala o pacote da API de Tabela do BD Cosmos do Azure de versão prévia, bem como todas as dependências. Observe que se trata de um pacote NuGet diferente do pacote do Armazenamento do Microsoft Azure usado pelo armazenamento de Tabelas do Azure. 

5. Clique em CTRL + F5 para executar o aplicativo.

    A janela do console exibe os dados que estão sendo adicionados, recuperados, consultados, substituídos e excluídos da tabela. Quando o script for concluído, pressione qualquer tecla duas vezes para fechar a janela do console. 
    
    ![Saída de console do início rápido](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. Se você deseja ver as novas entidades no Data Explorer, basta comentar as linhas 188 208 em program.cs para que elas não sejam excluídas e execute o exemplo novamente. 

    Agora, você pode ir para o Data Explorer, clicar em **Atualizar**, expandir a tabela **Pessoas** e clicar em **Entidades** para trabalhar com esses novos dados. 

    ![Novas entidades no Data Explorer](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse início rápido no portal do Azure com as seguintes etapas: 

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure, como criar uma tabela usando o Data Explorer e como executar um aplicativo.  Agora, você pode consultar os dados usando a API de Tabela.  

> [!div class="nextstepaction"]
> [Consultar usando a API de Tabela](tutorial-query-table.md)

