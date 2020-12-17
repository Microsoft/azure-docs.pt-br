---
title: Associações de RabbitMQ do Azure para Azure Functions
description: Saiba como enviar gatilhos e associações do Azure RabbitMQ no Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/15/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 72c31d0eebc19a926805976ed637168a00fa9495
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617180"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>RabbitMQ associações para Azure Functions visão geral

> [!NOTE]
> As associações RabbitMQ têm suporte total apenas nos planos do **Windows Premium** . Não há suporte para consumo e para Linux no momento.

Azure Functions integra-se com o [RabbitMQ](https://www.rabbitmq.com/) por meio de [gatilhos e associações](./functions-triggers-bindings.md). As extensões de associação do Azure Functions RabbitMQ permitem que você envie e receba mensagens usando a API RabbitMQ com funções.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando uma mensagem RabbitMQ chegar à fila | [Gatilho](./functions-bindings-rabbitmq-trigger.md) |
| Enviar mensagens RabbitMQ |[Associação de saída](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

Para começar a desenvolver com essa extensão, verifique se você [configurou primeiro um ponto de extremidade RabbitMQ](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Para saber mais sobre o RabbitMQ, confira sua [página de introdução](https://www.rabbitmq.com/getstarted.html).

### <a name="functions-3x-and-higher"></a>Funções 3. x e superior

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 4. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure] é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualizar suas extensões]: ./functions-bindings-register.md
[Extensão de ferramentas do Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Funções 1. x e 2. x

As extensões de associação RabbitMQ não têm suporte para as funções 1. x e 2. x. Use o Functions 3. x e superior.

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função quando uma mensagem RabbitMQ é criada (gatilho)](./functions-bindings-rabbitmq-trigger.md)
- [Enviar mensagens RabbitMQ de Azure Functions (Associação de saída)](./functions-bindings-rabbitmq-output.md)