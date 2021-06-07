---
title: Criar um aplicativo Go com a API do Cassandra para Azure Cosmos DB usando o cliente do gocql
description: Este guia de início rápido mostra como usar um cliente do Go para interagir com a API do Cassandra para Azure Cosmos DB
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93076394"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Início Rápido: Criar um aplicativo Go com o cliente do `gocql` para gerenciar dados da API do Cassandra para Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global. Neste guia de início rápido, você começará criando uma conta da API do Cassandra para Azure Cosmos DB. Em seguida, você executará um aplicativo Go para criar um keyspace do Cassandra, uma tabela e executar algumas operações. Esse aplicativo Go usa o [gocql](https://github.com/gocql/gocql), que é um cliente do Cassandra para a linguagem Go. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) sem uma assinatura do Azure.
- Ter [Go](https://golang.org/) instalado em seu computador e ter conhecimentos práticos de Go.
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Para criar um banco de dados, crie uma conta do Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Comece clonando o aplicativo do GitHub.

1. Abra um prompt de comando e crie uma pasta chamada `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do Git, como git bash. Use o comando `cd` para mudar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se estiver interessado em saber como o código cria os recursos de banco de dados, examine os snippets de código a seguir. Caso contrário, vá direto para [Executar o aplicativo](#run-the-application)

A função `GetSession` (parte de `utils\utils.go`) retorna uma [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session), que é usada para executar operações de cluster, como inserir, localizar etc.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

O host do Cassandra para Azure Cosmos DB é passado para a função [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) a fim de obter um struct [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) que é configurado para usar o nome de usuário, a senha, a porta e a versão do TLS apropriada ([requisito de segurança de criptografia HTTPS/SSL/TLS](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database))

A função `GetSession` é então chamada por meio da função `main` (`main.go`).

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

As informações de conectividade e as credenciais são aceitas na forma de variáveis de ambiente (resolvidas no método `init`)

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Em seguida, elas são usadas para executar várias operações (parte de `operations\setup.go`) no Azure Cosmos DB começando com a criação de `keyspace` e `table`.

Como o nome sugere, a função `DropKeySpaceIfExists` remove o `keyspace` somente se ele existe.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

A função `CreateKeySpace` é usada para criar o `keyspace` (`user_profile`)

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Isso é seguido da criação da tabela (`user`), que é responsável pela função `CreateUserTable`

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

Depois que o keyspace e a tabela são criados, invocamos operações CRUD (parte de `operations\crud.go`). 

`InsertUser` é usado para criar um `User`. Ele define as informações do usuário (ID, nome e cidade) como os argumentos de consulta usando [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` é usado para pesquisar um usuário (`model\user.go`) usando uma ID de usuário específica, enquanto [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) associa os atributos de usuário (retornados pelo Cassandra) a variáveis individuais (`userid`, `name` e `city`); essa é apenas uma das maneiras de usar o resultado obtido como o resultado da consulta de pesquisa

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` é usado para buscar todos os usuários. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) é usado como uma função abreviada para obter todas as informações do usuário na forma de uma fatia de `map`s. Imagine cada `map` como pares chave-valor, em que o nome da coluna (por exemplo, `user_id`) é a chave junto com o respectivo valor.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Cada `map` das informações do usuário é convertido em um `User` usando a função `mapToUser` que apenas extrai o valor da respectiva coluna e a usa para criar uma instância do struct `User`

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Executar o aplicativo

Como mencionado anteriormente, o aplicativo aceita a conectividade e as credenciais no formato de variáveis de ambiente. 

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Ver e copiar detalhes da página Cadeia de Conexão no portal do Azure":::

Copie os valores para os atributos a seguir (`CONTACT POINT`, `PORT`, `USERNAME` e `PRIMARY PASSWORD`) e defina-os com as respectivas variáveis de ambiente

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

Na janela do terminal, mude para a pasta correta. Por exemplo:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. No terminal, execute o comando a seguir para iniciar o aplicativo.

```shell
go run main.go
```

3. A janela do terminal exibe notificações para as várias operações, incluindo configuração de keyspace e de tabela, criação de usuário etc.

4. No portal do Azure, abra **Data Explorer** para consultar, modificar e trabalhar com esses novos dados. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Exibir os dados no Data Explorer – Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar uma conta do Azure Cosmos DB com a API do Cassandra e executar um aplicativo Go que cria um banco de dados e um contêiner do Cassandra. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)