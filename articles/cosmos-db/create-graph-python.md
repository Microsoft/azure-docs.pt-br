---
title: 'Início Rápido: API do Gremlin com Python – Azure Cosmos DB'
description: Este início rápido mostra como usar a API do Gremlin do Azure Cosmos DB para criar um aplicativo de console com o Portal do Azure e o Python
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 03/29/2021
ms.author: chrande
ms.custom: devx-track-python
ms.openlocfilehash: cef397789d5ebcfa95c01e42dac9a80b9e1564e0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106918"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Início Rápido: Criar um banco de dados de grafo no Azure Cosmos DB usando o Python e o portal do Azure
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Console do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Neste início rápido, você criará e gerenciará uma conta da API do Gremlin para Azure Cosmos DB (grafo) no portal do Azure e adicionará dados usando um aplicativo Python clonado do GitHub. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure.
- O [Python 3.6+](https://www.python.org/downloads/), incluindo o instalador de pacotes [pip](https://pip.pypa.io/en/stable/installing/).
- [Driver do Python para Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Este início rápido exige uma conta de banco de dados de grafo criada depois de 20 de dezembro de 2017. As contas existentes oferecerão suporte a Python depois de serem migradas para a disponibilidade geral.

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de grafo, você precisa criar uma conta do banco de dados Gremlin (Graph) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Vamos clonar um aplicativo de API do Gremlin do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática.  

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    mkdir "./git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para uma pasta para instalar o aplicativo de exemplo.  

    ```bash
    cd "./git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Os snippets foram todos obtidos do arquivo *connect.py* na pasta *C:\git-samples\azure-cosmos-db-graph-python-getting-started\\* . Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-information).

* O `client` do Gremlin é inicializado na linha 155 em *connect.py*. Certifique-se de substituir `<YOUR_DATABASE>` e `<YOUR_CONTAINER_OR_GRAPH>` pelos valores do nome do banco de dados da sua conta e do nome do grafo:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_CONTAINER_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* Uma série de etapas do Gremlin são declaradas no início do arquivo *connect.py*. Elas são executadas usando o método `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Atualizar as informações de conexão

Agora, volte ao Portal do Azure para obter as informações de conexão e copiá-las para o aplicativo. Essas configurações permitem que seu aplicativo se comunique com o banco de dados hospedado.

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Chaves**. 

    Copie a primeira parte do valor do URI.

    :::image type="content" source="./media/create-graph-python/keys.png" alt-text="Exibir e copiar uma chave de acesso no Portal do Azure, na página Chaves":::

2. Abra o arquivo *connect.py* e, na linha 155, cole o valor de URI em `<YOUR_ENDPOINT>` aqui:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    Agora, a parte do URI do objeto do cliente deve ser semelhante a este código:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Altere o segundo parâmetro do objeto `client` para substituir a cadeia de caracteres `<YOUR_DATABASE>` e `<YOUR_COLLECTION_OR_GRAPH>`. Se você tiver usado os valores sugeridos, o parâmetro deve se parecer com este código:

    `username="/dbs/sample-database/colls/sample-graph"`

    Agora, todo o objeto `client` deve se parecer com este código:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. Na página **Chaves**, use o botão Copiar para copiar a CHAVE PRIMÁRIA e cole-a em `<YOUR_PASSWORD>` no parâmetro `password=<YOUR_PASSWORD>`.

    Agora, toda a definição do objeto `client` deve se parecer com este código:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Salve o arquivo *connect.py*.

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. Na janela do terminal git, `cd` para a pasta azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "./git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. Na janela do terminal git, use o seguinte comando para instalar os pacotes necessários do Python.

   ```
   pip install -r requirements.txt
   ```

3. Na janela do terminal git, use os comandos a seguir para iniciar o aplicativo Python.
    
    ```
    python connect.py
    ```

    A janela do terminal exibe os vértices e margens sendo adicionados ao grafo. 
    
    Se ocorrerem erros de tempo limite, verifique se você atualizou as informações de conexão corretamente em [Atualizar suas informações de conexão](#update-your-connection-information) e também tente executar o último comando novamente. 
    
    Após o programa parar, pressione Enter e volte para o Portal do Azure no seu navegador da Internet.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Revisar e adicionar dados de exemplo

Após a inserção dos vértices e das bordas, você pode voltar para o Data Explorer, ver os vértices adicionados ao grafo e acrescentar pontos de dados adicionais.

1. Em sua conta do Azure Cosmos DB no portal do Azure, selecione **Data Explorer**, expanda **sample-graph**, selecione **Grafo** e, em seguida, selecione **Aplicar Filtro**. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png" alt-text="A captura de tela mostra a opção Grafo selecionada na API com a opção Aplicar Filtro.":::

2. Na lista **Resultados**, observe que três novos usuários são adicionados ao grafo. Você pode mover os vértices arrastando e soltando, ampliar e reduzir rolando o botão de rolagem do mouse e expandir o tamanho do grafo com a seta dupla. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png" alt-text="Novos vértices no grafo no Data Explorer no portal do Azure":::

3. Vamos adicionar alguns novos usuários. Selecione o botão **Novo Vértice** para adicionar dados ao grafo.

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="A captura de tela mostra o painel Novo Vértice, em que você pode inserir valores.":::

4. Digite um rótulo de *pessoa*.

5. Selecione **Adicionar propriedade** para adicionar cada uma das propriedades a seguir. Observe que você pode criar propriedades exclusivas para cada pessoa no grafo. Somente a chave da id é necessária.

    chave|value|Observações
    ----|----|----
    pk|/pk| 
    id|ashley|O identificador exclusivo do vértice. Se você não especificar uma ID, ela será gerada para você.
    gender|feminino| 
    técnico | java | 

    > [!NOTE]
    > Neste início rápido, criamos uma coleção não particionada. No entanto, se você criar uma coleção particionada especificando uma chave de partição durante a criação da coleção, então, precisará incluir a chave de partição como uma chave em cada novo vértice. 

6. Selecione **OK**. Talvez seja necessário expandir a tela para ver **OK** na parte inferior da tela.

7. Selecione **Novo Vértice** novamente e adicione um novo usuário. 

8. Digite um rótulo de *pessoa*.

9. Selecione **Adicionar propriedade** para adicionar cada uma das seguintes propriedades:

    chave|value|Observações
    ----|----|----
    pk|/pk| 
    id|rakesh|O identificador exclusivo do vértice. Se você não especificar uma ID, ela será gerada para você.
    gender|masculino| 
    escola|MIT| 

10. Selecione **OK**. 

11. Selecione o botão **Aplicar Filtro** com o filtro `g.V()` padrão para exibir todos os valores do grafo. Todos os usuários agora aparecem na lista **Resultados**. 

    Conforme você adiciona mais dados, pode usar os filtros para limitar os resultados. Por padrão, o Data Explorer usa `g.V()` para recuperar todos os vértices em um grafo. Você pode alterá-lo para outra [consulta de grafo](tutorial-query-graph.md), como `g.V().count()`, para retornar uma contagem de todos os vértices no grafo no formato JSON. Se você alterou o filtro, altere o filtro novamente para `g.V()` e selecione **Aplicar Filtro** para exibir todos os resultados novamente.

12. Agora, podemos conectar rakesh e ashley. Verifique se **ashley** está selecionada na lista **Resultados** e, em seguida, selecione o botão Editar ao lado de **Destinos** no canto inferior direito. Talvez seja necessário ampliar a janela para ver a área **Propriedades**.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Alterar o destino de um vértice em um grafo":::

13. Na caixa **Destino**, digite *rakesh* e, na caixa **Rótulo de borda**, digite *conhece* e, em seguida, selecione a marca de seleção.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png" alt-text="Adicionar uma conexão entre ashley e rakesh no Data Explorer":::

14. Agora, selecione **rakesh** na lista de resultados e veja se ashley e rakesh estão conectados. 

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer.png" alt-text="Dois vértices conectados no Data Explorer":::

Isso conclui a parte da criação de recursos deste tutorial. Você pode continuar a adicionar vértices ao seu grafo, modificar os vértices existentes ou alterar as consultas. Agora vamos examinar as métricas que o Azure Cosmos DB fornece e, em seguida, limpar os recursos. 

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos DB, criar um grafo usando o Data Explorer e executar um aplicativo Python para adicionar dados ao grafo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de grafo avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)

