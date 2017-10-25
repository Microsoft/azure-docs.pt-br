---
title: Compilar um aplicativo .NET Framework ou Core do Azure Cosmos DB usando a API do Graph | Microsoft Docs
description: "Apresenta um exemplo de código do .NET Framework/Core que pode ser usado para conectar e consultar o Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: denlee
ms.openlocfilehash: 4c90ead99c513a56f8891b889e2c873952a33ec8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-net-framework-or-core-application-using-the-graph-api"></a>Azure Cosmos DB: compilar um aplicativo .NET Framework ou Core usando a API do Graph

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do BD Cosmos do Azure. 

Este início rápido demonstra como criar uma conta do BD Cosmos do Azure, um banco de dados e um gráfico (contêiner) usando o Portal do Azure. Em seguida, você compila e executa um aplicativo de console criado na [API do Graph](graph-sdk-dotnet.md) (versão prévia).  

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

Se você já tem o Visual Studio 2017 instalado, verifique se está com o [Visual Studio 2017 Atualização 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de API do Graph do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

Este projeto de exemplo está usando o formato de projeto do .NET Core e foi configurado para se direcionar às seguintes estruturas:
 - netcoreapp2.0
 - net461

1. Abra uma janela de terminal do Git, como git bash, e `cd` para um diretório de trabalho.  

2. Execute o comando a seguir para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Em seguida, abra o Visual Studio e o arquivo da solução. 

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida análise do que está acontecendo no aplicativo. Abra o arquivo Program.cs e você verá que essas linhas de código criam os recursos do BD Cosmos do Azure. 

* O DocumentClient é inicializado. Na versão prévia, nós adicionamos uma API de extensão de gráfico no cliente Azure Cosmos DB. Estamos trabalhando em um cliente gráfico autônomo dissociado do cliente do Azure Cosmos DB e dos recursos.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* Um novo banco de dados é criado.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* Um novo gráfico é criado.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* Uma série de etapas do Gremlin são executadas usando o método `CreateGremlinQuery`.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No Visual Studio 2017, abra o arquivo appsettings.json. 

2. No Portal do Azure, em sua conta do Azure Cosmos DB, clique em **Chaves** no painel de navegação esquerdo. 

    ![Exibir e copiar uma chave primária no portal do Azure, na página de Chaves](./media/create-graph-dotnet/keys.png)

3. Copie o valor de **URI** do portal e transforme-o no valor da chave do Ponto de extremidade no appsettings.json. Você pode usar o botão copiar, como na captura de tela anterior, para copiar o valor.

    `"endpoint": "https://FILLME.documents.azure.com:443/",`

4. Copie o valor da **CHAVE PRIMÁRIA** no portal e transforme-o no valor da chave AuthKey em App.config, depois salve suas alterações. 

    `"authkey": "FILLME"`

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Execute o aplicativo de console

Antes de executar o aplicativo, é recomendável que você atualize o pacote *Microsoft.Azure.Graphs* para a versão mais recente.

1. No Visual Studio, clique com o botão direito do mouse no projeto **GraphGetStarted** no **Gerenciador de Soluções** e clique em **Gerenciar Pacotes NuGet**. 

2. Na guia **Atualizações** do Gerenciador de pacotes do NuGet, digite *Microsoft.Azure.Graphs* e marque a caixa **Inclui pré-lançamento**. 

3. Nos resultados, atualize a biblioteca **Microsoft.Azure.Graphs** para a versão mais recente do pacote. Isso instala o pacote da biblioteca de extensão gráfica do BD Cosmos do Azure e todas as dependências.

    Se receber uma mensagem sobre a análise das alterações para a solução, clique em **OK**. Se receber uma mensagem sobre a aceitação da licença, clique em **Aceito**.

4. Clique em CTRL + F5 para executar o aplicativo.

   A janela do console exibe os vértices e bordas sendo adicionadas ao gráfico. Quando o script for concluído, pressione ENTER duas vezes para fechar a janela do console.

## <a name="browse-using-the-data-explorer"></a>Navegar usando o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e procurar e consultar seus novos dados de gráfico.

1. No Data Explorer, o novo banco de dados aparece no painel Gráficos. Expanda **graphdb**, **graphcollz** e, depois, clique em **Gráfico**.

2. Clique no botão **Aplicar Filtro** para usar a consulta padrão e exibir todos os vértices no gráfico. Os dados gerados pelo aplicativo de exemplo são exibidos no painel Gráficos.

    Você pode ampliar e reduzir o zoom do gráfico, pode expandir o espaço de exibição do gráfico, adicionar outros vértices e mover vértices na superfície de exibição.

    ![Exibir o gráfico no Data Explorer no Portal do Azure](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse início rápido no portal do Azure com as seguintes etapas: 

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure, como criar um gráfico usando o Data Explorer e como executar um aplicativo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de gráfico avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)

