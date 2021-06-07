---
title: Conectar o Azure Functions ao Azure Cosmos DB usando o Visual Studio Code
description: Saiba como conectar o Azure Functions a uma conta do Azure Cosmos DB adicionando uma associação de saída ao seu projeto do Visual Studio Code.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023241"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Conectar o Azure Functions ao Azure Cosmos DB usando o Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostrará de que modo usar o Visual Studio Code para conectar o [Azure Cosmos DB](../cosmos-db/introduction.md) à função criada no artigo anterior do guia de início rápido. A associação de saída adicionada a essa função gravará dados da solicitação HTTP em um documento JSON armazenado em um contêiner do Azure Cosmos DB. 

::: zone pivot="programming-language-csharp"
Antes de começar, você precisa concluir o [Guia de início rápido: Criar uma função C# no Azure usando o Visual Studio Code](create-first-function-vs-code-csharp.md). Se você já limpou os recursos ao final daquele artigo, percorra as etapas novamente para recriar o aplicativo de funções e recursos relacionados no Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Antes de começar, você precisa concluir o [Guia de início rápido: Criar uma função JavaScript no Azure usando o Visual Studio Code](create-first-function-vs-code-node.md). Se você já limpou os recursos ao final daquele artigo, percorra as etapas novamente para recriar o aplicativo de funções e recursos relacionados no Azure.  
::: zone-end

## <a name="configure-your-environment"></a>Configurar seu ambiente

Antes de começar, lembre-se de instalar a [extensão de Bancos de Dados do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) no Visual Studio Code.

## <a name="create-your-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

> [!IMPORTANT]
> O [Azure Cosmos DB sem servidor](../cosmos-db/serverless.md) já está disponível em versão prévia. Esse modo baseado em consumo torna o Azure Cosmos DB uma opção forte para cargas de trabalho sem servidor. Para usar o Azure Cosmos DB no modo sem servidor, escolha a opção **Sem Servidor** como o **Modo de capacidade** durante a criação de sua conta.

1. Em uma nova janela do navegador, entre no [Portal do Azure](https://portal.azure.com/).

2. Clique em **Criar um recurso** > **Bancos de dados** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="O painel Bancos de Dados do portal do Azure" border="true":::

3. Na página **Criar conta do Azure Cosmos DB**, insira as configurações da nova conta do Azure Cosmos DB. 
 
    Configuração|Valor|Descrição
    ---|---|---
    Subscription|*Sua assinatura*|Escolha a assinatura do Azure em que o Aplicativo de Funções foi criado no [artigo anterior](./create-first-function-vs-code-csharp.md).
    Grupo de recursos|*O seu grupo de recursos*|Escolha o grupo de recursos em que o Aplicativo de Funções foi criado no [artigo anterior](./create-first-function-vs-code-csharp.md).
    Nome da conta|*Insira um nome exclusivo*|Insira um nome exclusivo para identificar a conta do Azure Cosmos DB.<br><br>O nome da conta pode usar apenas letras minúsculas, números e hifens (-) e deve ter de 3 a 31 caracteres.
    API|Núcleo (SQL)|Selecione **Núcleo (SQL)** para criar um banco de dados de documentos em que será possível executar consultas usando uma sintaxe SQL. [Saiba mais sobre a API do SQL do Azure Cosmos DB](../cosmos-db/introduction.md).|
    Localização|*Selecionar a região mais próxima de sua localização*|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use a localização mais próxima de você ou de seus usuários para obter um acesso mais rápido aos dados.
    Modo de capacidade|Taxa de transferência provisionada ou sem servidor|Selecione **Sem servidor** para criar uma conta no modo [sem servidor](../cosmos-db/serverless.md). Selecione **Taxa de transferência provisionada** para criar uma conta no modo [taxa de transferência provisionada](../cosmos-db/set-throughput.md).<br><br>Caso esteja começando a usar o Azure Cosmos DB, escolha a opção **Sem Servidor**.

4. Clique em **Revisar + Criar**. Você pode ignorar as seções **Rede** e **Marcas**. 

5. Examine as informações de resumo e clique em **Criar**. 

6. Aguarde até que o novo Azure Cosmos DB seja criado, depois clique em **Acessar o recurso**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="A criação da conta do Azure Cosmos DB foi concluída" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Criar um banco de dados e um contêiner do Azure Cosmos DB

Em sua conta do Azure Cosmos DB, selecione **Data Explorer**, depois clique em **Novo Contêiner**. Crie um banco de dados chamado *my-database*, um contêiner chamado *my-container* e escolha `/id` como a [chave de partição](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Como criar um contêiner do Azure Cosmos DB no portal do Azure" border="true":::

## <a name="update-your-function-app-settings"></a>Atualizar as configurações do aplicativo de funções

No [artigo anterior do guia de início rápido](./create-first-function-vs-code-csharp.md), um aplicativo de funções foi criado no Azure. Neste artigo, atualize seu Aplicativo de Funções para gravar documentos JSON no contêiner do Azure Cosmos DB criado acima. Para se conectar à conta do Azure Cosmos DB, será necessário adicionar a cadeia de conexão dela às configurações do aplicativo. Depois baixe a nova configuração no arquivo local.settings.json para que seja possível se conectar à conta do Azure Cosmos DB quando ela estiver em execução localmente.

1. Localize a conta recém-criada do Azure Cosmos DB no Visual Studio Code. Clique com o botão direito do mouse em cima do nome dela, depois clique em **Copiar Cadeia de Conexão**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Como copiar a cadeia de conexão do Azure Cosmos DB" border="true":::

1. Pressione a tecla <kbd>F1</kbd> para abrir a paleta de comandos, depois pesquise e execute o comando `Azure Functions: Add New Setting...`.

1. Escolha o aplicativo de função que você criou no artigo anterior. Forneça as seguintes informações nos prompts:

    + **Inserir o nome de configuração do novo aplicativo**: digite `CosmosDbConnectionString`.

    + **Inserir o valor de "CosmosDbConnectionString"** : cole a cadeia de conexão de sua conta do Azure Cosmos DB, conforme copiado anteriormente.

1. Pressione a tecla <kbd>F1</kbd> novamente para abrir a paleta de comandos, depois pesquise e execute o comando `Azure Functions: Download Remote Settings...`. 

1. Escolha o aplicativo de função que você criou no artigo anterior. Selecione **Sim para todos** para substituir as configurações locais existentes. 

## <a name="register-binding-extensions"></a>Registrar as extensões de associação

Como você está usando uma associação de saída do Azure Cosmos DB, será necessário obter a extensão das associações correspondentes instalada antes de executar o projeto. 

::: zone pivot="programming-language-csharp"

Com exceção dos gatilhos de timer e HTTP, as associações são implementadas como pacotes de extensão. Execute o comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) a seguir na janela Terminal para adicionar o pacote de extensão Armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Seu projeto foi configurado para usar [pacotes de extensão](functions-bindings-register.md#extension-bundles), que instalam automaticamente um conjunto predefinido de pacotes de extensão. 

O uso de pacotes de extensão é habilitado no arquivo host.json na raiz do projeto, cuja aparência é semelhante à seguinte:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Agora é possível adicionar a associação de saída do Azure Cosmos DB ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

No Functions, cada tipo de associação requer que um `direction`, `type` e um `name` exclusivo seja definido no arquivo functions.json. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

::: zone pivot="programming-language-csharp"

Em um projeto de biblioteca de classes C#, as associações são definidas como atributos de associação no método de função. O arquivo *function.json* exigido por Functions é gerado automaticamente com base nesses atributos.

Abra o arquivo de projeto *HttpExample.cs* e adicione o seguinte parâmetro à definição do método `Run`:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

O parâmetro `documentsOut` é um tipo IAsyncCollector<T>. Ele representa uma coleção de documentos JSON que serão gravados no contêiner do Azure Cosmos DB após a conclusão da função. Os atributos específicos indicam o nome do contêiner e o nome do respectivo banco de dados pai. A cadeia de conexão de sua conta do Azure Cosmos DB será definida pelo `ConnectionStringSettingAttribute`.

A definição do método Executar deve agora se parecer com o seguinte:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Atributos de associação são definidos diretamente no arquivo function.json. Dependendo do tipo de associação, outras propriedades podem ser necessárias. A [configuração de saída do Azure Cosmos DB](./functions-bindings-cosmosdb-v2-output.md#configuration) descreve os campos necessários para executar uma associação de saída do Azure Cosmos DB. A extensão facilita a inclusão de associações no arquivo function.json. 

Para criar uma associação, clique com o botão direito do mouse (Ctrl+clique no macOS) no arquivo `function.json` na pasta HttpTrigger e escolha **Adicionar associação...** . Siga os prompts para definir as seguintes propriedades de associação para a nova associação:

| Prompt | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecionar direção de associação** | `out` | A associação é uma associação de saída. |
| **Selecionar associação com direção de saída** | `Azure Cosmos DB` | A associação é uma associação do Azure Cosmos DB. |
| **O nome usado para identificar essa associação em seu código** | `outputDocument` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **O banco de dados do Cosmos DB em que os dados serão gravados** | `my-database` | O nome do banco de dados do Azure Cosmos DB em que o contêiner de destino está localizado. |
| **A coleção de bancos de dados em que os dados serão gravados** | `my-container` | O nome do contêiner do Azure Cosmos DB em que os documentos JSON serão gravados. |
| **Se for true, cria o banco de dados e a coleção do Cosmos DB** | `false` | O banco de dados e o contêiner de destino já existem. |
| **Selecione a configuração de "local.setting.json"** | `CosmosDbConnectionString` | O nome de uma configuração de aplicativo que contém a cadeia de conexão da conta do Azure Cosmos DB. |
| **Chave de partição (opcional)** | *deixar em branco* | Será necessário somente quando a associação de saída criar o contêiner. |
| **Produtividade da coleção (opcional)** | *deixar em branco* | Será necessário somente quando a associação de saída criar o contêiner. |

Uma associação é incluída na matriz `bindings` no seu function.json, que deve ter a seguinte aparência:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

::: zone pivot="programming-language-csharp"  

Adicione o código que usa o objeto de associação de saída `documentsOut` para criar um documento JSON. Adicione esse código antes do método retornar.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

Neste ponto, sua função deve ser a seguinte:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Adicione o código que usa o objeto de associação de saída `outputDocument` em `context.bindings` para criar um documento JSON. Adicione esse código antes da instrução `context.res`.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

Neste ponto, sua função deve ser a seguinte:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Como no artigo anterior, clique em <kbd>F5</kbd> para iniciar o projeto do aplicativo de funções e o Core Tools. 

1. Com o Core Tools em execução, acesse a área **Azure: Funções**. Em **Funções**, expanda **Projeto Local** > **Funções**. Clique com o botão direito do mouse (CTRL-clique no Mac) na função `HttpExample` e escolha **Executar Função Agora...** .

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Executar a função agora por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`. Clique em ENTER para enviar essa mensagem de solicitação à função.  
 
1. Depois que uma resposta for retornada, clique em <kbd>CTRL + C</kbd> para interromper o Core Tools.

### <a name="verify-that-a-json-document-has-been-created"></a>Verificar se o documento JSON foi criado

1. No portal do Azure, navegue de volta para sua conta do Azure Cosmos DB e selecione **Data Explorer**.

1. Expanda o banco de dados e o contêiner, depois selecione **Itens** para listar os documentos criados em seu contêiner.

1. Verifique se um documento JSON foi criado pela associação de saída.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Como verificar se um documento foi criado no contêiner do Azure Cosmos DB" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Reimplementar e verificar o aplicativo atualizado

1. No Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione `Azure Functions: Deploy to function app...`.

1. Escolha o aplicativo de funções que você criou no primeiro artigo. Como você está reimplantando seu projeto no mesmo aplicativo, selecione **Implantar** para descartar o aviso de substituição de arquivos.

1. Após a conclusão da implantação, use novamente o recurso **Executar Função Agora...** para disparar a função no Azure.

1. Clique mais uma vez em [Verificar os documentos criados no contêiner do Azure Cosmos DB](#verify-that-a-json-document-has-been-created) para verificar se a associação de saída vai gerar um documento JSON novamente.

## <a name="clean-up-resources"></a>Limpar os recursos

No Azure, os *Recursos* se referem a aplicativos de funções, funções, contas de armazenamento e assim por diante. Eles são agrupados em *grupos de recursos* e você pode excluir tudo junto ao excluir o grupo.

Você criou recursos para concluir esses guias de início rápido. Você pode ser cobrado por esses recursos, dependendo do [status de conta](https://azure.microsoft.com/account/) e [preços do serviço](https://azure.microsoft.com/pricing/). Caso não precise mais dos recursos, é possível excluí-los desta maneira:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Próximas etapas

Você atualizou uma função disparada por HTTP para gravar documentos JSON em um contêiner do Azure Cosmos DB. Agora você pode saber mais sobre o desenvolvimento no Functions usando o Visual Studio Code:

+ [Desenvolver no Azure Functions usando o Visual Studio Code](functions-develop-vs-code.md)

+ [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos de função completos em C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completos em JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guia do desenvolvedor de JavaScript do Azure Functions](functions-reference-node.md)  
::: zone-end  