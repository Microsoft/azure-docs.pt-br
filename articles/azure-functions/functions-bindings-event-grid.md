---
title: Associações da grade de eventos do Azure para Azure Functions
description: Entenda como manipular a Grade de Eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104539"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Associações da grade de eventos do Azure para Azure Functions

Esta referência explica como manipular eventos de [grade de eventos](../event-grid/overview.md) no Azure functions. Para obter detalhes sobre como lidar com mensagens de grade de eventos em um ponto de extremidade HTTP, consulte [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md).

A Grade de Eventos é um serviço do Azure que envia solicitações HTTP para notificá-lo sobre eventos que acontecem nos *publicadores*. Um publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de Blobs do Azure é um publicador, e [uma exclusão ou upload de blob é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [serviços do Azure têm suporte interno para publicar eventos na Grade de Eventos](../event-grid/overview.md#event-sources).

Os *manipuladores* de eventos recebem e processam eventos. O Azure Functions é um dos vários serviços do[Azure que possuem suporte interno para manipular eventos da Grande de Eventos](../event-grid/overview.md#event-handlers). Nessa referência, você aprende a usar um gatilho de grade de eventos para invocar uma função quando um evento é recebido da grade de eventos e para usar a associação de saída para enviar eventos para um [tópico personalizado da grade de eventos](../event-grid/post-to-custom-topic.md).

Se preferir, você pode usar um gatilho HTTP para manipular eventos de grade de eventos; consulte [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md). No momento, você não pode usar um gatilho de grade de eventos para um aplicativo Azure Functions quando o evento é entregue no [esquema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Em vez disso, use um gatilho HTTP.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando um evento de grade de eventos for despachado | [Gatilho](./functions-bindings-event-grid-trigger.md) |
| Envia um evento de grade de eventos |[Associação de saída](./functions-bindings-event-grid-output.md) |

O código nessa referência usa como padrão a sintaxe do .NET Core, usada nas funções versão 2. x e superior. Para obter informações sobre a sintaxe 1.x, consulte os [modelos de funções 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Language                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 2. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Atualizar suas extensões]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos do Functions 1. x têm automaticamente uma referência ao pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x.

## <a name="next-steps"></a>Próximas etapas
* [Executar uma função quando um evento de grade de eventos for despachado](./functions-bindings-event-grid-trigger.md)
* [Enviar um evento de Grade de Eventos](./functions-bindings-event-grid-trigger.md)