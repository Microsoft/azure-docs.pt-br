---
title: API de Tabela do Azure Cosmos DB usando SDK do .NET Standard
description: Saiba como armazenar e consultar os dados estruturados na conta da API de Tabela do Azure Cosmos DB
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: f4817e53cb36bc96add19d855750a73a151b4891
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772290"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Introdução à API de Tabela do Azure Cosmos DB e armazenamento de Tabelas do Azure usando SDK do .NET
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

É possível usar a API de Tabela do Azure Cosmos DB ou o armazenamento de Tabelas do Azure para armazenar dados NoSQL estruturados na nuvem, fornecendo um repositório de chave/atributo com um esquema sem design. Como a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas são sem esquema, é fácil adaptar seus dados à medida que as necessidades do aplicativo evoluem. É possível usar a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas para armazenar conjuntos de dados flexíveis como dados de usuário para aplicativos Web, catálogos de endereços, informações de dispositivo ou outros tipos de metadados que o serviço exigir. 

Este tutorial descreve um exemplo que mostra como usar a [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) com cenários de API de Tabela do Azure Cosmos DB e Armazenamento de Tabelas do Azure. Você deverá usar a conexão específica para o serviço do Azure. Esses cenários são explorados usando exemplos em C# que ilustram como criar tabelas, inserir/atualizar dados, consultar dados e excluir tabelas.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este exemplo com sucesso:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - Essa biblioteca atualmente está disponível para estrutura .NET e .NET Standard. 

* [Conta de API de Tabela do Azure Cosmos DB](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta de API de Tabele do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Criar um projeto de console do .NET

No Visual Studio, crie um novo aplicativo de console do .NET. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. É possível usar a Biblioteca de Tabelas do Azure Cosmos DB em qualquer tipo de aplicativo .NET, incluindo um serviço de nuvem do Azure ou um aplicativo Web, além de aplicativos para área de trabalho e dispositivos móveis. Neste guia, usamos um aplicativo de console para simplificar.

1. Selecione **Arquivo** > **Novo** > **Projeto**.

1. Escolha **Aplicativo de Console (.NET Core)** e, em seguida, selecione **Avançar**.

1. No campo **Nome do projeto**, insira um nome para seu aplicativo, por exemplo, **CosmosTableSamples**. (Você pode fornecer um nome diferente conforme necessário.)

1. Selecione **Criar**.

Todos os exemplos de código neste exemplo podem ser adicionados ao método Main() do arquivo **Program.cs** do aplicativo do console.

## <a name="install-the-required-nuget-package"></a>Instalar o pacote NuGet necessário

Para obter o pacote NuGet, siga estas etapas:

1. Clique com o botão direito do mouse no seu projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.

1. Pesquise online por [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json), [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) e selecione **Instalar** para instalar a Biblioteca de Tabelas do Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

1. Do [portal do Azure](https://portal.azure.com/), navegue até sua conta do Azure Cosmos ou conta do Armazenamento de Tabelas. 

1. Abra o painel **Cadeia de Conexão** ou **Chaves de acesso** painel. Use o botão de cópia no lado direito da tela para copiar a **CADEIA DE CONEXÃO PRIMÁRIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Exibir e copiar a CADEIA DE CONEXÃO PRIMÁRIA no painel Cadeia de Conexão":::
   
1. Para configurar a cadeia de conexão, no Visual Studio clique com o botão direito do mouse no projeto **CosmosTableSamples**.

1. Selecione **Adicionar** e, em seguida, **Novo Item**. Crie um novo arquivo **Settings.json** com o tipo de arquivo como **Arquivo de Configuração JSON do TypeScript**. 

1. Substitua o código no arquivo Settings.json pelo seguinte código e atribua a cadeia de conexão primária:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **AppSettings.cs**.

1. Adicione o seguinte código ao arquivo AppSettings.cs. Esse arquivo lê a cadeia de conexão do arquivo Settings.json e a atribui ao parâmetro de configuração:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>Analisar e validar os detalhes da conexão

1. Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **Common.cs**. Você gravará código para validar os detalhes da conexão e criar uma tabela dentro dessa classe.

1. Defina um método `CreateStorageAccountFromConnectionString`, conforme mostrado abaixo. Esse método analisará os detalhes da cadeia de conexão e validará se os detalhes da chave de conta e do nome da conta fornecidos no arquivo "Settings.json" são válidos.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Criar uma tabela 

A classe [CloudTableClient](/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) permite que você recupere as tabelas e entidades armazenadas no Armazenamento de tabelas. Como não temos tabelas na conta de API de Tabela do Cosmos DB, adicionaremos o método `CreateTableAsync` para a classe **Common.cs** para criar uma tabela:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

Caso você receba um erro "exceção 503 de serviço não disponível", é possível que as portas exigidas do modo de conectividade estejam bloqueadas por um firewall. Para corrigir esse problema, abra as portas necessárias ou use a conectividade do modo de gateway, conforme mostrado no código a seguir:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definir a entidade 

As entidades são mapeadas para objetos C# usando uma classe personalizada derivada de [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da sua entidade.

Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Nova Pasta** e nomeie-a como **Modelo**. Na pasta Modelo, adicione uma classe nomeada **CustomerEntity.cs** e adicione o código a seguir.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Esse código define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes, mas usar chaves de partição diferentes permite uma escalabilidade maior de operações paralelas. As entidades que serão armazenadas em tabelas deverão ser de um tipo com suporte, por exemplo, derivado da classe [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). As propriedades de entidade que você gostaria de armazenar em uma tabela precisam ser propriedades públicas do tipo e dar suporte à obtenção e à definição dos valores. Além disso, o tipo de entidade deverá expor um construtor sem parâmetros.

## <a name="insert-or-merge-an-entity"></a>Inserir ou mesclar uma entidade

O exemplo de código a seguir cria um objeto de entidade e o adiciona à tabela. O método InsertOrMerge dentro da classe [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) é usado para inserir ou mesclar uma entidade. O método [CloudTable.ExecuteAsync](/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync) é chamado para executar a operação. 

Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **SamplesUtils.cs**. Essa classe armazena todo o código necessário para executar operações CRUD nas entidades. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Obter uma entidade de uma partição

É possível obter entidade de uma partição usando o método Recuperar na classe [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation). O exemplo de código a seguir obtém a chave de linha da chave de partição, o email e o número de telefone de uma entidade do cliente. Este exemplo também imprime as unidades de solicitação consumidas para consultar a entidade. Para consultar uma entidade, acrescente o seguinte código ao arquivo **SamplesUtils.cs**:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Excluir uma entidade

Você pode excluir facilmente uma entidade após a recuperação usando o mesmo padrão mostrado para a atualização de uma entidade. O código a seguir recupera e exclui uma entidade de cliente. Para excluir uma entidade, anexe o seguinte código ao arquivo **SamplesUtils.cs**: 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="execute-the-crud-operations-on-sample-data"></a>Executar as operações CRUD em dados de exemplo

Após definir os métodos para criar tabela, inserir ou mesclar entidades, execute esses métodos nos dados de exemplo. Para fazer isso, clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **BasicSamples.cs**, em seguida, adicione o seguinte código. Esse código cria uma tabela e adiciona entidades a ela.

Se você não quiser excluir a entidade e a tabela ao final do projeto, comente os métodos `await table.DeleteIfExistsAsync()` e `SamplesUtils.DeleteEntityAsync(table, customer)` do código a seguir. É melhor comentar esses métodos e validar os dados antes de excluir a tabela.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

O código anterior cria uma tabela que começa com “demo” e o GUID gerado é anexado ao nome da tabela. Em seguida, adiciona uma entidade de cliente com nome e sobrenome como "Valter Lima" e, posteriormente atualiza o número de telefone desse usuário. 

Neste tutorial, você compilou um código para executar operações CRUD básicas nos dados armazenados na conta de API de Tabela. Também é possível executar operações avançadas como – inserir dados em lotes, consultar todos os dados em uma partição, consultar um intervalo de dados em uma partição, listar tabelas na conta cujos nomes iniciam com o prefixo especificado. Você pode baixar o formulário de exemplo completo [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) do repositório GitHub. A classe [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/main/CosmosTableSamples/AdvancedSamples.cs) tem mais operações que podem ser executadas nos dados.  

## <a name="run-the-project"></a>Executar o projeto

No projeto **CosmosTableSamples**. Abra a classe chamada **Program.cs** e adicione o código a seguir a ela para chamar BasicSamples quando o projeto for executado.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Agora, compile a solução e pressione F5 para executar o projeto. Quando o projeto for executado, a seguinte saída no prompt de comando será exibida:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Saída do prompt de comando":::

Se você receber um erro informando que o arquivo Settings.json não pode ser encontrado ao executar o projeto, será possível resolvê-lo adicionando a seguinte entrada XML às configurações do projeto. Clique com o botão direito do mouse em CosmosTableSamples, selecione Editar CosmosTableSamples.csproj e adicione o seguinte itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Agora, você pode entrar no portal do Azure e verificar se os dados existem na tabela. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Resultados no portal":::

## <a name="next-steps"></a>Próximas etapas

Você pode avançar para o próximo tutorial e saber como migrar dados para conta de API de Tabela do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Migrar dados para a API de Tabela do Azure Cosmos DB](../cosmos-db/table-import.md)
