---
title: 'BD Cosmos do Azure: compilar um aplicativo de console da API do MongoDB com Golang e o portal do Azure | Microsoft Docs'
description: "Apresenta um exemplo de código Golang que pode ser usado para conectar e consultar o BD Cosmos do Azure"
services: cosmos-db
author: Durgaprasad-Budhwani
manager: jhubbard
editor: mimig1
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 07/21/2017
ms.author: mimig
ms.openlocfilehash: cb123107178f5e7c0207524c19331a6fa4658739
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-golang-and-the-azure-portal"></a>BD Cosmos do Azure: compilar um aplicativo de console da API do MongoDB com Golang e o portal do Azure

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB.

Este início rápido demonstra como usar um aplicativo [MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) existente escrito em [Golang](https://golang.org/) e como conectá-lo ao banco de dados do BD Cosmos do Azure, que dá suporte a conexões de cliente do MongoDB.

Em outras palavras, o aplicativo Golang só sabe que está se conectando a um banco de dados usando as APIs do MongoDB. Está claro para o aplicativo que os dados estão armazenados no BD Cosmos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) e um conhecimento básico sobre a linguagem [Go](https://golang.org/).
- Um IDE — [Gogland](https://www.jetbrains.com/go/) da Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) ou [Atom](https://atom.io/). Neste tutorial, estou usando Goglang.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Clone o aplicativo de exemplo e instale os pacotes necessários.

1. Crie uma pasta chamada CosmosDBSample dentro da pasta GOROOT\src, que é C:\Go\ por padrão.
2. Execute o comando a seguir usando uma janela do terminal git como git bash para clonar o repositório de exemplo na pasta CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Execute o comando a seguir para obter o pacote mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

O driver [mgo](http://labix.org/mgo) (pronuncia-se *mango*) é um driver [MongoDB](http://www.mongodb.org/) para a [linguagem Go](http://golang.org/) que implementa uma seleção bem testada e avançada de recursos em uma API muito simples seguindo expressões Go padrão.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. Clique em **Início rápido** no menu de navegação à esquerda e clique em **Outros** para exibir as informações de cadeia de conexão exigidas pelo aplicativo Go.

2. No Goglang, abra o arquivo main.go no diretório GOROOT\CosmosDBSample e atualize as linhas de código a seguir usando as informações de cadeia de conexão do portal do Azure, conforme mostrado na captura de tela a seguir. 

    O Nome do banco de dados é o prefixo do valor **Host** no painel de cadeia de conexão de portal do Azure. Para a conta mostrada na imagem abaixo, o nome do banco de dados é golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Painel Início rápido, guia Outros no portal do Azure mostrando as informações de cadeia de conexão](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Salve o arquivo main.go.

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida revisão do que está acontecendo no arquivo main.go. 

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Conectando o aplicativo Go para o BD Cosmos do Azure

O BD Cosmos do Azure dá suporte ao MongoDB habilitado para SSL. Para se conectar a um MongoDB habilitado para SSL, você precisa definir a função **DialServer** em [mgo.DialInfo](http://gopkg.in/mgo.v2#DialInfo)e usar a função [tls.*Dial* ](http://golang.org/pkg/crypto/tls#Dial) para realizar a conexão.

O trecho de código Golang a seguir se conecta ao aplicativo Go com a API MongoDB do Cosmos DB do Azure. A classe *DialInfo* contém opções para estabelecer uma sessão com um cluster do MongoDB.

```go
// DialInfo holds options for establishing a session with a MongoDB cluster.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to our Azure Cosmos DB MongoDB database.
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect to mongo, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

O método **mgo.Dial()** é usado quando não há nenhuma conexão SSL. Para uma conexão SSL, o método **mgo.DialWithInfo()** é necessário.

Uma instância do objeto **DialWIthInfo{}** é usada para criar o objeto de sessão. Quando a sessão é estabelecida, você pode acessar a coleção usando o trecho de código abaixo:

```go
collection := session.DB(“database”).C(“package”)
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Criar um documento

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Consultar ou ler um documento

O Azure Cosmos DB dá suporte a consultas avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra uma consulta que pode ser executada em documentos em sua coleção.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Atualizar um documento

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Excluir um documento

O Azure Cosmos DB dá suporte à exclusão de documentos JSON.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Execute o aplicativo

1. Em Goglang, verifique se seu GOPATH (disponível em **Arquivo**, **Configurações**, **Go**, **GOPATH**) inclui o local no qual o gopkg foi instalado, que é PERFILDOUSUÁRIO/go por padrão. 
2. Comente as linhas que excluem o documento, linhas 91 a 96, para que você possa ver o documento depois de executar o aplicativo.
3. No Goglang, clique em **Executar**e em **Run 'Build main.go and run'**.

    O aplicativo termina e exibe a descrição do documento criado em [Criar um documento](#create-document).
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Goglang mostrando a saída do aplicativo](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Revisar o documento no Data Explorer

Volte para o portal do Azure a fim de ver o documento no Data Explorer.

1. Clique em **Data Explorer (Versão prévia)** no menu de navegação à esquerda, expanda **golang-coach**, **pacote**e clique em **Documentos**. Na guia **Documentos** , clique na \_id para exibir o documento no painel direito. 

    ![Data Explorer mostrando o documento recém-criado](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Você pode trabalhar com o documento em linha e clicar em **Atualizar** para salvá-lo. Você também pode excluir o documento ou criar novos documentos ou consultas.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse início rápido no portal do Azure com as seguintes etapas:

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure e executar um aplicativo Golang usando a API para MongoDB. Agora, é possível importar outros dados para sua conta do BD Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados no BD Cosmos do Azure para a API do MongoDB](mongodb-migrate.md)
