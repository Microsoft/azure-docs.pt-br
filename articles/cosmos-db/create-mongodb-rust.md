---
title: Conectar um aplicativo Rust à API do Azure Cosmos DB para MongoDB
description: Este início rápido demonstra como criar um aplicativo Rust com suporte da API do Azure Cosmos DB para MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 91e7bafe98b1aceaf8fe27b07029291a48a31351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555645"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Início rápido: Conectar um aplicativo Rust à API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global. O exemplo apresentado neste artigo é um aplicativo simples baseado em linha de comando que usa o [driver Rust para MongoDB](https://github.com/mongodb/mongo-rust-driver). Como a API do Azure Cosmos DB para MongoDB é [compatível com o protocolo de transmissão do MongoDB](./mongodb-introduction.md#wire-protocol-compatibility), é possível que qualquer driver cliente do MongoDB se conecte a ela.

Você aprenderá a usar o driver Rust do MongoDB para interagir com a API do Azure Cosmos DB para MongoDB explorando as operações CRUD (criar, ler, atualizar, excluir) implementadas no código de exemplo. Por fim, você pode executar o aplicativo localmente para vê-lo em ação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure. Use também o [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com a cadeia de conexão `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Rust](https://www.rust-lang.org/tools/install) (versão 1.39 ou posterior)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Configurar o Azure Cosmos DB

Para configurar uma conta do Azure Cosmos DB, siga as instruções [aqui](create-mongodb-dotnet.md). O aplicativo precisará da cadeia de conexão do MongoDB, que você pode buscar usando o portal do Azure. Para obter detalhes, confira [Obter a cadeia de conexão do MongoDB para personalização](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Executar o aplicativo

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Execute os comandos a seguir para clonar o repositório de exemplo.

1. Abra um prompt de comando, crie uma pasta chamada `git-samples` e, em seguida, feche o prompt de comando.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

1. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Compilar o aplicativo

Para criar o binário:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Configurar o aplicativo 

Exporte a cadeia de conexão, o banco de dados MongoDB e os nomes de coleção como variáveis de ambiente. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> A opção `ssl=true` é importante devido aos requisitos do Cosmos DB. Para obter mais informações, confira [Requisitos de cadeia de conexão](connect-mongodb-account.md#connection-string-requirements).
>

Para a variável de ambiente `MONGODB_URL`, substitua os espaços reservados por `<COSMOSDB_ACCOUNT_NAME>` e `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: O nome da conta do Azure Cosmos DB que você criou
- `<COSMOSDB_PASSWORD>`: A chave do banco de dados extraída na etapa anterior

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Você pode escolher seus valores preferenciais para `MONGODB_DATABASE` e `MONGODB_COLLECTION` ou deixá-los como estão.

Para executar o aplicativo, altere para a pasta correta (na qual reside o binário do aplicativo):

```bash
cd target/release
```

Para criar um `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Se for bem-sucedido, você verá uma saída com o `_id` do MongoDB do documento recém-criado:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Criar outro `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Listar todos os `todo`s

```bash
./todo list all
```

Você deve ver os que acabou de adicionar:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Para atualizar o status de um `todo` (por exemplo, altere-o para o status `completed`), use a ID de `todo`, como:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Listar somente os `todo`s concluídos

```bash
./todo list completed
```

Você deve ver aquele que acabou de atualizar

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Excluir um `todo` usando sua ID

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Listar os `todo`s a serem confirmados

```bash
./todo list all
```

O `todo` que você acabou de excluir não deve estar presente.

### <a name="view-data-in-data-explorer"></a>Exibir dados no Data Explorer

Os dados armazenados no Azure Cosmos DB ficam disponíveis para exibição e consulta no portal do Azure.

Para exibir, consultar e trabalhar com os dados de usuário criados na etapa anterior, faça logon no [portal do Azure](https://portal.azure.com) no seu navegador da Web.

Na caixa Pesquisa superior, insira **Azure Cosmos DB**. Quando a folha da conta do Cosmos abrir, selecione sua conta do Cosmos. No painel de navegação esquerdo, clique em **Data Explorer**. Expanda a coleção no painel Coleções e, em seguida, será possível exibir os documentos na coleção, consultar os dados e até mesmo criar e executar gatilhos, UDFs e procedimentos armazenados.

## <a name="review-the-code-optional"></a>Examinar o código (opcional)

Se você estiver interessado em saber como o aplicativo funciona, poderá examinar os snippets de código nesta seção. Os snippets a seguir são extraídos do arquivo `src/main.rs`.

A função `main` é o ponto de entrada para o aplicativo `todo`. Ela espera que a URL de conexão da API do Azure Cosmos DB para MongoDB seja fornecida pela variável de ambiente `MONGODB_URL`. Uma instância de `TodoManager` é criada, seguida por uma [expressão `match`](https://doc.rust-lang.org/book/ch06-02-match.html) delegada ao método `TodoManager` apropriado com base na operação escolhida pelo usuário: `create`, `update`, `list` ou `delete`.

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` é um `struct` que encapsula um [mongodb::sync::Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html). Quando você tenta criar uma instância de `TodoManager` usando a função `new`, ela inicia uma conexão com a API do Azure Cosmos DB para MongoDB.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

O mais importante é que `TodoManager` tem métodos para ajudar a gerenciar `todo`s. Vamos falar sobre cada um, individualmente.

O método `add_todo` usa uma descrição de `todo` fornecida pelo usuário e cria uma instância do struct `Todo`, que tem a aparência abaixo. A estrutura [serde](https://github.com/serde-rs/serde) é usada para mapear (serializar/desserializar) dados BSON para instâncias de structs `Todo`. Observe como os atributos de campo `serde` são usados para personalizar o processo de serialização/desserialização. Por exemplo, o campo `todo_id` no `struct` Todo é um `ObjectId` e é armazenado no MongoDB como `_id`.

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) aceita um [Documento](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) que representa os detalhes de `todo` a serem adicionados. Observe que a conversão de `Todo` em um `Document` é um processo em duas etapas, realizado usando uma combinação de [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) e [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection.find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) é usado para obter *todos* os `todo`s ou os filtra com base no status fornecido pelo usuário (`pending` ou `completed`). Observe como, no loop `while`, cada `Document` obtido como resultado da pesquisa é convertido em um struct `Todo` usando [bson::from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). Esse é o oposto do que foi feito no método `add_todo`.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

O status de `todo` poderá ser atualizado (de `pending` para `completed` ou vice-versa). O `todo` é convertido em um [bson::oid::ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) que é usado pelo método[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) para localizar o documento que precisa ser atualizado.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

A exclusão de um `todo` é simples usando o método [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one).


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta da API do MongoDB para Azure Cosmos DB usando o Azure Cloud Shell e criar e executar um aplicativo Rust de linha de comando para gerenciar os `todo`s. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
