---
title: 'O Azure Cosmos DB: Compilar um aplicativo com Python e a API do SQL'
description: Apresenta um exemplo de código Python que pode ser usado para se conectar à API do SQL do Azure Cosmos DB e consultá-la
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: d85b156e9c6d213b6f65fe738f5d22c8cce022ee
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001189"
---
# <a name="azure-cosmos-db-build-a-python-application-using-azure-cosmos-db-sql-api-account"></a>O Azure Cosmos DB: Criar um aplicativo do Python usando a conta de API de SQL do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Crie e consulte rapidamente documentos, chave/valor, coluna larga e bancos de dados de grafo. Todas essas operações beneficiam-se da distribuição e da escala do Azure Cosmos DB.

Este início rápido demonstra como criar uma conta de [API do SQL](sql-api-introduction.md), um banco de dados de documento e um contêiner do Azure Cosmos DB usando o portal do Azure. Em seguida, você compila e executa um aplicativo de console criado com o SDK de Python para a [API do SQL](sql-api-sdk-python.md). Este Início Rápido usa a versão 3.0 do [SDK do Python](https://pypi.org/project/azure-cosmos).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6](https://www.python.org/downloads/), com o `python` executável disponível no `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensão do Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contêiner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora vamos clonar um aplicativo de API do SQL do GitHub, definir a cadeia de conexão e executá-lo.

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```cmd
    md "git-samples"
    ```
   Se você estiver usando um prompt do bash, use o seguinte comando:

   ```bash
   mkdir "git-samples"
   ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [portal do Azure](https://portal.azure.com/), em sua conta do Azure Cosmos, no painel de navegação à esquerda, clique em **Chaves**. Você usará os botões de cópia no lado direito da tela para copiar o **URI** e a **Chave Primária** para o arquivo `CosmosGetStarted.py` na próxima etapa.

    ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/create-sql-api-dotnet/keys.png)

2. Abra o arquivo `CosmosGetStarted.py` em \git-samples\azure-cosmos-db-python-getting-started no Visual Studio Code.

3. Copie o valor do **URI** do portal (usando o botão de cópia) e transforme-o no valor da chave do **ponto de extremidade** em ``CosmosGetStarted.py``. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Em seguida, copie o valor da **CHAVE PRIMÁRIA** do portal e transforme-a no valor de **config.PRIMARYKEY** em ``CosmosGetStarted.py``. Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

    `'PRIMARYKEY': 'FILLME',`

5. Salve o arquivo ``CosmosGetStarted.py``.

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Saiba mais sobre os recursos de banco de dados criados no código ou vá para [Atualizar a cadeia de conexão](#update-your-connection-string).

Observe que se você estiver familiarizado com a versão anterior do SDK do Python, poderá estar acostumado a ver os termos "coleção" e "documento". Como o Azure Cosmos DB dá suporte a vários modelos de API, a versão 3.0 ou superior do SDK do Python usa o termo genérico "contêiner", que pode ser uma coleção, um gráfico ou uma tabela, e "item" para descrever o conteúdo do contêiner.

Todos os snippets de código a seguir são retirados do arquivo `CosmosGetStarted.py`.

* O CosmosClient é inicializado. Atualize os valores de "Ponto de Extremidade" e "chave mestra" conforme descrito na seção [Atualizar sua cadeia de conexão](#update-your-connection-string). 

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* Um novo banco de dados é criado.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* Uma nova coleção é criada.

    ```python
    # Create collection options
    options = {
        'offerThroughput': 400
    }

    # Create a container
    container = client.CreateContainer(db['_self'], container_definition, options)
    ```

* Alguns itens são adicionados ao contêiner.

    ```python
    # Create and add some items to the container
    item1 = client.CreateItem(container['_self'], {
        'serverId': 'server1',
        'Web Site': 0,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 1!'
        }
    )

    item2 = client.CreateItem(container['_self'], {
        'serverId': 'server2',
        'Web Site': 1,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 2!'
        }
    )
    ```

* Uma consulta é executada usando SQL

    ```python
    query = {'query': 'SELECT * FROM server s'}

    options = {}
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryItems(container['_self'], query, options)
    for item in iter(result_iterable):
        print(item['message'])
    ```
   
## <a name="run-the-app"></a>Execute o aplicativo

1. No Visual Studio Code, selecione **Exibir**>**Paleta de Comandos**. 

2. No prompt, digite **Python: Selecione Interpretador** e, em seguida, selecione a versão do Python a ser usada.

    O Rodapé no Visual Studio Code é atualizado para indicar o interpretador selecionado. 

3. Selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.

4. Na janela do terminal integrada, verifique se você está na pasta azure-cosmos-db-python-getting-started. Se não estiver, execute o comando a seguir para alternar para a pasta raiz. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Execute o seguinte comando para instalar o pacote azure-cosmos. 

    ```python
    pip3 install azure-cosmos
    ```

    Se você receber um erro de acesso negado ao tentar instalar azure-cosmos, precisará [executar o VS Code como administrador](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Execute o seguinte comando para executar o exemplo, criar e armazenar novos documentos no Azure Cosmos DB.

    ```python
    python CosmosGetStarted.py
    ```

7. Para confirmar a criação e gravação dos novos itens, no Portal do Azure, selecione **Data Explorer**, expanda **coll**, expanda **Documentos** e, depois, selecione o documento **server1**. O conteúdo do documento server1 corresponde o conteúdo retornado na janela do terminal integrado. 

    ![Exibir os novos documentos no Portal do Azure](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos, criar um contêiner usando o Data Explorer e executar um aplicativo. Agora, é possível importar outros dados para sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB para a API do SQL](import-data.md)


