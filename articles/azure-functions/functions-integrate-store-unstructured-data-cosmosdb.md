---
title: Armazenar dados não estruturados usando o Azure Cosmos DB e Functions
description: Armazenar dados não estruturados usando o Azure Functions e o Cosmos DB
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91661152"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Armazenar dados não estruturados usando o Azure Functions e o Azure Cosmos DB

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é uma ótima maneira de armazenar dados não estruturados e JSON. Combinado com o Azure Functions, o Cosmos DB torna o armazenamento de dados rápido e fácil com muito menos código do que o necessário para armazenar dados em um banco de dados relacional.

> [!NOTE]
> Neste momento, o gatilho, as associações de entrada e as associações de saída do Azure Cosmos DB funcionam apenas com contas da API do Graph e a API do SQL.

No Azure Functions, associações de entrada e saída fornecem uma maneira declarativa para se conectar a dados de serviço externo de sua função. Neste artigo, saiba como atualizar uma função existente a fim de adicionar uma associação de saída que armazena dados não estruturados em um documento do Azure Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Antes de criar a associação de saída, você precisa ter uma conta do Azure Cosmos DB que use a API de SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

1. No portal do Azure, navegue até o aplicativo de funções que você criou anteriormente e selecione-o.

1. Selecione **Funções** e a função HttpTrigger.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Selecionar a função Http no portal do Azure." border="true":::

1. Selecione **Integração** e **+ Adicionar saída**.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Adicionar uma associação de saída do Azure Cosmos DB." border="true":::

1. Use a configuração **Criar Saída** conforme especificado na tabela:

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Configurar associação de saída do Azure Cosmos DB." border="true":::

    | Configuração      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Tipo de Associação** | Azure Cosmos DB | Nome do tipo de associação a ser selecionado para criar a associação de saída ao Azure Cosmos DB. |
    | **Nome do parâmetro do documento** | taskDocument | Nome que se refere ao objeto do Cosmos DB no código. |
    | **Nome do banco de dados** | taskDatabase | Nome do banco de dados para salvar os documentos. |
    | **Nome da coleção** | taskCollection | Nome da coleção de banco de dados. |
    | **Se for true, cria o banco de dados e a coleção do Cosmos DB** | Sim | A coleção ainda não existe, então crie uma. |
    | **Conexão da conta do Cosmos DB** | Nova configuração | Selecione **Novo** e depois escolha sua **Conta do Azure Cosmos DB** e a **Conta de banco de dados** criada anteriormente e, em seguida, selecione **OK**. Isso cria uma configuração de aplicativo para sua conexão de conta. Essa configuração é usada pela associação para conexão com o banco de dados. |

1. Selecione **OK** para criar a associação.

## <a name="update-the-function-code"></a>Atualizar o código de função

Substitua o código existente da função pelo código a seguir na linguagem que você escolheu:

# <a name="c"></a>[C#](#tab/csharp)

Substitua função de C# existente por este código:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Substitua função de JavaScript existente por este código:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

Esse exemplo de código lê as cadeias de consulta da Solicitação HTTP e as atribui a campos no objeto `taskDocument`. A associação `taskDocument` envia os dados do objeto desse parâmetro de associação para armazenamento no banco de dados de documento associado. O banco de dados é criado na primeira execução da função.

## <a name="test-the-function-and-database"></a>Testar a função e o banco de dados

1. Selecione **Testar/Executar**. Em **Consulta**, selecione **+ Adicionar parâmetro** e adicione os seguintes parâmetros à cadeia de caracteres de consulta:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="Testar a função." border="true":::


1. Selecione **Executar** e verifique se um status 200 é retornado.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="Captura de tela que mostra o status do código de resposta HTTP 200 realçado após a seleção da execução." border="true":::


1. No portal do Azure, pesquise e selecione **Azure Cosmos DB**.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Pesquisar o serviço do Cosmos DB." border="true":::

1. Escolha sua conta do Azure Cosmos DB e selecione o **Data Explorer**.

1. Expanda os nós **TaskCollection**, selecione o novo documento e confirme se o documento contém os valores de cadeia de caracteres de consulta, juntamente com alguns metadados adicionais.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="Verificar os valores de cadeia de caracteres em seu documento." border="true":::

Você adicionou com êxito uma associação ao gatilho HTTP para armazenar dados não estruturados em um Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a associação a um banco de dados Cosmos DB, veja [Associações do Azure Functions Cosmos DB](functions-bindings-cosmosdb.md).

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
