---
title: Criar um aplicativo Web do Python Flask usando a API para MongoDB do Azure Cosmos DB
description: Apresenta um exemplo de código Python do Flask que você pode usar para se conectar à API para MongoDB do Azure Cosmos DB e consultá-la.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: 3d066005ac18bf42f913885558bf6f0ee5fc50c4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349156"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Início Rápido: Criar um aplicativo do Python usando a API para MongoDB do Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Neste início rápido, você usará uma conta da API do MongoDB para Azure Cosmos DB ou o Emulador do Azure Cosmos DB para executar um aplicativo Web de tarefas pendentes do Python Flask clonado do GitHub. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure. Ou então você pode usar o [Emulador do Azure Cosmos DB](local-emulator.md). 
- [Python 3.6 e superior](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) com a [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora vamos clonar um aplicativo Flask-MongoDB do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática.

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
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Execute o seguinte comando para instalar os módulos do Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Abra a pasta no Visual Studio Code.

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá pular para [Executar o aplicativo Web](#run-the-web-app). 

Os snippets a seguir foram todos obtidos do arquivo *app.py* e usam a cadeia de conexão para o Emulador do Azure Cosmos DB local. A senha deve ser dividida da maneira mostrada abaixo para acomodar as barras "/" que não podem ser analisadas de nenhuma outra maneira.

* Inicialize o cliente MongoDB, recupere o banco de dados e autentique-se.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Recupere a coleção ou crie-a se ela ainda não existir.

    ```python
    todos = db.todo #Select the collection
    ```

* Criar o aplicativo

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Executar o aplicativo Web

1. Verifique se o emulador do Azure Cosmos DB está em execução.

2. Abra uma janela do terminal e emita `cd` para o diretório no qual o aplicativo está salvo.

3. Em seguida, defina a variável de ambiente para o aplicativo Flask com `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` para editores do PowerShell ou `export FLASK_APP=app.py` se você estiver usando um Mac. 

4. Execute o aplicativo com `flask run` e navegue até *http:\//127.0.0.1:5000/* .

5. Adicione e remova tarefas e veja-as adicionadas e alteradas na coleção.

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Caso deseje testar o código em uma conta ativa do Azure Cosmos DB, acesse o portal do Azure para criar uma conta.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Para testar o código na conta ativa do Azure Cosmos DB, obtenha as informações da cadeia de conexão. Em seguida, copie-a no aplicativo.

1. Em sua conta do Azure Cosmos DB no portal do Azure, no painel de navegação à esquerda, selecione **Cadeia de Conexão** e, em seguida, selecione **Chaves de leitura/gravação**. Você usará os botões de cópia no lado direito da tela para copiar o nome de usuário, a cadeia de conexão e a senha. 

2. Abra o arquivo *app.py* no diretório raiz.

3. Copie o valor de **nome de usuário** do portal (usando o botão de cópia) e cole-o como o valor de **nome** no arquivo *app.py*.

4. Em seguida, copie o valor de **cadeia de conexão** no portal e cole-o como o valor do **MongoClient** no arquivo *app.py*.

5. Finalmente copie o valor de **senha** do portal e cole-o como o valor de **senha** no arquivo *app.py*.

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. É possível executá-lo da mesma forma que antes.

## <a name="deploy-to-azure"></a>Implantar no Azure

Para implantar esse aplicativo, você pode criar um novo aplicativo Web no Azure e habilitar a implantação contínua com um fork deste repositório do GitHub. Siga este [tutorial](../app-service/deploy-continuous-deployment.md) para configurar a implantação contínua com o GitHub no Azure.

Ao implantar no Azure, você deve remover as chaves do aplicativo e verificar se a seção abaixo não está comentada:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Em seguida, você precisa adicionar as suas informações de MONGOURL, MONGO_PASSWORD e MONGO_USERNAME nas configurações do aplicativo. Você pode seguir este [tutorial](../app-service/configure-common.md#configure-app-settings) para saber mais sobre as configurações de aplicativo em aplicativos Web do Azure.

Caso não deseje criar um fork deste repositório, selecione o botão **Implantar no Azure** abaixo. Em seguida, acesse o Azure e defina as configurações de aplicativo com as informações da sua conta do Azure Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Se você planeja armazenar o código no GitHub ou em outras opções de controle do código-fonte, lembre-se de remover as cadeias de conexão do código. Nesse caso, elas podem ser definidas com as configurações do aplicativo Web.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta da API do MongoDB para Azure Cosmos DB e usar o Emulador do Azure Cosmos DB para executar um aplicativo Web de tarefas pendentes do Python Flask clonado do GitHub. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)