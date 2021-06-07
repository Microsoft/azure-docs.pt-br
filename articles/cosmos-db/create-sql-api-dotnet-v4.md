---
title: Gerenciar recursos da API SQL do Azure Cosmos DB usando o SDK do .NET V4
description: Início Rápido para criar um aplicativo de console usando o SDK do .NET V4 para gerenciar recursos de conta da API SQL do Azure Cosmos DB.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 224a1b67ff0282c216763229593fcfed81d7567b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090096"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Início Rápido: Criar um aplicativo de console usando o SDK do .NET V4 para gerenciar recursos de conta da API SQL do Azure Cosmos DB.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK do Java v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Introdução à biblioteca de clientes da API do SQL do Azure Cosmos DB para .NET. Siga as etapas neste documento para instalar o pacote .NET V4 (Azure.Cosmos), criar um aplicativo e testar o código de exemplo para operações CRUD básicas nos dados armazenados no Azure Cosmos DB. 

O Azure Cosmos DB é o banco de dados NoSQL rápido da Microsoft com APIs abertas para qualquer escala. É possível usar o Azure Cosmos DB para criar e consultar rapidamente chaves/valores, documentos e bancos de dados de grafos. Use a biblioteca de clientes da API do SQL do Azure Cosmos DB para .NET para:

* Criar um banco de dados e um contêiner do Azure Cosmos
* Adicionar dados de exemplo ao contêiner
* Consultar os dados 
* Excluir o banco de dados

[Código-fonte da biblioteca](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/) ou você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, sem ônus e sem compromisso. 
* [SDK do .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core). Você pode verificar qual versão está disponível em seu ambiente executando `dotnet --version`.

## <a name="setting-up"></a>Configurando

Esta seção orienta a criação de uma conta do Azure Cosmos e a configuração de um projeto que usa a biblioteca de cliente da API do SQL do Azure Cosmos DB para .NET a fim de gerenciar recursos. O código de exemplo descrito neste artigo cria o banco de dados `FamilyDatabase` e os membros da família (cada membro da família é um item) dentro desse banco de dados. Cada membro da família tem propriedades como `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. A propriedade `LastName` é usada como a chave de partição para o contêiner. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Criar uma conta do Azure Cosmos

Se usar a opção [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) para criar uma conta do Azure Cosmos, você deverá criar uma conta do Azure Cosmos DB do tipo **API do SQL**. Uma conta de teste do Azure Cosmos DB já foi criada para você. Você não precisa criar a conta explicitamente, portanto, você pode ignorar esta seção e ir para a próxima.

Se tiver sua própria assinatura do Azure ou tiver criado uma gratuitamente, você deverá criar uma conta do Azure Cosmos explicitamente. O código a seguir criará uma conta do Azure Cosmos com consistência de sessão. A conta é replicada no `South Central US` e no `North Central US`.  

Você pode usar o Azure Cloud Shell para criar a conta do Azure Cosmos. O Azure Cloud Shell é um shell interativo, autenticado e acessível pelo navegador para o gerenciamento de recursos do Azure. Ele dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha, seja com o Bash ou o PowerShell. Para este guia de início rápido, escolha o modo **Bash**. O Azure Cloud Shell também requer uma conta de armazenamento, a qual você pode criar quando solicitado.

Selecione o botão **Experimentar** ao lado do código a seguir, escolha o modo **Bash**, selecione **criar uma conta de armazenamento** e faça logon no Cloud Shell. Em seguida, copie e cole o código a seguir no Azure Cloud Shell e execute-o. O nome da conta do Azure Cosmos deve ser globalmente exclusivo, certifique-se de atualizar o valor `mysqlapicosmosdb` antes de executar o comando.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

A criação da conta do Azure Cosmos demora um pouco. Quando a operação for bem-sucedida, você verá a saída de confirmação. Após o comando ser concluído com êxito, entre no [portal do Azure](https://portal.azure.com/) e verifique se a conta do Azure Cosmos com o nome especificado existe. Você pode fechar a janela de Azure Cloud Shell após a criação do recurso. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Criar um novo aplicativo .NET

Crie um novo aplicativo .NET em seu IDE ou editor preferido. Abra o prompt de comando do Windows ou uma janela do Terminal no computador local. Você executará todos os comandos nas próximas seções no prompt de comando ou no terminal.  Execute o novo comando dotnet a seguir para criar um novo aplicativo com o nome `todo`. O parâmetro --langVersion define a propriedade LangVersion no arquivo de projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

   ```bash
   cd todo
   dotnet build
   ```

A saída esperada da compilação deve ser algo como:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalar pacote Azure Cosmos DB

Ainda no diretório do aplicativo, instale a biblioteca de clientes do Azure Cosmos DB para .NET Core usando o comando para adicionar pacote do dotnet.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copiar suas credenciais da conta do Azure Cosmos no portal do Azure

O aplicativo de exemplo precisa autenticar sua conta do Azure Cosmos. Para autenticar, você deve passar as credenciais da conta do Azure Cosmos para o aplicativo. Obtenha suas credenciais da conta do Azure Cosmos seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Navegue até a conta do Azure Cosmos.

1. Abra o painel **Chaves** e copie o **URI** e a **CHAVE PRIMÁRIA** da sua conta. Você adicionará os valores do URI e das chaves em uma variável de ambiente da próxima etapa.

## <a name="object-model"></a><a id="object-model"></a>Modelo de objeto

Antes de começar a criar o aplicativo, vamos examinar a hierarquia de recursos no Azure Cosmos DB e o modelo de objeto usado para criar e acessar esses recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta do Azure Cosmos 
* Bancos de dados 
* Contêineres 
* Itens

Para saber mais sobre a hierarquia de diferentes entidades, confira o artigo [Trabalhando com bancos de dados, contêineres e itens no Azure Cosmos DB](account-databases-containers-items.md). Use as seguintes classes .NET para interagir com esses recursos:

* CosmosClient: esta classe fornece a representação lógica do lado do cliente para o serviço do Azure Cosmos DB. Esse objeto do cliente é usado para configurar e executar solicitações no serviço.
* CreateDatabaseIfNotExistsAsync: esse método cria (se não existir) ou obtém (se já existir) um recurso de banco de dados como uma operação assíncrona. 
* CreateContainerIfNotExistsAsync: esse método cria (se não existir) ou obtém (se já existir) um contêiner como uma operação assíncrona. Você pode verificar o código de status da resposta para determinar se o contêiner foi criado recentemente (201) ou se um contêiner existente foi retornado (200). 
* CreateItemAsync: esse método cria um item dentro do contêiner.
* UpsertItemAsync: esse método cria um item dentro do contêiner (se não existir) ou substitui o item (se já existir). 
* GetItemQueryIterator: esse método cria uma consulta para itens em contêiner em um banco de dados do Azure Cosmos usando uma instrução SQL com valores parametrizados. 
* DeleteAsync: exclui o banco de dados especificado de sua conta do Azure Cosmos. O método `DeleteAsync` exclui somente o banco de dados.

 ## <a name="code-examples"></a><a id="code-examples"></a>Exemplos de código

O código de exemplo descrito neste artigo cria um banco de dados de família em Azure Cosmos DB. O banco de dados da família contém detalhes da família, como nome, endereço, local, pais associados, filhos e animais de estimação. Antes de preencher os dados de sua conta do Azure Cosmos, defina as propriedades de um item da família. Crie uma nova classe chamada `Family.cs` no nível raiz do seu aplicativo de exemplo e adicione o seguinte código a ela:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Adicionar usando as diretivas e definir o objeto de cliente

No diretório do projeto, abra o arquivo `Program.cs` em seu editor e adicione o seguinte usando as diretivas na parte superior do seu aplicativo:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Adicione as variáveis globais a seguir à classe `Program`. Elas incluirão o ponto de extremidade e as chaves de autorização, o nome do banco de dados e o contêiner que você criará. Certifique-se de substituir os valores de chaves de autorização e o ponto de extremidade de acordo com seu ambiente. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Por fim, substitua o método `Main`:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Criar um banco de dados 

Defina o método `CreateDatabaseAsync` dentro da classe `program.cs`. Esse método cria o `FamilyDatabase`, caso ainda não exista.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Criar um contêiner

Defina o método `CreateContainerAsync` dentro da classe `Program`. Esse método cria o `FamilyContainer`, caso ainda não exista. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Criar um item

Crie um item de família adicionando o método `AddItemsToContainerAsync` com o código a seguir. Você pode usar os métodos `CreateItemAsync` ou `UpsertItemAsync` para criar um item:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, você pode executar uma consulta para obter os detalhes da família "Martins". O código a seguir mostra como executar a consulta usando diretamente a consulta SQL. A consulta SQL para obter os detalhes da família "Martins" é: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Defina o método `QueryItemsAsync` dentro da classe `Program` e adicione o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Substituir um item 

Leia um item de família e atualize-o adicionando o método `ReplaceFamilyItemAsync` com o código a seguir.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Excluir um item 

Exclua um item de família adicionando o método `DeleteFamilyItemAsync` com o código a seguir.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Excluir o banco de dados 

Por fim, você pode excluir o banco de dados adicionando o método `DeleteDatabaseAndCleanupAsync` com o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Depois de adicionar todos os métodos necessários, salve o arquivo `Program`. 

## <a name="run-the-code"></a>Executar o código

Em seguida, compile e execute o aplicativo para criar os recursos do Azure Cosmos DB.

   ```bash
   dotnet run
   ```

A saída a seguir é gerada quando você executa o aplicativo. Você também pode entrar no portal do Azure e validar que os recursos foram criados:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Você pode validar que os dados foram criados entrando no portal do Azure e olhando os itens necessários em sua conta do Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessária, você poderá usar o CLI do Azure ou o Azure PowerShell para remover a conta do Azure Cosmos e o grupo de recursos correspondente. O comando a seguir mostra como excluir o grupo de recursos usando o CLI do Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos, como criar um banco de dados e um contêiner usando o .NET Core. Agora você pode importar dados adicionais para sua conta do Azure Cosmos com as instruções no artigo a seguir. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)
