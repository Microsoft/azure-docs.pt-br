---
title: Azure Cosmos DB associações para as funções 2. xD e superior
description: Entenda como usar gatilhos e associações do Azure Cosmos DB no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106919"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Cosmos DB gatilho e associações para Azure Functions 2. x e visão geral mais alta

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2 e superior](functions-bindings-cosmosdb-v2.md)

Este conjunto de artigos explica como trabalhar com associações de [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) no Azure Functions 2. x e superior. O Azure Functions dá suporte a associações de gatilho, entrada e saída para o Azure Cosmos DB.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando um documento Azure Cosmos DB for criado ou modificado | [Gatilho](./functions-bindings-cosmosdb-v2-trigger.md) |
| Ler um documento de Azure Cosmos DB | [Associação de entrada](./functions-bindings-cosmosdb-v2-input.md) |
| Salvar alterações em um documento Azure Cosmos DB  |[Associação de saída](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Essa referência é para [Azure Functions versão 2. x e superior](functions-versions.md).  Para obter informações sobre como usar essas associações em Functions 1. x, consulte [Associações do Azure Cosmos DB para Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Essa associação era originalmente denominada DocumentDB. Nas funções versão 2. x e superior, o gatilho, as associações e o pacote são todos nomeados Cosmos DB.

## <a name="supported-apis"></a>APIs com suporte

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Language                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure] é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualizar suas extensões]: ./functions-bindings-register.md
[Extensão de ferramentas do Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos do Functions 1. x têm automaticamente uma referência ao pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x.

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função quando um documento de Azure Cosmos DB é criado ou modificado (gatilho)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Ler um documento de Azure Cosmos DB (Associação de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Salvar alterações em um documento de Azure Cosmos DB (Associação de saída)](./functions-bindings-cosmosdb-v2-output.md)