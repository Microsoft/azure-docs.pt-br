---
title: Azure Functions tratamento de erros e diretrizes de repetição
description: Saiba como tratar erros e eventos de repetição no Azure Functions com links para erros de associação específicos.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93284460"
---
# <a name="azure-functions-error-handling-and-retries"></a>Azure Functions tratamento e novas tentativas de erro

O tratamento de erros no Azure Functions é importante para evitar perda de dados, eventos perdidos e para monitorar a integridade do aplicativo.

Este artigo descreve estratégias gerais para tratamento de erros junto com links para erros específicos de associação.

## <a name="handling-errors"></a>Tratando erros

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>Códigos de erro de associação

Ao integrar com os serviços do Azure, os erros podem se originar das APIs dos serviços subjacentes. Informações relacionadas a erros específicos de associação estão disponíveis na seção **exceções e códigos de retorno** dos seguintes artigos:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de Blobs](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hubs de Evento](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Hubs IoT](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Barramento de Serviço](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
