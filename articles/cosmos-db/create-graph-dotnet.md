---
title: Compilar um aplicativo .NET Framework, Core do Azure Cosmos DB usando a API do Gremlin
description: Apresenta um exemplo de código do .NET Framework/Core que pode ser usado para conectar e consultar o Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: chrande
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1953f4a21df6f550320592fbe009834a7b573887
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360523"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Início Rápido: Compilar um aplicativo .NET Framework ou Core usando a conta da API do Gremlin do Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Console do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta da [API do Gremlin](graph-introduction.md) do Azure Cosmos DB, um banco de dados e um grafo (contêiner) usando o portal do Azure. Depois, compile e execute um aplicativo de console criado usando o driver de código-aberto [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tiver o Visual Studio 2019 instalado, baixe e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora vamos clonar um aplicativo de API do Gremlin do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Em seguida, abra o Visual Studio e o arquivo da solução.

5. Restaure os pacotes NuGet no projeto. Isso deve incluir o driver Gremlin.Net, bem como o pacote Newtonsoft.Json.


6. Também é possível instalar o driver Gremlin.Net manualmente, usando o gerenciador de pacotes Nuget ou o [utilitário de linha de comando do nuget](/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

Todos os snippets de código a seguir são retirados do arquivo Program.cs.

* Defina os parâmetros de conexão baseados na conta criada acima: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* Os comandos de Gremlin a serem executados são listados em um dicionário:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Crie objetos de conexão `GremlinServer` e `GremlinClient` novos usando os parâmetros fornecidos acima:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Execute cada consulta Gremlin usando o objeto `GremlinClient` com uma tarefa assíncrona. Você pode ler as consultas Gremlin do dicionário definido na etapa anterior e executá-las. Depois, obtenha o resultado e leia os valores, que são formatados como um dicionário, usando a classe `JsonSerializer` do pacote Newtonsoft.Json:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [portal do Azure](https://portal.azure.com/), navegue até sua conta de banco de dados do gráfico. Na guia **Visão geral**, você pode ver dois pontos de extremidade - 
 
   **URI de SDK do .NET** - Esse valor é usado ao conectar-se à conta do Graph usando a biblioteca Microsoft.Azure.Graphs. 

   **Ponto de Extremidade do Gremlin** - Esse valor é usado ao se conectar à conta do Graph usando a biblioteca Gremlin.Net.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Copiar o ponto de extremidade":::

   Para executar este exemplo, copie o valor do **Ponto de Extremidade do Gremlin**, exclua o número da porta no final e o URI se tornará `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. O valor de ponto de extremidade deve ter esta aparência: `testgraphacct.gremlin.cosmosdb.azure.com`

1. Em seguida, navegue até a guia **Chaves** e copie o valor de **CHAVE PRIMÁRIA** do portal do Azure. 

1. Depois de copiar o URI e a CHAVE PRIMÁRIA da sua conta, salve-os em uma nova variável de ambiente no computador local que executa o aplicativo. Para definir a variável de ambiente, abra uma janela do prompt de comando e execute o comando a seguir. Não se esqueça de substituir os valores <Your_Azure_Cosmos_account_URI> e <Your_Azure_Cosmos_account_PRIMARY_KEY>.

   ```console
   setx Host "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Abra o arquivo *Program.cs* e atualize as variáveis "banco de dados" e "contêiner" pelos nomes do banco de dados e contêiner (que também é o nome do gráfico) criados acima.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Salve o arquivo Program.cs. 

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Execute o aplicativo de console

Clique em CTRL + F5 para executar o aplicativo. O aplicativo imprimirá os comandos e resultados de consulta Gremlin no console.

   A janela do console exibe os vértices e bordas sendo adicionadas ao grafo. Quando o script for concluído, pressione ENTER para fechar a janela do console.

## <a name="browse-using-the-data-explorer"></a>Navegar usando o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e procurar e consultar seus novos dados de grafo.

1. No Data Explorer, o novo banco de dados aparece no painel Grafos. Expanda os nós do banco de dados e do contêiner, depois clique em **Grafo**.

2. Clique no botão **Aplicar Filtro** para usar a consulta padrão para exibir todos os vértices no grafo. Os dados gerados pelo aplicativo de exemplo são exibidos no painel Grafos.

    É possível ampliar e diminuir o zoom do grafo, expandir o espaço de exibição do grafo, adicionar outros vértices e mover vértices na superfície de exibição.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Exibir o grafo no Data Explorer no portal do Azure":::

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB, como criar um grafo usando o Data Explorer e como executar um aplicativo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de grafo avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)