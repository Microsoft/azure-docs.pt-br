---
title: Diretrizes de tratamento de erros de funções do Azure Functions | Microsoft Docs
description: Fornece diretrizes gerais para tratamento de erros que ocorrem quando as funções são executadas e links para tópicos de erros específicos da associação.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: ef8f2d5a63f7924097362f6aa0ebc78cc0f6455f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480703"
---
# <a name="azure-functions-error-handling"></a>Tratamento de erros do Azure Functions

Este tópico fornece diretrizes gerais para tratamento de erros que ocorrem quando as funções são executadas. Ele também fornece links para tópicos que descrevem os erros específicos da associação que podem ocorrer. 

## <a name="handing-errors-in-functions"></a>Tratamento de erros em funções
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Códigos de erro de associação

Ao se integrar com os serviços do Azure, você pode ter erros gerados com origem nas APIs dos serviços subjacentes. Links para a documentação do código de erro para esses serviços podem ser encontrados na seção **Exceções e códigos de retorno** dos seguintes tópicos de gatilho e referência da associação:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de Blobs](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hubs de Evento](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Barramento de Serviço](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table.md#exceptions-and-return-codes)
