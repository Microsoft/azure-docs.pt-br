---
title: Criar um banco de dados de grafo com Java no Azure Cosmos DB
description: Apresenta um exemplo de código Java que pode ser usado para, usando o Gremlin, conectar-se aos dados de grafo no Azure Cosmos DB e consultá-los.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: chrande
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a793eb69b344e361679759e46db47c78885831e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360948"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Início Rápido: Criar um banco de dados de grafo com o SDK do Java e a API do Gremlin para Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Console do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Neste início rápido, você criará e gerenciará uma conta da API do Gremlin para Azure Cosmos DB (grafo) no portal do Azure e adicionará dados usando um aplicativo Java clonado do GitHub. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [JDK (Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a variável de ambiente `JAVA_HOME` para a pasta em que o JDK está instalado.
- Um [arquivo binário do Maven](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de grafo, você precisa criar uma conta do banco de dados Gremlin (Graph) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Vamos clonar um aplicativo de API do Gremlin do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática.  

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para uma pasta para instalar o aplicativo de exemplo.  

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-information).

Os snippets de código a seguir foram todos obtidos do arquivo *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java*.

Este aplicativo de console Java usa um banco de dados da [API do Gremlin](graph-introduction.md) com o driver OSS [Apache TinkerPop](https://tinkerpop.apache.org/). 

- O Gremlin `Client` é inicializado nas configurações do arquivo *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml*.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Séries de etapas do Gremlin são executadas usando o método `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Atualizar as informações de conexão

Agora, volte ao Portal do Azure para obter as informações de conexão e copiá-las para o aplicativo. Essas configurações permitem que seu aplicativo se comunique com o banco de dados hospedado.

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Chaves**. 

    Copie a primeira parte do valor do URI.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Exibir e copiar uma chave de acesso no Portal do Azure, na página Chaves":::

2. Abra o arquivo *src/remote.yaml* e cole o valor de ID exclusivo de `$name$` em `hosts: [$name$.graphs.azure.com]`.

    A linha 1 de *remote.yaml* agora deverá ser semelhante a 

    `hosts: [test-graph.graphs.azure.com]`

3. Mude `graphs` para `gremlin.cosmosdb` no valor `endpoint`. (Se você criou sua conta de banco de dados do gráfico antes de 20 de dezembro de 2017, não faça nenhuma alteração no valor do ponto de extremidade e vá para a próxima etapa.)

    Agora, o valor de ponto de extremidade deve ter esta aparência:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. No Portal do Azure, use o botão de copiar para copiar a CHAVE PRIMÁRIA e cole-a em `$masterKey$` em `password: $masterKey$`.

    A linha 4 de *remote.yaml* agora deverá ser semelhante a 

    `password: 2Ggkr662ifxz2Mg==`

5. Altere a linha 3 de *remote.yaml* de

    `username: /dbs/$database$/colls/$collection$`

    para 

    `username: /dbs/sample-database/colls/sample-graph`

    Caso tenha usado um nome exclusivo para o banco de dados ou gráfico de exemplo, atualize os valores conforme apropriado.

6. Salve o arquivo *remote.yaml*.

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. Na janela do terminal git, `cd` para a pasta azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Na janela do terminal git, use o seguinte comando para instalar os pacotes necessários do Java.

   ```git
   mvn package
   ```

3. Na janela do terminal git, use os comandos a seguir para iniciar o aplicativo Java.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    A janela do terminal exibe os vértices sendo adicionados ao grafo. 
    
    Se ocorrerem erros de tempo limite, verifique se você atualizou as informações de conexão corretamente em [Atualizar suas informações de conexão](#update-your-connection-information) e também tente executar o último comando novamente. 
    
    Depois que o programa for interrompido, selecione Enter e, em seguida, alterne novamente para o portal do Azure no navegador da Internet. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Revisar e adicionar dados de exemplo

Agora, você pode voltar para o Data Explorer, ver os vértices adicionados ao grafo e acrescentar pontos de dados adicionais.

1. Em sua conta do Azure Cosmos DB no portal do Azure, selecione **Data Explorer**, expanda **sample-graph**, selecione **Grafo** e, em seguida, selecione **Aplicar Filtro**. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="A captura de tela mostra a opção Grafo selecionada na API com a opção Aplicar Filtro.":::

2. Na lista **Resultados**, observe os novos usuários adicionados ao grafo. Selecione **ben** e observe que o usuário está conectado a robin. Você pode mover os vértices arrastando e soltando, ampliar e reduzir rolando o botão de rolagem do mouse e expandir o tamanho do grafo com a seta dupla. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Novos vértices no grafo no Data Explorer no portal do Azure":::

3. Vamos adicionar alguns novos usuários. Selecione **Novo Vértice** para adicionar dados ao grafo.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="A captura de tela mostra o painel Novo Vértice, em que você pode inserir valores.":::

4. Na caixa de diálogo, insira *person*.

5. Selecione **Adicionar propriedade** para adicionar cada uma das propriedades a seguir. Observe que você pode criar propriedades exclusivas para cada pessoa no grafo. Somente a chave da id é necessária.

    chave|value|Observações
    ----|----|----
    id|ashley|O identificador exclusivo do vértice. Se você não especificar uma ID, ela será gerada para você.
    gender|feminino| 
    técnico | java | 

    > [!NOTE]
    > Neste início rápido, você criou uma coleção não particionada. No entanto, se você criar uma coleção particionada especificando uma chave de partição durante a criação da coleção, então, precisará incluir a chave de partição como uma chave em cada novo vértice. 

6. Selecione **OK**. Talvez seja necessário expandir a tela para ver **OK** na parte inferior da tela.

7. Selecione **Novo Vértice** novamente e adicione um novo usuário. 

8. Digite um rótulo de *pessoa*.

9. Selecione **Adicionar propriedade** para adicionar cada uma das seguintes propriedades:

    chave|value|Observações
    ----|----|----
    id|rakesh|O identificador exclusivo do vértice. Se você não especificar uma ID, ela será gerada para você.
    gender|masculino| 
    escola|MIT| 

10. Selecione **OK**. 

11. Selecione o botão **Aplicar Filtro** com o filtro `g.V()` padrão para exibir todos os valores do grafo. Todos os usuários agora aparecem na lista **Resultados**. 

    Conforme você adiciona mais dados, pode usar os filtros para limitar os resultados. Por padrão, o Data Explorer usa `g.V()` para recuperar todos os vértices em um grafo. Você pode alterá-lo para outra [consulta de grafo](tutorial-query-graph.md), como `g.V().count()`, para retornar uma contagem de todos os vértices no grafo no formato JSON. Se você alterou o filtro, altere o filtro novamente para `g.V()` e selecione **Aplicar Filtro** para exibir todos os resultados novamente.

12. Agora, você pode conectar rakesh e ashley. Verifique se **ashley** está selecionada na lista **Resultados** e, em seguida, selecione :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="Alterar o destino de um vértice em um grafo"::: ao lado de **Destinos**, no lado inferior direito. Talvez seja necessário ampliar a janela para ver o botão.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Alterar o destino de um vértice em um grafo – Azure CosmosDB":::

13. Na caixa **Destino**, insira *rakesh*. Na caixa **Rótulo de borda**, insira *knows* e, em seguida, marque a caixa de seleção.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Adicionar uma conexão no Data Explorer – Azure CosmosDB":::

14. Agora, selecione **rakesh** na lista de resultados e veja se ashley e rakesh estão conectados. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Dois vértices conectados no Data Explorer – Azure CosmosDB":::

Isso conclui a parte da criação de recursos deste tutorial. Você pode continuar a adicionar vértices ao seu grafo, modificar os vértices existentes ou alterar as consultas. Agora vamos examinar as métricas que o Azure Cosmos DB fornece e, em seguida, limpar os recursos. 

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos DB, criar um grafo usando o Data Explorer e executar um aplicativo Java que adiciona dados ao grafo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de grafo avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)

