---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 957009ec9833df2dd0f89c3745c7f3df5a3705b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403465"
---
A tabela a seguir mostra as associações que são suportadas nas duas versões principais do tempo de execução do Azure Functions.

| Type | 1.x | 2.x<sup>1</sup> | Gatilho | Entrada | Saída |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Armazenamento de Blobs](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Grade de Eventos](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Hubs de Evento](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP & Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Tabelas do Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>Arquivos do OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Email do Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>Eventos](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Tokens de autenticação](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Aplicativos Móveis](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Hubs de Notificação](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Armazenamento de filas](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Barramento de Serviço](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Armazenamento de tabelas](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> Em 2.x, todas as associações, exceto HTTP e Timer, precisam ser registradas. Confira [Registrar as extensões de associação](../articles/azure-functions/functions-bindings-register.md).
