---
title: Usar comandos e recursos de notebook integrado nos notebooks C# do Azure Cosmos DB (versão prévia)
description: Saiba como usar comandos e recursos internos para realizar operações comuns usando os notebooks C# internos do Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340462"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Usar comandos e recursos de notebook integrado nos notebooks C# do Azure Cosmos DB (versão prévia)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Os notebooks Jupyter internos do Azure Cosmos DB permitem que você analise e visualize os dados do portal do Azure. Este artigo descreve como usar os recursos e comandos do notebook integrado para realizar operações comuns nos notebooks C#.

## <a name="install-a-new-nuget-package"></a>Instalar um novo pacote NuGet
Depois de habilitar o suporte para notebook nas contas do Azure Cosmos, você pode abrir um novo notebook e instalar um pacote.

Em uma nova célula de código, insira e execute o código a seguir, substituindo ``PackageToBeInstalled`` pelo pacote NuGet desejado e ``optionalVersion`` por uma versão específica do pacote, se desejado. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

Você pode instalar vários pacotes NuGet na mesma célula. Os pacotes estarão disponíveis para uso em qualquer notebook, no espaço de trabalho da conta do Azure Cosmos. 

Atualmente, o espaço de trabalho de notebooks C# não oferece suporte à resolução recursiva de pacotes NuGet. Se um pacote NuGet tiver dependências em outros pacotes NuGet não instalados no momento, você precisará referenciá-los explicitamente junto com o pacote pai.

> [!TIP]
> Se o seu notebook exigir um pacote personalizado, recomendamos que você adicione uma célula ao seu notebook para instalar o pacote e torná-lo a primeira célula. Isso reduz a chance de conflitos com outros pacotes que o Azure Cosmos DB carrega por padrão. Também é fácil reinstalar os pacotes se você [redefinir o espaço de trabalho](#reset-notebooks-workspace), o que remove todos os pacotes. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Usar o SDK do .NET do Azure Cosmos DB interno
A versão 3 da [API do SDK do .NET para SQL do Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3) está instalada e incluída no ambiente de notebook da conta do Azure Cosmos.

Crie uma instância do ``CosmosClient`` para executar qualquer operação do SDK. 

Por exemplo:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Para saber mais, confira os [exemplos de SDK do .NET V3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> O SDK do .NET do Azure Cosmos DB interno só é compatível com contas de API do SQL (Core). Para outras APIs, você precisará [instalar o driver do .NET relevante](#install-a-new-nuget-package) que corresponda à API. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Definir opções personalizadas usando ``CosmosClientOptions``
Para obter mais flexibilidade, você pode definir a propriedade personalizada ``CosmosClientOptions`` e passá-la em sua instância ``CosmosClient``. Você pode usar essa propriedade para:

- Definir um nome de aplicativo no sufixo user-agent para incluí-lo em todas as solicitações.
- Definir a região preferida a ser usada ao executar operações no serviço.
- Definir uma política de repetição personalizada para manipular solicitações limitadas por taxa.

Consulte no artigo [referência da API CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) todas as configurações com suporte. Veja a seguir um exemplo que mostra como definir a propriedade `cosmosClientOptions`:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Acessar as variáveis de ponto de extremidade e chave primária da conta
Você pode acessar o ponto de extremidade interno e a chave da conta do Azure Cosmos em que o seu notebook existe.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> As variáveis ``Cosmos.Key`` e ``Cosmos.Endpoint`` só se aplicam à API do SQL. Para outras APIs, localize o ponto de extremidade e a chave na folha **cadeias de conexão** ou **chaves** da sua conta do Azure Cosmos.  

## <a name="print-console-output-in-c-code"></a>Imprimir saída do console no código C#
No seu código C#, você pode usar a sintaxe Display.AsMarkdown() com [ interpolação de cadeia de caracteres](/dotnet/csharp/language-reference/tokens/interpolated) para imprimir a saída de console que será exibida ao executar a célula. 

Por exemplo: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> No momento, a sintaxe dConsole.WriteLine() não tem suporte em notebooks C#. Use Display.AsMarkdown para imprimir a saída de console do seu programa. 

## <a name="use-built-in-nteract-data-explorer"></a>Usar o explorador de dados interno nteract
Você pode usar o [explorador de dados interno nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) para filtrar e visualizar uma coleção de itens. Em uma célula, coloque a variável que você deseja visualizar na última linha, que é exibida automaticamente em nteract ao executar a célula.

No exemplo *GetingStarted_Csharp.ipynb*, podemos imprimir a variável com nosso resultado, o ``telemetryEvents``. Confira o [notebook GettingStarted_Csharp.ipynb](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) para ver o exemplo inteiro. 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Célula de consulta do Csharp":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="Explorador de dados nteract":::

## <a name="use-built-in-dictionary-viewer"></a>Usar o visualizador de dicionário interno
Você pode usar o visualizador de dicionário interno para exibir uma variável. Em uma célula, coloque a variável que você deseja visualizar na última linha, que será exibida automaticamente quando a célula for executada.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Visualizador de dicionário interno":::

## <a name="upload-json-items-to-a-container"></a>Carregar itens JSON em um contêiner
Você pode usar o comando mágico ``%%upload`` para carregar dados de um arquivo JSON em um contêiner específico do Azure Cosmos. Use o comando a seguir para carregar os itens:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Substitua ``{database_id}`` e ``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do Azure Cosmos. 
- Substitua ``{url_location_of_file}`` pela localização do arquivo JSON. O arquivo deve ser uma matriz de objetos JSON válidos e deve ser possível acessá-lo pela Internet pública.

Por exemplo:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Com as estatísticas de saída, você pode calcular a RU/s efetiva usado para carregar os itens. Por exemplo, se 25.000 RUs foram consumidos em 38 segundos, a RU/s efetiva é 25.000 RUs/38 segundos = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Executar outro notebook no notebook atual 
Você pode usar o comando mágico ``%%run`` para executar outro notebook no espaço de trabalho do notebook atual. Use a sintaxe:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Substitua ``{notebookName}`` pelo nome do notebook que você deseja executar. O notebook deve estar no atual espaço de trabalho “Meus Notebooks”. 

## <a name="reset-notebooks-workspace"></a>Restaurar espaço de trabalho de notebook
Para restaurar as configurações padrão do espaço de trabalho de notebooks, selecione **Restaurar espaço de trabalho** na barra de comandos. Isso removerá todos os pacotes personalizados instalados e reiniciará o servidor Jupyter. Seus notebooks, arquivos e recursos de Cosmos do Azure não serão afetados.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Restaurar espaço de trabalho de notebook":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os benefícios dos [Notebooks do Jupyter do Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
- Saiba mais sobre a [API do SDK do .NET para SQL do Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
