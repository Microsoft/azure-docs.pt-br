---
title: Conectar um aplicativo Go à API do Azure Cosmos DB para MongoDB
description: Este início rápido demonstra como conectar um aplicativo Go existente à API do Azure Cosmos DB para MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: c9829b49662c90df685388691c04b201a7010eb8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765207"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Início Rápido: Conectar um aplicativo Go à API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global. Neste início rápido você cria e gerencia uma conta do Azure Cosmos DB usando o Azure Cloud Shell, clona um aplicativo de exemplo existente do GitHub e configura ele para funcionar com o Azure Cosmos DB. 

O aplicativo de exemplo é uma ferramenta de gerenciamento `todo` baseada em linha de comando escrita em Go. A API do Azure Cosmos DB para MongoDB é [compatível com o protocolo de transmissão do MongoDB](./mongodb-introduction.md#wire-protocol-compatibility), possibilitando que qualquer driver cliente do MongoDB se conecte a ele. Esse aplicativo usa o [Driver do Go para MongoDB](https://github.com/mongodb/mongo-go-driver) de maneira transparente para o aplicativo em que os dados são armazenados em um banco de dados do Azure Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure. Use também o [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com a cadeia de conexão `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- Ter [Go](https://golang.org/) instalado em seu computador e ter conhecimentos práticos de Go.
- [Git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Execute os comandos a seguir para clonar o repositório de exemplo.

1. Abra um prompt de comando, crie uma pasta chamada `git-samples` e, em seguida, feche o prompt de comando.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como o aplicativo funciona, poderá examinar os trechos de código a seguir. Caso contrário, você poderá passar direto para [Executar o aplicativo](#run-the-application). O layout do aplicativo é o seguinte:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Todos os snippets de código a seguir são retirados do arquivo `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Conectando o aplicativo Go para o BD Cosmos do Azure

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) encapsula a cadeia de conexão para o Azure Cosmos DB, que é passada usando uma variável de ambiente (detalhes na próxima seção). A conexão é inicializada usando [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient), ao qual a instância de `clientOptions` é passada. [A função `Ping`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) é invocada para confirmar a conectividade bem-sucedida (trata-se de uma estratégia fail-fast)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Usar a configuração [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) é importante, pois sem ela você verá o seguinte erro de conectividade: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Criar um item de `todo`

Para criar um `todo`, obtemos um vínculo para um [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) e invocamos a função [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne). 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Passamos um struct `Todo` que contém a descrição e o status (inicialmente definido como `pending`)

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>Listar itens de `todo`

Podemos listar as Tarefas Pendentes com base em critérios. Um [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) é criado para encapsular os critérios de filtro

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) é usado para pesquisar documentos com base no filtro e o resultado é convertido em uma fatia de `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Por fim, as informações são renderizadas em formato tabular

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Atualizar um item de `todo`

Um `todo` pode ser atualizado com base em seu `_id`. Um filtro de [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) é criado com base no `_id` e outro é criado para as informações atualizadas, que é um novo status (`completed` ou `pending`) nesse caso. Por fim, a função [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) é invocada com o filtro e o documento atualizado

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Excluir um `todo`

Um `todo` é excluído com base em seu `_id` e é encapsulado na forma de um [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D). [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) é invocado para excluir o documento.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Compilar o aplicativo

Mude para o diretório em que você clonou o aplicativo e crie-o (usando `go build`).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Para verificar se o aplicativo foi criado corretamente.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Configurar o Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Entrar no Azure

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Caso precise instalá-la ou atualizá-la, confira [Instalar a CLI do Azure]. 

Se estiver usando uma CLI do Azure instalada, entre em sua assinatura do Azure com o comando [az login](/cli/azure/reference-index#az_login) e siga as instruções na tela. Você poderá ignorar esta etapa se estiver usando o Azure Cloud Shell.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Adicione o módulo do BD Cosmos do Azure

Se você estiver usando uma CLI do Azure instalada, verifique se o componente `cosmosdb` já está instalado ao executar o comando `az`. Se `cosmosdb` estiver na lista de comandos básicos, vá para o próximo comando. Você poderá ignorar esta etapa se estiver usando o Azure Cloud Shell.

Se `cosmosdb` não estiver na lista de comandos base, reinstale a [CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/management/overview.md) com o [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure, como os aplicativos Web, bancos de dados e contas de armazenamento, são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos na região da Europa Ocidental. Escolha um nome exclusivo para o grupo de recursos.

Se você estiver usando o Azure Cloud Shell, selecione **Experimente**, siga as instruções na tela para fazer logon e copie o comando para o prompt de comando.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta do Cosmos com o comando [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create).

No comando a seguir, substitua os espaços reservados `<cosmosdb-name>` pelo seu próprio nome exclusivo da conta do Cosmos. Esse nome exclusivo será usado como parte do ponto de extremidade do Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`), portanto, o nome precisa ser exclusivo entre todas as contas do Cosmos no Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro `--kind MongoDB` habilita conexões do cliente MongoDB.

Quando a conta do BD Cosmos do Azure é criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir. 

> [!NOTE]
> Este exemplo usa JSON como o formato de saída da CLI do Azure, que é o padrão. Para usar outro formato de saída, consulte [Formatos de saída para comandos da CLI do Azure](/cli/azure/format-output-azure-cli). 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Recuperar a chave do banco de dados

Para conectar-se a um banco de dados Cosmos, você precisa da chave de banco de dados. Use o comando [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) para recuperar a chave primária.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

A CLI do Azure emite informações semelhantes ao seguinte exemplo. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Configurar o aplicativo 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exporte a cadeia de conexão, o banco de dados MongoDB e os nomes de coleção como variáveis de ambiente. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> A opção `ssl=true` é importante devido aos requisitos do Cosmos DB. Para obter mais informações, confira [Requisitos de cadeia de conexão](connect-mongodb-account.md#connection-string-requirements).
>

Para a variável de ambiente `MONGODB_CONNECTION_STRING`, substitua os espaços reservados por `<COSMOSDB_ACCOUNT_NAME>` e `<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: O nome da conta do Azure Cosmos DB que você criou
2. `<COSMOSDB_PASSWORD>`: A chave do banco de dados extraída na etapa anterior

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Você pode escolher seus valores preferenciais para `MONGODB_DATABASE` e `MONGODB_COLLECTION` ou deixá-los como estão.

## <a name="run-the-application"></a>Executar o aplicativo

Para criar um `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Se for bem-sucedido, você verá uma saída com o `_id` do MongoDB do documento recém-criado:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Criar outro `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Listar todos os `todo`s

```bash
./todo --list all
```

Você deve examinar aqueles que acabou de adicionar em formato tabular

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Para atualizar o status de um `todo` (por exemplo, para alterá-lo para o status `completed`), use a ID de `todo`

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Listar somente os `todo`s concluídos

```bash
./todo --list completed
```

Você deve ver aquele que acabou de atualizar

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Exibir dados no Data Explorer

Os dados armazenados no Azure Cosmos DB ficam disponíveis para exibição e consulta no portal do Azure.

Para exibir, consultar e trabalhar com os dados de usuário criados na etapa anterior, faça logon no [portal do Azure](https://portal.azure.com) no seu navegador da Web.

Na caixa Pesquisa superior, insira **Azure Cosmos DB**. Quando a folha da conta do Cosmos abrir, selecione sua conta do Cosmos. No painel de navegação esquerdo, clique em **Data Explorer**. Expanda a coleção no painel Coleções e, em seguida, será possível exibir os documentos na coleção, consultar os dados e até mesmo criar e executar gatilhos, UDFs e procedimentos armazenados. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Data Explorer mostrando o documento recém-criado":::


Excluir um `todo` usando sua ID

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Listar os `todo`s a serem confirmados

```bash
./todo --list all
```

O `todo` que você acabou de excluir não deve estar presente

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta da API do MongoDB para Azure Cosmos DB usando o Azure Cloud Shell e criar e executar um aplicativo Go de linha de comando para gerenciar os `todo`s. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)